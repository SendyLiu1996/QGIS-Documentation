# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

这是 QGIS 官方文档仓库，用于编写和维护 QGIS（一个免费开源的地理信息系统软件）的官方文档。该仓库使用 Sphinx 文档系统构建多语言文档，支持 HTML、PDF 等多种输出格式。

## 核心构建命令

### 开发环境设置
```bash
# 创建并激活 Python 虚拟环境
python3 -m venv venv
source ./venv/bin/activate  # Linux/macOS
# 或 
venv\Scripts\activate.bat   # Windows

# 安装依赖
pip install -r REQUIREMENTS.txt
```

### 主要构建命令
```bash
# 构建英文 HTML 文档（最常用）
make html

# 构建特定语言文档（例如中文）
make LANG=zh_Hans html

# 构建 PDF（仅 Linux，需要额外依赖）
make pdf
make LANG=zh_Hans pdf

# 快速环境设置和构建（推荐）
make -f venv.mk html

# 清理构建文件
make springclean

# 完全清理（包括虚拟环境）
make -f venv.mk cleanall
```

### Windows 特殊命令
```cmd
# Windows 构建
make.bat html

# Windows 多语言构建
set SPHINXOPTS=-D language=zh_Hans
make.bat html
```

### 测试命令
```bash
# 测试 Python 代码片段（需要 QGIS 安装）
make doctest

# 使用 Docker 测试
make -f docker.mk doctest

# 测试单个文件
SINGLE_TEST=filename.rst make doctest

# 或使用脚本测试所有包含测试代码的文件
./run_single_test.sh

# 链接检查
make linkcheck
```

## 架构概述

### 文档结构
- `docs/` - 主要文档内容
  - `user_manual/` - 用户手册，面向 QGIS 最终用户
  - `pyqgis_developer_cookbook/` - PyQGIS 开发者指南，包含 Python API 示例
  - `server_manual/` - QGIS Server 配置和使用指南
  - `training_manual/` - 培训教程，适合课堂教学
  - `gentle_gis_introduction/` - GIS 入门介绍
  - `developers_guide/` - 开发者指南（QGIS 核心开发）
  - `documentation_guidelines/` - 文档编写规范

### 关键配置文件
- `conf.py` - Sphinx 主配置文件，包含构建设置和扩展配置
- `Makefile` - Unix/Linux 构建脚本
- `make.bat` - Windows 构建脚本
- `venv.mk` - 自动化虚拟环境管理的 Makefile
- `docker.mk` - Docker 构建环境
- `REQUIREMENTS.txt` - Python 依赖列表

### 多语言支持
- `locale/` - 翻译文件目录
- `transifex.yml` - Transifex 翻译平台配置
- 使用 Transifex 进行协作翻译
- 支持语言：en, cs, de, es, fr, hu, it, ja, ko, lt, nl, pl, pt_BR, pt_PT, ro, ru, sv, zh_Hans

### 静态资源
- `static/common/` - 通用图标、图片资源
- `qgis-projects/` - 示例 QGIS 项目文件
- `themes/rtd_qgis/` - 自定义主题

## 开发工作流程

### 文档编辑
1. 所有文档使用 reStructuredText (.rst) 格式
2. 图片应放置在对应文档目录的 `img/` 子目录中
3. 使用相对路径引用图片和其他资源
4. 遵循现有的文档结构和命名约定

### 代码示例测试
PyQGIS 代码示例使用 Sphinx doctest 扩展：
- 使用 `.. testcode::` 指令标记可测试代码
- 使用 `.. testsetup::` 指令准备测试环境
- 测试需要 QGIS 运行环境

### 翻译工作流程
- 英文是源语言，其他语言通过 Transifex 翻译
- 翻译文件自动同步到对应的发布分支
- 本地测试翻译：使用 `./scripts/minimize_translation.sh` 脚本

## 重要脚本

### 实用脚本（scripts/ 目录）
- `minimize_translation.sh` - 从 Transifex 拉取翻译文件
- `create_transifex_resources.sh` - 创建 Transifex 资源配置
- `populate_expressions_list.py` - 更新表达式函数列表
- `find_set_subst.py` - 管理文档替换变量
- `start_qgis.sh` / `start_qgis_ltr.sh` - 启动 QGIS 进行测试

## 构建系统详细说明

### Sphinx 配置要点
- 使用自定义主题 `rtd_qgis`
- 支持多种输出格式：HTML、PDF、EPUB
- 启用国际化 (i18n) 支持
- 集成 doctest 进行代码测试
- 使用多个 Sphinx 扩展增强功能

### 环境要求
- Python >= 3.9
- Sphinx 7.4.6
- 对于 PDF 构建：XeLaTeX 编译器和相关字体包

### 性能优化
- 使用 `venv.mk` 进行增量构建
- 利用 Sphinx 缓存机制
- 并行处理翻译文件

## 注意事项

### 文档编写
- 遵循现有的 reStructuredText 样式
- 确保所有链接和引用正确
- 图片使用合适的格式和大小
- 代码示例必须可测试和可运行

### 多语言支持
- 不要直接编辑翻译文件，使用 Transifex
- 测试多语言构建确保兼容性
- 注意不同语言的特殊字符和排版

### 版本管理
- 主分支 (`master`) 用于开发版本文档
- 发布分支 (`release_X.Y`) 包含对应版本的稳定文档
- 翻译仅在 LTR (长期支持) 版本上进行