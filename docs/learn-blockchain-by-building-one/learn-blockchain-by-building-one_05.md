# 1. 为应用开发做好准备

对于不熟悉的人来说，Python 是最流行的语言之一。它被广泛使用于各个领域——从学术界、科学领域到像 Instagram 这样的大型网络应用。其受欢迎的部分原因在于其拥有大量免费在线的库、包和扩展，以及由于其类似伪代码而易于阅读。

在本章中，我们将确保你的计算机已为应用开发正确配置，并且 Python 已正确安装。然后，我将向你展示如何创建一个实用的 Python 项目以及如何安装依赖项。

## Python 版本

Python 有两个版本：版本 2 和版本 3。版本 2 已不再受 Python 软件基金会支持，但由于许多内部工具仍在使用它，因此大多数操作系统仍预装了它。另一个复杂因素是，不同的操作系统将 Python 安装在文件系统的不同位置。这些因素使得设置开发环境变得棘手。

我们将尝试通过安装和使用有助于管理 Python 安装的工具来克服这些障碍。

> **注意**  
> 按照惯例，在本书中，我们将在终端命令前加上 `$` 符号。输出将以纯文本形式显示。

## 安装 Python

### Windows 系统安装

`Python.org` 包含适用于 Windows 的可下载二进制文件。前往 [`www.python.org/downloads/windows/`](http://www.python.org/downloads/windows/) 下载 Python 3.8 的二进制文件。

下载完成后，安装 Python 3.8，确保选择以下选项：

-   卸载 Python 的先前版本。
-   安装 `pip`（Python 包管理器）。
-   将 Python 添加到 `PATH` 环境变量（允许你在命令行中执行 Python）。

安装完成后，为了确认一切设置正确，打开命令行并检查 Python 版本：

```
C:\Users\dan> python --version
3.8.3
```


好的，作为一名高级文档工程师和翻译员，我将遵循您提供的注意事项和示例，将给定的英文文本翻译成中文。


### macOS 安装

尽管 macOS 自带了一个内部使用的 Python 版本，但我们在开发时并不想修改它，因此我们将使用 Homebrew——一个用于在 macOS 上帮助管理和安装第三方包的工具——来安装一个全新的 Python 版本。

首先，我们需要确保安装 Apple 的命令行工具，请在终端中执行：

```
$ xcode-select --install
```

你需要安装 *Homebrew*，这是一个 macOS 上的包安装器。要安装它，请遵循 [`https://brew.sh/`](https://brew.sh/) 上的说明，并确保 Homebrew 已正确安装。

安装完 Homebrew 后，让我们安装最新版本的 Python：

```
$ brew install python
```

安装完成后，请验证 Python 是否已正确安装：

```
$ python --version
Python 3.8.3
```

### Linux 安装

如果你使用的是基于 Debian 的 Linux 版本，你可以使用 `apt`（或任何其他包管理器）来安装 Python 3.8：

```
$ sudo apt-get update
$ sudo apt-get install python3.8
```

安装完成后，请验证 Python 是否已正确安装：

```
$ python --version
Python 3.8.3
```

如果你使用的不是基于 Debian 的 Linux 发行版，你可以从源代码编译 Python：[`www.python.org/downloads/source/`](http://www.python.org/downloads/source/)。

## Python 程序如何运行

当你安装 Python 时，你实际上是在安装一个*解释器*——一个将你编写的 Python 代码翻译成计算机能够理解和执行的指令的程序。你安装的解释器叫做 CPython，这是一个用 C 语言编写的流行解释器。

你通过在终端中将 Python 程序提供给 Python 解释器来运行它：

```
$ python my_program.py
```

这会将你的代码转换为“计算机指令”并执行它们。

你的操作系统如何知道 Python 解释器的位置？

你的操作系统有一个名为 `PATH` 的系统级变量，其中包含一个在查找程序时要遍历的文件路径列表。你可以通过在终端中运行 `echo $PATH` 来检查它的设置。Python 解释器位于 `/usr/local/bin/`。这可以通过调用 `which python` 来验证。

## 管理项目依赖

你构建的每个项目都可能使用到外部库。这些依赖可能是数据库访问库，或是解析文档或网站所需的工具，但重点是它们要被包含在你的项目中。

管理项目依赖可能是一项棘手的任务，因为不同的依赖有不同的要求——有些依赖需要特定版本的 Python，其他的可能依赖于兄弟依赖。现代 Python 项目使用包管理器来应对下载、安装和保持依赖更新的艰巨任务。总之，使用包管理器能让你的工作更轻松。

Poetry 是 Python 的众多依赖管理器之一。还有其他更流行的，比如 Pipenv。但在广泛使用了两者之后，我发现 Poetry 拥有更简洁的界面，并且其目标更加务实。

### 安装 Poetry

安装 Poetry 的推荐方法是在你的终端中运行以下命令：

```
$ curl -sSL https://raw.githubusercontent.com/sdispater/poetry/master/get-poetry.py | python
```

如果遇到任何问题，请参考 Poetry 网站上的官方文档和安装说明：[`https://poetry.eustace.io/docs/`](https://poetry.eustace.io/docs/)

通过在 shell 中调用 Poetry 来验证它是否已正确安装：

```
$ poetry
Poetry version 1.0.9
USAGE
poetry [-h] [-q] [-v []] [-V] [--ansi] [--no-ansi] [-n]  [] ... []
ARGUMENTS
               The  command  to  execute
                   The arguments of the command
GLOBAL OPTIONS
-h (--help)             Display this help message
-q (--quiet)            Do not output any message
-v (--verbose)          Increase the verbosity of messages: "-v" for normal output, "-vv" for more verbose output, and "-vvv" for debug
-V (--version)          Display this application version
--ansi                  Force ANSI output
--no-ansi               Disable ANSI output
-n (--no-interaction)   Do not ask any interactive question
AVAILABLE  COMMANDS
about                   Shows information about Poetry.
add                     Adds a new dependency to pyproject.toml.
build                   Builds a package, as a tarball and a wheel by default.
Cache                   Interacts with Poetry's cache.
check                   Checks the validity of the pyproject.toml file.
config                  Manages configuration settings.
debug                   Debugs various elements of Poetry.
env                     Interacts with Poetry's project environments.
export                  Exports the lock file to alternative formats.
help                    Displays the manual of a command.
init                    Creates a basic pyproject.toml file in the current directory.
install                 Installs the project dependencies.
lock                    Locks the project dependencies.
new                     Creates a new Python project at .
publish                 Publishes a package to a remote repository.
remove                  Removes a package from the project dependencies.
run                     Runs a command in the appropriate environment.
search                  Searches for packages on remote repositories.
self                    Interacts with Poetry directly.
shell                   Spawns a shell within the virtual environment.
show                    Shows information about packages.
update                  Updates the dependencies as according to the pyproject.toml file.
version                 Shows the version of the project or bumps it when a valid bump rule is provided .
```

### 使用 Poetry 创建一个 Python 项目

让我们用 Poetry 创建一个新项目：

```
poetry new my-project
```

这将在名为 `my-project` 的文件夹中创建以下“标准”的 Python 项目结构：

```
.
├── README.rst
├── my_project
│  └── __init__.py
├── pyproject.toml
└── tests
├── __init__.py
└── test_my_project.py
```

这里最重要的文件是 `pyproject.toml`，它描述了项目及其依赖关系。它使用一种名为 `TOML` 的标记格式编写：

```
[tool.poetry]
name = "my-project"
version = "0.1.0"
description = ""
authors = ["Daniel van Flymen "]
[tool.poetry.dependencies]
python = "³.8"
[tool.poetry.dev-dependencies]
pytest = "⁵.2"
[build-system]
requires = ["poetry>=0.12"]
build-backend = "poetry.masonry.api "
```

请注意，我们现在唯一的依赖项是 Python `³.8`，这表明你的项目需要 `3.8.0` 到 `4.0.0` 之间的*任何* Python 版本。Poetry 还添加了 `pytest` 作为用于运行测试的 `dev` 依赖项，但我们稍后会讲到这一点。



### 安装依赖

最流行的 Python 库之一是 `requests` 库，它使我们能够轻松发起 HTTP 请求。我们来安装它：

```
$ poetry add requests
Creating virtualenv my-project-py3.8 in /Users/dvf/Library/Caches/pypoetry/virtualenvs Using version ².22 for requests
Updating dependencies
Resolving dependencies... (0.7s)
Writing lock file
Package  operations:  14  installs, 0 updates, 0 removals
- Installing  more-itertools  (7.2.0)
- Installing zipp (0.6.0)
- Installing importlib-metadata (0.23)
- Installing atomicwrites (1.3.0)
- Installing attrs (19.3.0)
- Installing certifi (2019.9.11)
- Installing chardet (3.0.4)
- Installing idna (2.8)
- Installing pluggy (0.13.0)
- Installing py (1.8.0)
- Installing six (1.12.0)
- Installing urllib3 (1.25.6)
- Installing pytest (3.10.1)
- Installing requests (2.22.0)
```

使用 `poetry add requests` 这一语法会导致以下一系列事情发生：

1. 在 `/Users/dvf/Library/Caches/pypoetry/virtualenvs/my-project-py3.8` 创建了一个“虚拟环境”（virtualenv）。虚拟环境是一个包含 Python 解释器和项目所需包的文件夹，这样就不会污染系统的其他部分。

2. 从 Python 的包索引 [`https://pypi.org`](https://pypi.org) 下载了最新版本的 `requests` (2.22.0)。

3. `requests` 所依赖的所有依赖项也被一并下载了。这些被称为子依赖。

4. 所有下载的包都被安装到了项目的虚拟环境中。

我们现在已经准备好开始构建并与我们的依赖进行交互了。

## 激活虚拟环境

在运行你的项目之前，你需要激活虚拟环境。也就是说，告诉你的计算机去使用由 Poetry 管理的虚拟环境中的 Python 解释器。

首先，让我们检查一下我们当前正在使用的 Python 解释器：

```
$ which python
/usr/local/bin/python
```

让我们激活虚拟环境：

```
$ poetry shell
Spawning shell within /Users/dvf/Library/Caches/pypoetry/virtualenvs/my-project-py3.8
```

现在，让我们验证一下我们正在使用的 Python 解释器是否是 Poetry 虚拟环境中的那个：

```
$ which python
/Users/dvf/Library/Caches/pypoetry/virtualenvs/my-project-py3.8/bin/python
```

遇到问题了？

如果你遇到了问题，那么你应该小心地关闭并重新打开你的终端，并严格按照本章开头部分的说明进行操作。如果仍然卡住，那么很可能是你的 `PATH` 变量有问题。

我建议你仔细阅读 Poetry 的文档以确保你已遵循所有步骤，因为自从本书出版以来，该软件可能已经发生了变更。

## 示例：获取比特币价格

让我们通过编写一些代码来小试牛刀，这些代码会从交易所获取当前的比特币现货价格。

首先，在 `my-project/my_project` 目录下创建一个名为 `current_price.py` 的文件。你的文件夹结构应如下所示：

```
.
├── README.rst
├── my_project
│  ├── __init__.py
│  └── current_price.py
├── pyproject.toml
└── tests
├── __init__.py
└── test_my_project.py
```

我们将使用刚刚安装的 `requests` 库来获取以美元计的价格。在 `current_price.py` 中输入以下代码：

```
import requests
response = requests.get("https://api.coinbase.com/v2/prices/spot?currency=USD")
print(response.text)
```

好的，保存你的文件。让我们运行它：

```
$ poetry run python my_project/current_price.py
{"data":{"base":"BTC","currency":"USD","amount":"9161.25"}}
```

在前面这个命令中，注意其中的 `poetry run...` 部分。这是一种快捷方式，用于启用虚拟环境并在此环境中运行命令的其余部分。等价地，你也可以使用 `poetry shell` 来启用虚拟环境，然后运行命令。

如果你仔细查看输出，你会看到价格为 `$9161.25`。

## 总结

总而言之，我们涵盖了：

1. 在你的机器上安装一个全新的 Python 3.8 解释器

2. 使用 Poetry 为一个新项目创建并安装依赖（从本章开始，我们将在后续的每一章都这样做）

3. 通过使用 `requests` 库调用 API 来获取最新的比特币价格

现在你已经设置好了项目结构，我们准备深入并开始在实践中学习。

