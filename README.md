
# 如何搭建一个csgo服务器

> 本文将引导你如何在 linux 上搭建一个属于自己的 csgo 服务器, 所以会假定你拥有基本的 linux 知识.
由于 linux 的发型版众多, 这里主要介绍 **ubuntu 16.04 LTS 64bit** 下的教程.

## 新建一个非 root 用户并赋予 sudo 权限
详情请前往 [我的博客](https://blog.fre-ak.me/archives/linux-add-newuser.html)

切换到新用户, 以下所有步骤都将在新用户下进行

## 环境配置

Ubuntu 64-bit
```bash
sudo dpkg --add-architecture i386; sudo apt update; sudo apt install mailutils postfix curl wget file bzip2 gzip unzip bsdmainutils python util-linux ca-certificates binutils bc jq tmux lib32gcc1 libstdc++6 libstdc++6:i386 lib32z1
```

其他发行版 (**Debian, Fedora, CentOS**) 请前往 [linuxGSM](https://linuxgsm.com/lgsm/csgoserver/)


## 下载服务端并安装

```bash
mkdir csgo && cd csgo &&  wget -O linuxgsm.sh https://linuxgsm.sh && chmod +x linuxgsm.sh && bash linuxgsm.sh csgoserver
./csgoserver install
```
>如果是想搭建除了 **csgo** 以外的服务器, 只需把csgoserver替换成其他的即可 如:cssserver(cs起源) gmodserver(盖瑞模组) 更多可在[**此处查看**](https://linuxgsm.com/lgsm/) 为了更方便管理服务器, 建议把 **csgo** 替换成相应的字符

这里应该会出现进度条, 等他慢慢安装完成

## 服务器管理

进入刚刚创建的文件夹, 接下来都是以csgo为例.

```bash
./csgoserver start   #运行服务器
./csgoserver restart   #重启服务器
./csgoserver stop   #停止服务器
./csgoserver update   #更新服务器
```

更多命令:
>
        start         st   | Start the server.
        stop          sp   | Stop the server.
        restart       r    | Restart the server.
        monitor       m    | Check server status and restart if crashed.
        test-alert    ta   | Send a test alert.
        details       dt   | Display server information.
        postdetails   pd   | Post details to hastebin (removing passwords).
        update-lgsm   ul   | Check and apply any LinuxGSM updates.
        update        u    | Check and apply any server updates.
        force-update  fu   | Apply server updates bypassing check.
        validate      v    | Validate server files with SteamCMD.
        backup        b    | Create backup archives of the server.
        console       c    | Access server console.
        debug         d    | Start server directly in your terminal.
        fastdl        fd   | Build a FastDL directory.
        mods-install  mi   | View and install available mods/addons.
        mods-remove   mr   | View and remove an installed mod/addon.
        mods-update   mu   | Update installed mods/addons.
        install       i    | Install the server.
        auto-install  ai   | Install the server without prompts.
        developer     dev  | Enable developer Mode.
        donate        do   | Donation options.

## 加入插件

首先要下载sourcemod和metamod, 我们所使用的插件都是基于这两个平台的

```bash
./csgoserver mods-install #安装插件
```

然后在交互模式中选择 **sourcemod** 或者 **metamod** 安装

如果我们需要安装其他的插件, 只需要把插件上传上去解压, 然后复制覆盖掉对应的文件夹即可

## 配置

配置分为三种, **服务器参数配置**, **[lgsm配置](https://docs.linuxgsm.com/configuration/linuxgsm-config)**, **插件配置**

分别的路径为:

>
        csgo/serverfiles/csgo/cfg/csgoserver.cfg
        csgo/lgsm/config-lgsm/*
        csgo/serverfiles/csgo/addons/sourcemod/configs #插件配置 1
        csgo/serverfiles/csgo/cfg/ #插件配置 2(取决于插件)

## 数据库

数据库的主要作用是保存数据, 比如地图数据与玩家数据等.
数据库分为关系型和非关系型, 其中非关系型又分为 键值型(redis, ssdb) 文档型(mongodb) 搜索型(es), 而关系型数据库却大同小异, 目前的 **sourcemod** 插件只支持 *mysql* 以及 *sqlite*

### 安装 **mysql**

```bash
sudo apt install mysql-server
```

安装完成后会出现弹窗设置 root 密码

### 创建数据库

```bash
mysql -uroot -p密码
create database csgo charset=utf8mb4
```

然后根据 **插件的数据库配置需求** 要求, 在 **csgo/serverfiles/csgo/addons/sourcemod/configs/databases.cfg** 这个文件中加入数据库信息

DEMO:
>
        "数据库接口名称"
        {
                "drive"                         "mysql / sqlite"
                "host"                          "localhost(本地) / 数据库ip / 服务器域名 / 接口文件路径"
                "database"                      "刚刚创建的数据库名"
                "user"                          "用户名"
                "pass"                          "密码"
                "port"                          "3306" //可选
        }

## 其他

### 一些linux命令

```bash
unzip *.zip             # 解压 zip 文件
bzip2 -d *.bz2             # 解压 bz2 文件
scp filename root@ip:filepath             # scp 上传文件
```

### 一些小技巧

**crontab** 是 linux 上的定时任务, 我们可以利用它来定时执行某些命令, 解放双手, 因为精力有限, 不做过多基础介绍, 有兴趣的自己去找资料

DEMO:
>
    0 * * * *  csgo/csgoserver update    #每小时自动更新服务器一次
    30 * * * * csgo/csgoserver start    #每小时自动启动服务器一次(防止因为意外挂掉)
    0 5 * * *  csgo/csgoserver restart   #每天凌晨五点重启服务器
    0 * * * * cd csgo/serverfiles/csgo/maps && ls bhop*.bsp | sed 's/.bsp//' > ../mapcycle.txt     #每小时自动更新 mapcycle.txt 一次

### 需要注意

+ 本文中路径需替换成你自己的路径
+ 没有 linux 基础的上手起来可能十分困难
+ 有些细节没有介绍, 需要各位自己摸索
+ 有问题的可以给我提 **issue**, 但是问题描述尽量详细
+ 这篇教程同时也会在 [我的博客](https://blog.fre-ak.me/archives/how-to-build-csgoserver.html) 上更新
+ 本文可以转载, 但必须附带上原作者以及原始链接

## 我的服务器

```bash
47.107.173.171:27015    # 128tick btimer 深圳
119.29.102.65:27015    # 102.4tick btimer 广州
112.74.163.160:27015    # 64tick surftimer 深圳
```
