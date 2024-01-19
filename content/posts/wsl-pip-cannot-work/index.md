---
title: "WSL中pip不能用的情况下装第三方包"
date: 2022-12-20 23:10:00
lastmod: 2022-12-20 23:15:20
tags: ["python", "pip", "wsl"]
---

### 问题描述
接到一个野单子，做一个Django+Vue的二次开发，由于Celery 4以后不再支持Windows，因此本地开发调试需要用到WSL子系统。在帮客户安装开发环境的时候，发现他要一直开一个防火墙软件，导致WSL内的pip无法访问到pypi和镜像站，一直提示
```shell
Retrying (Retry(total=4, connect=None, read=None, redirect=None, status=None)) after connection broken by 'ProtocolError('Connection aborted.', OSError(0, 'Error'))': /simple/[package]/
```
conda可以安装，但是缺少项目用到的一些第三方库。查找Stackoverflow发现，很多人有这种问题，包括[WSL的官方issues](https://github.com/microsoft/WSL/issues/4020)，至今没有一个统一有效的解决方案。https://github.com/microsoft/WSL/issues/4020

### 思路
1.完整拷贝我的WSL下环境给他，但考虑到整个环境将近1GB，放弃  
2.挨个下载需要用到的whl文件，然后分别安装。几十个包，太麻烦，放弃  
3.在WSL通过pip.exe调用windows的pip，通过`python -m site`找到安装路径，然后用`pip install [package] --target /path/to/wsl/site-packages`来装到WSL的环境内，好像可行
### 实践
在本机用requests包安装了一下，发现可以完美检测使用，于是开始帮客户装。
调试过程中，发现numpy，pandas包的使用出现问题，果然没那么一帆风顺，猜测是因为Windows和Linux环境下这些包的代码不一致，而pip.exe默认下载的是Windows下的包，所以会有兼容问题，好在客户的conda能用，于是在WSL内卸载，用conda重装，问题解决！

但后续，celery本身出问题了，conda提供的celery和flower版本又太旧，这时候我试着pip了一下，竟然好了。然后我寻思着重新建一个环境，直接在WSL里安装合适的版本，但这时候又不行了。最后我发现用Windows的pip向WSL里安装任意一个包都可以让WSL里的pip变得正常，原来是Miniconda的锅！猜测是certifi这个包被更新了，然后就可以用了。
### 结论
WSL运行`python -m site`获取路径
Windows运行`pip install [any_package] --targer /path/to/wsl/site-packages`
此时WSL里的pip恢复正常！