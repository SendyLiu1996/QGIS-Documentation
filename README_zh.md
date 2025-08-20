<img src="https://github.com/qgis/QGIS/blob/master/images/README-md/main_logo.png" width="300" alt="QGIS 完整 Logo">

[![文档构建](https://github.com/qgis/QGIS-Documentation/actions/workflows/builds.yml/badge.svg?branch=master&event=push)](https://github.com/qgis/QGIS-Documentation/actions/workflows/builds.yml)
[![文档测试构建](https://github.com/qgis/QGIS-Documentation/actions/workflows/doctest.yml/badge.svg?branch=master&event=push)](https://github.com/qgis/QGIS-Documentation/actions/workflows/doctest.yml)
[![阅读文档](https://img.shields.io/badge/Read-the%20docs-green.svg)](https://docs.qgis.org/testing/)

本仓库用于编写和管理 [QGIS](https://qgis.org) 的官方文档，QGIS 是一个免费开源的地理信息系统 (GIS) 软件，其源代码可在 [此仓库](https://github.com/qgis/QGIS) 中找到。QGIS 是 [开源地理空间基金会 (OSGeo)](https://www.osgeo.org) 的一部分，提供一系列互补的开源 GIS 软件项目。

QGIS 的最新文档可在 <https://docs.qgis.org/latest> 获取

* [构建文档](#构建文档)
   * [在 Linux 或 macOS 上构建](#在-linux-或-macos-上构建)
     * [修复 macOS 上的构建区域设置错误](#修复-macos-上的构建区域设置错误)
   * [在 Windows 上构建](#在-windows-上构建)
   * [构建 PDF](#构建-pdf)
* [翻译](#翻译)
  * [GitHub 工作流程](#github-工作流程)
  * [本地管理翻译](#本地管理翻译)
* [测试 Python 代码片段](#测试-python-代码片段)

# 构建文档

1. 如果您的操作系统没有提供，您需要安装：

   - [git](https://git-scm.com/download/)
   - 和 [Python](https://www.python.org/downloads/) (>=3.9)

   您可以将两者安装在默认位置并使用默认选项。
1. [克隆仓库](https://help.github.com/zh/github/creating-cloning-and-archiving-repositories/cloning-a-repository)
1. 进入该目录并根据您的操作系统遵循下面的说明。

构建文档的最佳方式是在 Python 虚拟环境 (venv) 中进行。

## 在 Linux 或 macOS 上构建

您可以通过首先创建来使用自己的虚拟环境：

```sh
# 您需要 python >=3.9。根据发行版，使用 `python3` 或 `python`
# 常见名称是 'venv'，但您可以随意命名

python3 -m venv venv  # 使用 venv 模块，创建一个名为 'venv' 的虚拟环境
```

然后激活虚拟环境：

```sh
source ./venv/bin/activate
```

激活虚拟环境后，您应该在提示符中看到 'venv'。通过 REQUIREMENTS.txt 安装依赖：

```sh
pip install -r REQUIREMENTS.txt
```

并在该虚拟环境中运行构建：

```sh
make html
```

想要构建您自己的语言？注意您将使用来自 git 的 po 文件的翻译！例如对于中文：

```sh
make LANG=zh_Hans html
```

<details>
  <summary>技巧：一行命令创建虚拟环境并运行构建</summary>

  `venv.mk` 文件将在当前目录/venv 中创建/更新虚拟环境（如果不可用）并在其中运行 html 构建。

  ```sh
  make -f venv.mk html
  ```
</details>

如果出于某种原因，您想要从头重新开始：

```sh
make -f venv.mk cleanall
```

### 修复 macOS 上的构建区域设置错误

当在 macOS 上构建失败并出现以下错误时：
  
```sh
locale.Error: unsupported locale setting
make: *** [html] Error 1
```

解决方案是使用文本编辑器（pico、textEdit、vscode...）编辑您的 `venv` 文件夹中的 Python 文件 `build.py`。
该文件存储在 `./venv/lib/python3.10/site-packages/sphinx/cmd/build.py`
（将 `python3.10` 替换为您安装的 Python 版本）。

1. 在文本编辑器中打开文件
2. 搜索并替换：

```py
def main(argv: Sequence[str] = (), /) -> int:
    locale.setlocale(locale.LC_ALL, '')
```

为：

```py
def main(argv: Sequence[str] = (), /) -> int:
    locale.setlocale(locale.LC_ALL, 'en_US.UTF-8')
```

3. 保存并关闭文件，现在您应该能够构建文档：

```sh
make html
```

## 在 Windows 上构建

在该目录中创建一个名为 'venv' 的虚拟环境（在互联网上搜索 Windows 上的 Python 虚拟环境以获得更多详细信息），但简而言之：使用模块 'venv' 创建一个名为 'venv' 的虚拟环境

```cmd
# 在 DOS 命令框中：
python -m venv venv
```

然后激活虚拟环境：

```cmd
venv\Scripts\activate.bat
```

激活虚拟环境后，您应该在提示符中看到 'venv'。通过 REQUIREMENTS.txt 安装依赖：

```cmd
pip install -r REQUIREMENTS.txt
```

并在该虚拟环境中运行构建，使用 make.bat 脚本和 html 参数来本地构建文档：

```cmd
make.bat html
```

想要构建您自己的语言？注意您将使用来自 git 的 po 文件的翻译！例如对于荷兰语：

```cmd
set SPHINXOPTS=-D language=nl
make.bat html
```

## 构建 PDF

在 Linux 中，您还可以构建主要文档的 PDF 版本。

```sh
make -f venv.mk pdf
```

或者在启用虚拟环境后：

```sh
make pdf
```

如果您想要构建英语以外语言的 PDF，可以使用类似的语法：

```sh
make LANG=fr pdf
```

要构建英文 PDF，您需要安装 XeLaTex 编译器包 `texlive-xetex` 和 GNU Freefont。

```sh
sudo apt install texlive-xetex fonts-freefont-otf
```

要构建翻译的 PDF，您可能需要为您的特定语言安装 texlive 额外包（例如 `texlive-lang-french`）。
对于日语，安装 `texlive-lang-japanese` 是至关重要的，它将安装 platex 编译器。
如果您计划构建所有语言，安装所有语言包（`texlive-lang-all`）可能更容易，但它将使用相当大的磁盘空间。

某些语言还需要安装特定字体：

-   韩语 (ko) - 来自 `fonts-nanum` 包的 NanumMyeongjo
-   印地语 (hi) - 来自 `fonts-nakula` 包的 Nakula

# 翻译

我们依赖 [Transifex 平台](https://www.transifex.com) 来存储和协调我们的翻译工作。
只有 [当前长期支持版本](https://docs.qgis.org/latest/) 正在被翻译。

要成为翻译团队的一部分，请遵循
[成为翻译者](https://www.qgis.org/en/site/getinvolved/translate.html#becoming-a-translator)。

## GitHub 工作流程

该过程使用 [Transifex - GitHub 集成系统](https://help.transifex.com/en/articles/6265125-github-via-transifex-ui) 和一些自定义脚本自动化：

* [transifex.yml](transifex.yml) 配置文件：
  提供检索英文源文件和定位翻译文件的方式。

  ---
   **给 Transifex 管理员的注意事项**

   如果在为新版本设置集成系统后，翻译字符串无法（完全）上传到 Transifex：

   1. 运行 [create_transifex_resources](scripts/create_transifex_resources.sh) 脚本：
      创建/更新 [.tx/config](.tx/config) 文件，其中包含 GitHub 仓库中英文源文件及其翻译的格式化引用，并将它们链接到 Transifex 中的资源。
   1. 强制推送翻译文件到 Transifex
      ```sh
       tx push -f -t --no-interactive
      ```
  ---

* Transifex 集成机器人： 
  管理字符串的拉取和推送，换句话说：
  - 跟踪 GitHub 中英文 `*.po` 资源文件的任何更改并自动将它们发送到 Transifex 平台
  - 当资源 100% 翻译完成时，自动将翻译的 `*.po` 文件发送回 GitHub 进行构建。
* [pofiles 操作](.github/workflows/pofiles.yml)：根据源 `*.rst` 文件的最新更改创建/更新英文 `*.po` 文件。为 Transifex 机器人提供数据。

* 对于在 Transifex 中尚未完全翻译的文件，[pull_minimized_translations 操作](.github/workflows/pull_minimized_translations.yml) 定期自动将它们拉取到仓库中。

基于以上，翻译字符串在发布分支中自动可用，因此可以按照前面部分的说明构建任何翻译语言的文档：
```sh
make html LANG=yourlanguage
```

## 本地管理翻译

虽然主分支仅提供英文版本，但我们将发布分支翻译为各种语言。
翻译字符串在相应的 `release_X.Y` 分支中可用（参见 `locale` 文件夹），因此您可以使用以下命令构建本地化的 HTML 文档：
```sh
make html LANG=yourlanguage
```

有时，您可能希望使用主仓库中尚未提供的非常新的翻译字符串（例如，来自部分翻译的文件）构建文档。
在这种情况下，您需要手动从 Transifex 拉取翻译到您的本地仓库：

1. 在 git 中本地检出仓库和目标分支
1. 准备环境
   ```sh
   python3 -m venv venv
   source ./venv/bin/activate
   pip install -r REQUIREMENTS.txt
   ```
1. 安装 [Transifex 命令行客户端](https://github.com/transifex/cli/)。
   在 Linux 或 macOS 上：
   ```sh
   curl -o- https://raw.githubusercontent.com/transifex/cli/master/install.sh | bash
   # 并重启终端
   ```

   另一种方式（包括在 Windows 上）是下载并本地解压最新的二进制文件。
   阅读 [更多说明](https://github.com/transifex/cli/?tab=readme-ov-file#download-from-github-releases-linuxmacwindows)。

1. 使用 [minimize_translation 脚本](scripts/minimize_translation.sh) 下载翻译字符串。
   默认情况下，这会拉取所有语言。
   ```sh
   ./scripts/minimize_translation.sh
   ```
   要拉取特定语言（例如意大利语），执行
   ```sh
   ./scripts/minimize_translation.sh -l it
   ```

   重要：要能够从 transifex.com 拉取，您需要一个凭据文件。
   该文件应命名为：`.transifexrc`，最简单的方法是将其放在您的主目录中。
   该文件应包含以下内容：
   ```
   [https://app.transifex.com]
   rest_hostname = https://rest.api.transifex.com
   token = yourtransifextoken
   ```
1. 构建您语言的文档
   ```sh
   make html LANG=yourlanguage
   ```
1. 通过开启拉取请求来分享更改，让我们为拉取的语言集成新字符串

# 测试 Python 代码片段

要测试 PyQGIS Cookbook 中的 Python 代码片段，您需要 *QGIS* 安装。
有许多选项：

* 您可以将系统 *QGIS* 安装与来自 Python 虚拟环境的 *Sphinx* 一起使用：

  ```sh
  make -f venv.mk doctest
  ```
* 您可以使用手动构建的 *QGIS* 安装。为此，您需要：
  1. 在 `venv.mk` 文件之上创建自定义 `Makefile` 扩展，例如包含以下内容的 `user.mk` 文件：

     ```sh
     # 根安装文件夹
     QGIS_PREFIX_PATH = /home/user/apps/qgis-master

     # 或构建输出文件夹
     QGIS_PREFIX_PATH = /home/user/dev/QGIS-build-master/output

     include venv.mk
     ```

  1. 然后使用它运行目标 `doctest`：

     ```sh
     make -f user.mk doctest
     ```
* 或者您可以在官方 *QGIS* docker 镜像中运行目标 `doctest`：

  ```sh
  make -f docker.mk doctest
  ```

注意，只有带有指令 `testcode` 的代码块会被测试，并且可以运行不会出现在文档中的测试设置代码，使用指令 `testsetup`，例如：

```py
 .. testsetup::

     from qgis.core import QgsCoordinateReferenceSystem

 .. testcode::

     # SRID 4326 分配给 WGS84
     crs = QgsCoordinateReferenceSystem("EPSG:4326")
     assert crs.isValid()
```

有关更多信息，请参阅 *Sphinx* doctest 扩展文档：
https://www.sphinx-doc.org/en/master/usage/extensions/doctest.html