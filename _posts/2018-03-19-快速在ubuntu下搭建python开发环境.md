---
layout: post
title: "快速在Ubuntu下搭建Python开发环境"
date: 2018-03-19 19:00：00 +0300
categories:
- Ubuntu,Python
tags:
- Ubuntu,Python
---

# 快速在Ubuntu下搭建Python开发环境

因为在windows环境下一些python包的安装会遇到各种各样奇怪的编码问题，为了彻底解决这个问题，我决定改到Linux环境下学习python开发，我选择的是Unbunt16.04版本，这个版本集成了python2.7和python3.5两个python版本，但现在的python3稳定版本都已经升级到3.6了，于是需要做的第一件事就是去更新python版本。

## 1 下载最新的源码
去python的[官网](https://www.python.org/)找到Download按钮，然后选择Source Code去下载最新的压缩包.以目前的python3.6.4为例。
下载完压缩包后记得解压压缩包。
然后使用 `cd python-3.6.4`进入文件
## 2 开始编译
进入被释放的文件夹`cd Python-3.6.4`
添加配置`./configure -prefixinstall`
然后利用`sudo make`和`sudo make install`来编译安装

## 3 修改系统默认的python
Ubuntu 系统默认的python是2.7，默认的python3是python3.5建议先行卸载python3.5，然后将python3链接到python3.6上
卸载python3.5
`sudo apt-get remove python3`
删除python3的链接文件
`sudo rm /usr/bin/python3`
建立指向python3.6的链接文件
`ln -s /usr/bin/python3.6/bin/python3.6 usr/bin/python`
## 4 查询python版本检验是否安装成功
`python3 --version`

## 5 安装Pycharm
简单的命令行安装 `sudo snap install pycharm-community --classic`，就可以直接安装，虽然会比较花时间，但可以一步到位的安装好。