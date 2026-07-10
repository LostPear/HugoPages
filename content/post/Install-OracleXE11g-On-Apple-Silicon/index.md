---
title: "在基于 Apple Silicon 处理器的 Mac 安装 OracleXE 数据库"
date: 2025-02-20 17:15:00
lastmod: 2025-02-26 10:45:05
tags:
  - 教程
categories:
  - 教程
---
这几天买了一台 Macbook Air M1 玩玩，学校上课的时候要用 OracleXE 11g 数据库，但是软件没有提供 macOS 的版本，几经周折终于折腾成功，在这里写下折腾的过程。

## 1.安装 homebrew

一开始在这一步就栽跟头了，macOS 默认用的终端软件是 zsh，但是官方的似乎是给 bash 准备的，安装了之后终端跑不了 brew。因此改为使用下面的命令安装：

```bash

/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"

```

安装的时候使用中科大下载源，等待安装完成。

## 2.安装 Colima

运行以下命令，安装 colima：

```bash

brew install colima qemu

```

然后运行以下命令，创建一个运行环境：

```bash

colima start --memory 4 --arch x86_64

```

## 3.安装 Docker

使用 brew 安装。运行以下命令：

```bash

brew install docker

```

等待安装完成。

## 4.设置 Docker 容器

先运行以下命令登录 Docker：

```bash

docker login

```

按照提示登录。然后在终端中运行以下命令：

```bash

docker run -h "oraclehost" --name "oracle" -d -p 1521:1521 oracleinanutshell/oracle-xe-11g

```

等待拉取镜像并安装完毕。

## 5.进入 Docker 容器，验证是否安装成功

首先运行以下命令，获取容器的 ID：

```bash

docker ps

```

你可以看到类似以下的输出：

```bash

CONTAINER ID   IMAGE                             COMMAND                   CREATED             STATUS             PORTS                                                         NAMES

cdd8e2328ef2   oracleinanutshell/oracle-xe-11g   "/bin/sh -c '/usr/sb…"   About an hour ago   Up About an hour   22/tcp, 8080/tcp, 0.0.0.0:1521->1521/tcp, :::1521->1521/tcp   oracle

```

`cdd8e2328ef2`就是需要的容器 ID。然后运行以下命令，进入容器内部的命令行，其中`cdd8e2328ef2`替换为你自己的容器

 ID：

```bash

docker exec -it cdd8e2328ef2 /bin/bash

```

然后输入以下命令：

```bash

sqlplus system/oracle

```

如果一切正常，应该可以看到以下输出：

```bash

SQL*Plus: Release 11.2.0.2.0 Production on Thu Feb 20 09:41:12 2025

Copyright (c) 1982, 2011, Oracle.  All rights reserved.

Connected to:

Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production

SQL>

```

这样就安装好了。

----

## 可能遇到的一些问题

### **Q1：**使用 brew 安装 docker 后，配置时提示以下错误信息该怎么办？

```bash

macos Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?

```

**A：**运行以下命令卸载掉 docker：

```bash

brew uninstall docker

```

然后前往 [Docker 官网][1]下载安装 Docker Desktop 作为替代。

### **Q2：**安装 Docker Desktop 后，终端显示找不到 docker 命令该怎么办？

**A：**用 vim 编辑`~/.zshrc`（如果没有就新建一个），加入以下行保存：

```bash

export PATH="/Applications/Docker.app/Contents/Resources/bin:$PATH"

```

然后运行以下命令，使更改生效。

```bash

source ~/.zshrc

```

### **Q3：**容器默认的用户名，密码，服务名是什么？端口是多少？

**A：**用户名：system；密码：oracle；服务名：XE；端口：1521。

### **Q4：**我想使用 Navicat 查看数据库中的内容，连接信息填写无误，但是连接时显示“未加载 Oracle 数据库”，该怎么办？

**A：**在访达中右键 Navicat，选择`显示简介`，勾选`使用 Rosetta 运行`后保存即可。

### **Q5：**使用 sqlplus 登录数据库后，箭头不能被正确识别，按下后显示转义字符

**A：**先在容器中运行以下命令安装`rlwrap`：

```bash

apt update

apt install rlwrap

```

然后改用以下命令登录：

```bash

rlwrap sqlplus system/oracle

```

## 一些碎碎念

不要使用网上那个 deepdiver 的镜像，好像因为镜像太老了 docker 根本拉不下来。

不知道为什么 Docker Desktop 显示不了用上面方法创建好的容器，而且 Docker Desktop 里创建的容器也运行不了，后来发现新的 Docker 环境在 Colima 的文件夹下，那应该就是因为这个 Docker 被装到了 Colima 的环境里导致软件不能直接读取。

  [1]: https://www.docker.com/
