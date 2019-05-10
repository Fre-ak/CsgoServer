# 怎样搭建一个csgo服务器

> 本文将引导你如何在linux上搭建一个属于自己的csgo服务器, 所以会假定你拥有基本的linux知识.
由于linux的发型版众多, 这里主要介绍ubuntu 16.04 LTS 64bit下的教程.

## 环境配置

```bash
sudo dpkg --add-architecture i386; sudo apt update; sudo apt install mailutils postfix curl wget file bzip2 gzip unzip bsdmainutils python util-linux ca-certificates binutils bc jq tmux lib32gcc1 libstdc++6 libstdc++6:i386
```

其他发行版 ( *Debian, Fedora, CentOS* ) 请看 [linuxGSM](https://linuxgsm.com/lgsm/csgoserver/)

## 下载本体并安装

```bash
mkdir csgo && cd csgo &&  wget -O linuxgsm.sh https://linuxgsm.sh && chmod +x linuxgsm.sh && bash linuxgsm.sh csgoserver
./csgoserver install
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

然后在交互模式中选择 *sourcemod* 或者 *metamod* 安装

此外我们还要安装其他的插件, 如 *btimer cksurf influx* 等, 只需要把插件上传上去解压, 然后复制覆盖掉对应的文件夹即可

## 配置

配置分为三种, *服务器参数配置*, *[lgsm配置](https://docs.linuxgsm.com/configuration/linuxgsm-config)*, *插件配置*

分别的路径为:

>
        csgo/serverfiles/csgo/cfg/csgoserver.cfg
        csgo/lgsm/config-lgsm/csgoserver
        插件配置可能存在于多个位置

## 数据库

数据库的主要作用是保存数据, 比如地图数据与玩家数据等.
数据库分为关系型和非关系型, 其中非关系型又分为 键值型(redis, ssdb) 文档型(mongodb) 搜索型(es), 而关系型数据库却大同小异, 一般来说timer只支持 sqllite和*mysql*

### 安装**mysql**

```bash
sudo apt install mysql-server
```

安装完成后会出现弹窗设置root密码

### 创建csgo数据库

```bash
mysql -uroot -p密码
create database csgo charset=utf8mb4
```

然后根据timer要求, 在 *csgo/serverfiles/csgo/addons/sourcemod/configs/databases.cfg* 这个文件中加入数据库信息

DEMO:
>
        "timer"
        {
                "drive"                         "default"
                "host"                          "localhost / 数据库ip"
                "database"                      "csgo"
                "user"                          "xxx"
                "pass"                          "xxx"
                "port"                          "3306"
        }

## 其他

### 一些linux命令

```bash
unzip *.zip             # 解压zip文件
bzip2 -d *.bz2             # 解压bz2文件
scp filename root@ip:filepath             # scp上传文件
```

### 一些小技巧

*crontab*是linux上的定时任务, 我们可以利用它来定时执行某些命令, 解放双手, 因为精力有限, 不做过多基础介绍, 有兴趣的自己去找资料

DEMO:
>
    0 * * * *  csgo/csgoserver update    #每小时自动更新服务器一次
    30 * * * * csgo/csgoserver start    #每小时自动启动服务器一次(防止因为意外挂掉)
    0 5 * * *  csgo/csgoserver restart   #每天凌晨五点重启服务器
    0 * * * * cd csgo/serverfiles/csgo/maps && ls bhop*.bsp | sed 's/.bsp//' > ../mapcycle.txt     #每小时自动更新mapcycle.txt一次

### 需要注意

+ 本文中路径需替换成你自己的路径
+ 没有linux基础的上手起来可能十分困难
+ 有些细节没有介绍, 需要各位自己摸索
+ 有问题的可以给我提**issue**, 但是问题描述尽量详细

## 我的服务器

```bash
47.107.173.171:27015    # 128tick btimer 广州
119.29.102.65:27015    # 100tick btimer 深圳or广州
118.25.224.90:27015    # 64tick surftimer 成都
```

欢迎各位来玩
