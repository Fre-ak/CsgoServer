# 怎样搭建一个csgo服务器

> 本文将引导你如何在linux上搭建一个属于自己的csgo服务器, 所以会假定你拥有基本的linux知识.
由于linux的发型版众多, 这里主要介绍ubuntu下的教程.

## 环境配置

```bash
sudo dpkg --add-architecture i386; sudo apt update; sudo apt install mailutils postfix curl wget file bzip2 gzip unzip bsdmainutils python util-linux ca-certificates binutils bc jq tmux lib32gcc1 libstdc++6 libstdc++6:i386
```

## 下载本体
```bash
mkdir csgo && cd csgo
wget -O linuxgsm.sh https://linuxgsm.sh && chmod +x linuxgsm.sh && bash linuxgsm.sh csgoserver #下载脚本
./csgoserver install #安装
```
这里应该会出现进度条, 等他慢慢安装完成

## 服务器管理
进入csgo文件夹

```bash
./csgoserver start   #运行服务器
./csgoserver restart   #重启服务器
./csgoserver stop   #停止服务器
./csgoserver update   #更新服务器
```

## 加入插件
首先要下载sourcemod和metamod, 其他插件都是基于这两个开发的

```bash
./csgoserver mods-install #安装插件
```

然后在交互模式中选择插件安装


## 数据库

### 安装mysql
```bash
sudo apt install mysql-server
```
安装完成后会出现弹窗设置root密码

### 创建csgo数据库
```bash
mysql -uroot -p密码
create database csgo charset=utf8mb4
```

## 其他