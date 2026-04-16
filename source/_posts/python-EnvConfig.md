---
title: Python 项目环境配置及运行操作指南
date: 2026-04-16 15:00:00
img: https://s21.ax1x.com/2024/05/31/pk8QzmF.jpg
tags:
 - js
 - python
categories:
 - js
 - python
keywords:
 - python环境配置
 - python
---


# Python 项目环境配置及运行操作指南

## 一、安装 Python 基础环境

Python 程序运行离不开基础环境。推荐直接去 Python 官网下载最新稳定版（如 3.14.x ）。【这里的版本要特别注意，一定要是Python 3+ 的，因为Python 2 已经停止维护，而且它没有 venv 这个模块，venv 是从 Python 3.3 才开始有的。】

### 1. 下载安装包

访问 [python.org/downloads](https://www.python.org/downloads/)，点击黄色的 “Download Python” 按钮。

### 2. 安装配置（关键）

运行下载的 `.exe` 文件，在安装界面最下方务必勾选 “Add Python to PATH”，然后点击 “Install Now” 一键安装。这样你才能在命令行里直接使用 `python` 命令。

### 3. 验证安装

按 `Win + R`，输入 `cmd` 并回车打开命令提示符，分别运行以下命令；如果能看到版本号信息，说明安装成功。

```bash
python --version
pip --version
```

## 二、准备 Python 运行环境（虚拟环境）

虚拟环境就像一个项目专用的“小隔间”，用来隔离不同项目所需的软件包，避免冲突。

### 1. 打开项目

在 Cursor 中，通过 `File > Open Folder...` 打开你的项目文件夹。

### 2. 打开终端

在 Cursor 中按快捷键 `Ctrl + \``（Esc 键下方的反引号）打开内置终端。

### 3. 创建虚拟环境

在终端中，确保当前路径是你的项目根目录，然后运行：

```bash
python -m venv .venv
```

### 4. 激活虚拟环境（Windows）

```bash
.venv\Scripts\activate
```

如果命令行前面出现了 `(.venv)`，说明激活成功。

> ⚠️ **PowerShell 权限问题（常见）**  
> 如果激活时遇到类似“无法加载，因为在此系统上禁止运行脚本”的错误，请以管理员身份打开 PowerShell，输入以下命令并选择 `Y` 确认：

```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

## 三、安装项目依赖

确认终端已激活虚拟环境（即最前面有 `(.venv)` 标识）。在终端中运行以下命令，从 `requirements.txt` 读取并安装依赖：

```bash
pip install -r requirements.txt
```

等待进度条跑完，看到 “Successfully installed...” 就成功了。

> 💡 **下载太慢可换国内镜像（如清华源）**  
> 在终端执行一次下面的命令，以后安装会快很多：

```bash
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

## 四、在 Cursor 中配置并运行项目

### 1. 确保 Python 扩展已安装

点击 Cursor 左侧活动栏的扩展图标（四个方块），或按 `Ctrl + Shift + X`。在搜索框输入 “Python”，找到微软官方发布的扩展（作者为 Microsoft，名字就是 “Python”），点击安装。

### 2. 选择解释器

在 Cursor 中按 `Ctrl + Shift + P` 打开命令面板，输入并选择 `Python: Select Interpreter`。

在弹出的列表中，选择或输入以下路径：

```text
.\.venv\Scripts\python.exe
```

如果列表里没有，可以点击 “Enter interpreter path” 手动输入。完成后，Cursor 右下角状态栏应该会显示 `.venv`，表示配置成功。

### 3. 运行项目

- **方法一（终端运行）**：在已激活虚拟环境的终端里运行 `python main.py`（入口文件也可能是 `app.py` / `run.py` 等）。
- **方法二（一键运行）**：在 Cursor 中打开项目入口文件，直接按 `Ctrl + F5` 运行。

## 五、常见问题快速排查

| 问题 | 解决方法 |
| --- | --- |
| 运行 `python` 命令提示“不是内部或外部命令” | 环境变量没配好。重新运行 Python 安装程序，并务必勾选 “Add Python to PATH”。 |
| 安装依赖时下载失败或速度很慢 | 换成国内镜像源（见第三部分的提示）。 |
| Cursor 里找不到 `Python: Select Interpreter` 命令 | 说明 Python 扩展没装。按 `Ctrl + Shift + X` 搜索 “Python” 并安装微软官方版本。 |
| 运行时提示 `ModuleNotFoundError: No module named 'xxx'` | 缺少第三方库。确认终端已激活虚拟环境（前缀显示 `(.venv)`），然后执行 `pip install xxx` 安装该库。 |

## 六、总结：一条清晰的流程

### 1. 创建并激活虚拟环境

```bash
python -m venv .venv
.venv\Scripts\activate
```

### 2. 安装项目依赖

```bash
pip install -r requirements.txt
```

### 3. 在 Cursor 中选择解释器

`Ctrl + Shift + P` → `Python: Select Interpreter` → `.\.venv\Scripts\python.exe`

### 4. 运行主文件

```bash
python main.py  # 入口文件名可能不同，如 app.py、run.py
```
