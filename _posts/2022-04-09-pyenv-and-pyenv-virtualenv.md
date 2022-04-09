---
title: macOS 12 pyenv 和 pyenv-virtualenv 安装和使用
author: unitaryorz
date: 2022-04-08 19:28:00 +0800
categories: [Python, Macos]
tags: [python, macos]
---
# macOS 12 pyenv 和 pyenv-virtualenv 安装和使用

macOS 12.3开始系统自带的Python.2.7被移除
[macOS Monterey 12.3 Release Notes | Apple Developer Documentation](https://developer.apple.com/documentation/macos-release-notes/macos-12_3-release-notes)

所以需要自行下载Python版本，而多个版本的Pyhton在Mac中可能会有冲突，最好是用版本管理来进行管理

pyenv可以通过命令方便的管理Python的环境，python-virtualenv是基于pyenv的python 虚拟环境管理

## 准备工作

安装之前推荐先设置代理，查看你的代理对应的端口，不设置代理brew和pyenv的下载都会特别的慢，没有代理自行搜索如何用国内镜像

![](https://cdn.jsdelivr.net/gh/Unitary-orz/unitary_oss/img/2022/04/16.27.00%20at%202022-04-09.jpg)

输入命令，终端(shell)使用代理，这个命令是一次行的，终端关闭后就失效了

```bash
export https_proxy=http://127.0.0.1:1087 http_proxy=http://127.0.0.1:1087 all_proxy=socks5://127.0.0.1:1087
```

Homebrew的安装

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## pyenv的安装和使用

Homebrew安装 pyenv

```bash
brew update
brew install pyenv
```

配置shell环境，这里有一个坑点，第一条是pyenv 2021下半年才新增的命令，网上许多文章没有，导致pyenv的版本控制无法正常使用

```bash
echo 'eval "$(pyenv init --path)"' >> ~/.zprofile
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
```

重新打开终端，或者执行以下命令加载pyenv的配置

```bash
source ~/.zprofile
source ~/.zshrc
```

查看配置成功没，通过查看环境变量`$PATH`中是否有`/Users/[用户名]/.pyenv/shims`

![](https://cdn.jsdelivr.net/gh/Unitary-orz/unitary_oss/img/2022/0415.34.02%20at%202022-04-06@2x.814d882651b942fdb255e910a1f0f1c0.jpg)

选择你要安装的版本，以下安装Python 3.8.13和Python 2.7.18

```bash
pyenv install 3.8.13
pyenv install 2.7.18
```

查看已安装的Python版本

```bash
pyenv versions
```

`pyenv global` 设置全局版本

```bash
pyenv global 3.8.13 2.7.18
```

根据设置版本的前后来决定`python`解析的是哪个，前面的版本为`3.8.13`所以优先解析它

```bash
➜  ~ python --version
Python 3.8.13
➜  ~ python2 --version
Python 2.7.18
➜  ~ python3 --version
Python 3.8.13
➜  ~ which python3
/Users/unitary/.pyenv/shims/python3
➜  ~ which python
/Users/unitary/.pyenv/shims/python
```

pip 类似

```bash
➜  ~ pip --version
pip 22.0.4 from /Users/unitary/.pyenv/versions/3.8.13/lib/python3.8/site-packages/pip (python 3.8)
➜  ~ pip2 --version
pip 19.2.3 from /Users/unitary/.pyenv/versions/2.7.18/lib/python2.7/site-packages/pip (python 2.7)
```

`pyenv shell` 设置shell下的版本，意思在当前的shell下使用的版本，关闭终端后就失效

```bash
pyenv shell 2.7.18
```

`pyenv local` 设置本地目录下的版本，这个目录下所有目录都共用这个版本

```bash
➜  ~ pyenv local 2.7.18
➜  ~ python --version
Python 2.7.18
➜  ~ cd Applications
➜  Applications python --version
Python 2.7.18
```

本地目录的版本设置信息存放在`./python-version`中，`pyenv local --unset`进行取消

```bash
➜  ~ cat .python-version
2.7.18
➜  ~ pyenv local --unset
➜  ~ cat .python-version
cat: .python-version: No such file or directory
```

## pyenv-virtualenv的安装和使用

Homebrew安装 pyenv-virtualenv

```bash
brew install pyenv-virtualenv
```

shell 环境配置

```bash
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.zshrc
source ~/.zshrc
```

创建一个虚拟环境`venv38`，环境为当前的Python版本`3.8.13`

```bash
pyenv virtualenv venv38
```

创建指定版本的虚拟环境`venv27`，指定版本为`2.7.18`

```bash
virtualenv 2.7.18 venv27
```

查看已创建的虚拟环境

```bash
➜  ~ pyenv virtualenvs
  2.7.18/envs/venv27 (created from /Users/unitary/.pyenv/versions/2.7.18)
  3.8.13/envs/venv38 (created from /Users/unitary/.pyenv/versions/3.8.13)
  venv27 (created from /Users/unitary/.pyenv/versions/2.7.18)
  venv38 (created from /Users/unitary/.pyenv/versions/3.8.13)
```

切换至虚拟环境，在虚拟环境中下载的模块是独立的，只能在虚拟环境中使用，和其他环境隔离开的

```bash
➜  ~ pyenv shell venv27
(venv27) ➜  ~ python --version
Python 2.7.18
```

在有些情况下需要用`activate`的方式启用虚拟环境，我们这次激活`venv38`虚拟环境，最后用`pyenv deativate`退出环境

```
➜  ~ pyenv activate venv38
(venv38) ➜  ~ python --version
Python 3.8.13
(venv38) ➜  ~ pyenv deactivate
➜  ~
```


