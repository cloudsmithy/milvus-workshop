# Implementation Plan

- [ ] 1. 创建项目基础结构
  - [ ] 1.1 创建 scripts 目录和模板目录结构
    - 创建 `scripts/` 目录
    - 创建 `scripts/templates/` 目录
    - _Requirements: 1.2, 5.1_

  - [ ] 1.2 创建 CSS 样式文件
    - 创建 `scripts/templates/custom.css`
    - 包含代码高亮样式、响应式布局、导航样式
    - _Requirements: 5.1, 5.2_

- [ ] 2. 实现核心转换脚本
  - [ ] 2.1 实现 notebook 扫描功能
    - 创建 `scripts/build_runbook.py`
    - 实现 `scan_notebooks()` 函数，递归查找所有 .ipynb 文件
    - 实现 `NotebookInfo` 数据类
    - _Requirements: 1.2, 1.3_

  - [ ] 2.2 编写属性测试：转换完整性
    - **Property 1: Notebook 转换完整性**
    - **Validates: Requirements 1.2, 1.3**

  - [ ] 2.3 实现语言标识功能
    - 实现 `get_language_label()` 函数
    - 根据文件名 `_en` 后缀判断语言
    - _Requirements: 3.1, 3.2_

  - [ ] 2.4 编写属性测试：语言标识一致性
    - **Property 3: 语言标识一致性**
    - **Validates: Requirements 3.1, 3.2**

  - [ ] 2.5 实现 notebook 转换功能
    - 实现 `convert_notebook()` 函数
    - 使用 nbconvert 将 notebook 转换为 HTML
    - 应用自定义 CSS 样式
    - 处理图片资源路径
    - _Requirements: 1.2, 5.1, 5.3_

  - [ ] 2.6 编写属性测试：HTML 输出完整性
    - **Property 4: HTML 输出完整性**
    - **Validates: Requirements 5.1, 5.3**

- [ ] 3. 实现目录页生成
  - [ ] 3.1 创建目录页模板
    - 创建 `scripts/templates/index_template.html`
    - 包含章节分组布局、中英文标识、响应式设计
    - _Requirements: 2.1, 2.2, 3.1_

  - [ ] 3.2 实现目录生成功能
    - 实现 `generate_index()` 函数
    - 按章节分组 notebook 列表
    - 生成带有语言标识的链接
    - _Requirements: 2.1, 2.2, 2.3, 3.1_

  - [ ] 3.3 编写属性测试：目录生成正确性
    - **Property 2: 目录生成正确性**
    - **Validates: Requirements 2.2, 2.3**

- [ ] 4. 实现主程序入口
  - [ ] 4.1 实现 main 函数
    - 整合扫描、转换、目录生成流程
    - 添加命令行参数支持
    - 实现错误处理和日志输出
    - _Requirements: 1.2, 1.3_

  - [ ] 4.2 添加资源复制功能
    - 复制图片目录到输出目录
    - 复制 CSS 文件到输出目录
    - _Requirements: 5.1, 5.3_

- [ ] 5. Checkpoint - 确保本地构建正常
  - Ensure all tests pass, ask the user if questions arise.

- [ ] 6. 创建 GitHub Actions Workflow
  - [ ] 6.1 创建 workflow 文件
    - 创建 `.github/workflows/build-runbook.yml`
    - 配置 main 分支 push 触发
    - 设置 Python 环境和依赖安装
    - _Requirements: 1.1_

  - [ ] 6.2 配置构建和部署步骤
    - 添加运行转换脚本的步骤
    - 配置 gh-pages 分支部署
    - 设置 GitHub Pages 权限
    - _Requirements: 1.1, 4.1, 4.3_

- [ ] 7. 创建依赖配置文件
  - [ ] 7.1 创建 requirements.txt
    - 添加 nbconvert、jinja2 等依赖
    - _Requirements: 1.2_

- [ ] 8. Final Checkpoint - 确保所有测试通过
  - Ensure all tests pass, ask the user if questions arise.
