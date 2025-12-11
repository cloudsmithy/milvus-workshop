# Requirements Document

## Introduction

本功能旨在通过 GitHub Actions 自动将仓库中的 Jupyter Notebook 文件转换为 HTML 格式，并发布为一个可浏览的网页版 Runbook。Runbook 将包含目录导航，支持中英文双语内容，并托管在 GitHub Pages 上。

## Glossary

- **Runbook**: 一个结构化的网页文档集合，用于展示教程、操作指南或技术文档
- **Notebook**: Jupyter Notebook 文件（.ipynb 格式），包含代码、文本和输出的交互式文档
- **GitHub Actions**: GitHub 提供的 CI/CD 自动化工具
- **GitHub Pages**: GitHub 提供的静态网站托管服务
- **nbconvert**: Jupyter 官方的 notebook 转换工具

## Requirements

### Requirement 1

**User Story:** As a 文档维护者, I want to 自动将 notebooks 转换为 HTML, so that 我不需要手动执行转换操作。

#### Acceptance Criteria

1. WHEN 代码推送到主分支 THEN the GitHub_Actions SHALL 触发自动构建流程
2. WHEN 构建流程执行 THEN the nbconvert SHALL 将所有 .ipynb 文件转换为 HTML 格式
3. WHEN 转换完成 THEN the GitHub_Actions SHALL 保留原有的目录结构（ch1, ch2, ch3, ch4）

### Requirement 2

**User Story:** As a 读者, I want to 通过目录页导航到各个章节, so that 我可以快速找到需要的内容。

#### Acceptance Criteria

1. WHEN 访问 Runbook 首页 THEN the 系统 SHALL 显示包含所有章节链接的目录页
2. WHEN 目录页生成 THEN the 系统 SHALL 按章节分组显示所有 notebook 链接
3. WHEN 点击目录链接 THEN the 系统 SHALL 导航到对应的 HTML 页面

### Requirement 3

**User Story:** As a 读者, I want to 区分中英文版本的内容, so that 我可以选择适合的语言阅读。

#### Acceptance Criteria

1. WHEN 目录页显示 notebook 列表 THEN the 系统 SHALL 明确标识中文版和英文版（_en 后缀）
2. WHEN 生成 HTML 页面 THEN the 系统 SHALL 在页面标题中包含语言标识

### Requirement 4

**User Story:** As a 文档维护者, I want to 将生成的 HTML 发布到 gh-pages 分支, so that 可以通过 GitHub Pages 访问。

#### Acceptance Criteria

1. WHEN HTML 生成完成 THEN the GitHub_Actions SHALL 将所有 HTML 文件推送到 gh-pages 分支
2. WHEN 推送到 gh-pages 分支 THEN the GitHub_Pages SHALL 自动部署并提供访问 URL
3. WHEN 部署完成 THEN the 系统 SHALL 保持之前的部署历史可追溯

### Requirement 5

**User Story:** As a 读者, I want to Runbook 页面有良好的样式, so that 阅读体验更好。

#### Acceptance Criteria

1. WHEN HTML 页面生成 THEN the 系统 SHALL 应用统一的 CSS 样式
2. WHEN 页面渲染 THEN the 系统 SHALL 正确显示代码高亮
3. WHEN 页面渲染 THEN the 系统 SHALL 正确显示 notebook 中的图片和输出结果
