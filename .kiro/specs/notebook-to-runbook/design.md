# Design Document: Notebook to Runbook

## Overview

本设计实现一个自动化流程，将 Jupyter Notebook 文件转换为网页版 Runbook。系统通过 GitHub Actions 监听代码推送事件，使用 nbconvert 进行格式转换，生成带有目录导航的静态网站，并自动部署到 GitHub Pages。

## Architecture

```mermaid
flowchart LR
    A[Push to main] --> B[GitHub Actions]
    B --> C[Install Dependencies]
    C --> D[Scan Notebooks]
    D --> E[nbconvert to HTML]
    E --> F[Generate Index Page]
    F --> G[Copy Assets]
    G --> H[Deploy to gh-pages]
    H --> I[GitHub Pages]
```

### 核心流程

1. **触发**: 代码推送到 main 分支触发 workflow
2. **扫描**: 递归查找所有 `.ipynb` 文件
3. **转换**: 使用 nbconvert 将 notebook 转换为 HTML
4. **生成目录**: 创建 index.html 作为导航首页
5. **部署**: 将生成的文件推送到 gh-pages 分支

## Components and Interfaces

### 1. GitHub Actions Workflow (`.github/workflows/build-runbook.yml`)

主要职责：
- 监听 main 分支的 push 事件
- 设置 Python 环境和依赖
- 执行转换脚本
- 部署到 gh-pages 分支

### 2. 转换脚本 (`scripts/build_runbook.py`)

主要职责：
- 扫描项目中的所有 notebook 文件
- 调用 nbconvert 进行 HTML 转换
- 生成目录页 index.html
- 处理中英文版本标识
- 复制图片等静态资源

接口：
```python
def scan_notebooks(root_dir: str) -> List[NotebookInfo]
def convert_notebook(notebook_path: str, output_dir: str) -> str
def generate_index(notebooks: List[NotebookInfo], output_dir: str) -> None
def get_language_label(filename: str) -> str
```

### 3. 样式模板 (`scripts/templates/`)

- `custom.css`: 统一的页面样式
- `index_template.html`: 目录页模板

## Data Models

### NotebookInfo

```python
@dataclass
class NotebookInfo:
    path: str           # 原始文件路径，如 "ch1/ch1_1.ipynb"
    chapter: str        # 章节，如 "ch1"
    title: str          # 标题，从 notebook 或 README 提取
    language: str       # 语言标识："zh" 或 "en"
    html_path: str      # 生成的 HTML 相对路径
```

### ChapterInfo

```python
@dataclass
class ChapterInfo:
    name: str                       # 章节名，如 "ch1"
    display_name: str               # 显示名，如 "Part 1: Exploring Milvus"
    notebooks: List[NotebookInfo]   # 该章节下的所有 notebook
```

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system-essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

### Property 1: Notebook 转换完整性

*For any* notebook 文件集合，执行转换后，每个 `.ipynb` 文件都应该有对应的 `.html` 文件，且输出目录结构与输入目录结构一致。

**Validates: Requirements 1.2, 1.3**

### Property 2: 目录生成正确性

*For any* notebook 集合，生成的目录页应该包含所有 notebook 的链接，且链接按章节正确分组，每个链接指向存在的 HTML 文件。

**Validates: Requirements 2.2, 2.3**

### Property 3: 语言标识一致性

*For any* notebook 文件名，如果文件名包含 `_en` 后缀，则应标识为英文版；否则标识为中文版。此标识应在目录页和 HTML 页面标题中一致体现。

**Validates: Requirements 3.1, 3.2**

### Property 4: HTML 输出完整性

*For any* 生成的 HTML 文件，应该包含统一的 CSS 样式引用，且 notebook 中引用的图片路径应该正确转换为相对路径。

**Validates: Requirements 5.1, 5.3**

## Error Handling

| 错误场景 | 处理方式 |
|---------|---------|
| Notebook 文件损坏 | 记录错误日志，跳过该文件，继续处理其他文件 |
| nbconvert 转换失败 | 记录详细错误信息，workflow 标记为失败 |
| 图片资源缺失 | 记录警告，生成的 HTML 中保留原始引用 |
| gh-pages 推送失败 | 重试一次，失败则标记 workflow 失败 |

## Testing Strategy

### 单元测试

使用 pytest 进行单元测试：

- `test_scan_notebooks`: 测试 notebook 扫描功能
- `test_get_language_label`: 测试语言标识识别
- `test_generate_index`: 测试目录生成逻辑

### Property-Based Testing

使用 Hypothesis 库进行属性测试：

- 每个属性测试运行至少 100 次迭代
- 测试注释格式：`**Feature: notebook-to-runbook, Property {number}: {property_text}**`

测试覆盖的属性：
1. Property 1: 转换完整性 - 验证输入输出一一对应
2. Property 2: 目录正确性 - 验证链接有效性和分组
3. Property 3: 语言标识 - 验证 `_en` 后缀识别
4. Property 4: HTML 完整性 - 验证样式和资源引用

### 集成测试

- 在 CI 中运行完整的转换流程
- 验证生成的 HTML 文件可以正常访问
- 验证 GitHub Pages 部署成功
