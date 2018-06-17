---
title: CentOS 下的网易云音乐安装
date: 2018-6-17 12:52:11
tags:
    - CentOS
    - 
---

端午节没有回家，无聊把我大学时期的笔记本装了Linux。新手就是新手总想着装最新版，反正是自己用 根本不用考虑什么稳定 然后我就装了CentOS 7


年轻人嘛 就是要折腾


CentOS主要还是中小型企业搭建服务器的比较多，个人用作桌面系统还是比较少，所以折腾之旅就开始辣～


作为一名`文青` 怎么能不用网易云音乐评论区去装B呢，没有文艺但是可以装嘛，在这个念头下我打开了网易云的官网，准备下载客户端。

### Centos下网易云音乐的安装
#### 下载
我记得网易云音乐是有Ubuntu版本的，先去网易云音乐的官网看了下
<div class="666">
<img src="/posts_img/download_netease_cloud_music.png" width="100%" >
</div>
Emmm？？？只有Ubumtu和Deepin版本？！这怎么能行，抓紧搜一搜有没有小哥哥装上过
<div class="666">
<img src="/posts_img/表情包/baiduyixia.jpg" width="30%" >
</div>

#### 解压、安装
果然有，大概意思呢就是在网易云下载Ubuntu的deb安装包，直接解压，会有三个压缩包。然后再次解压里面的`data.tar.xz`把解压出来的usr目录直接复制到`系统usr目录下`
Emmmmm 很暴力 由于netease-cloud-music在bin目录下，所以我们可以直接在终端中运行netease-cloud-music

#### 解决依赖

直接运行的话根据每个人电脑环境不同可能有的会报错，比如我 提示`vlc command not found` emmm～能看懂 给我厉害坏了 叉会儿腰
使用yum 命令安装 vlc 然后再次尝试运行！
<div class="666">
<img src="/posts_img/lack_3.4.21.png" width="80%" >
</div>
提示`GLIBCXX_3.4.21' not found
抓紧看下自己的gcc版本
<div class="666">
<img src="/posts_img/gcc4.8.5.png" width="80%" >
</div>

喵喵喵？？？都更新到8.x了我还在用4.8.x？
直接去[清华源镜像](https://mirrors.tuna.tsinghua.edu.cn/gnu/gcc/)下载源码更新gcc，解决这个问题 解压并执行

``` bash
./contrib/download_prerequisites
make
```
经过漫长长长长长长的等待（这段时间炒了个菜，冰好啤酒，热好馒头，洗个澡，吃完早午饭）后gcc终于编译完成了！
<div class="666">
<img src="/posts_img/gcc8.1.png" width="80%" >
</div>


``` bash
make install
find / -name libstdc++.so.6 

```
找到所在文件夹将编译生成的新版本替换 usr/lib64下的libstdc++.so.6

再次运行～ 成功
#### 配置
比较蛋疼的是 我试了下只能通过在终端中通过sudo命令运行网易云，而且终端一关 网易云就掉了，怎么办呢？
<div class="666">
<img src="/posts_img/sudo_nestease_cloud_music.png" width="100%" >
</div>
打开你的 /usr/share/applications 找到网易云的图标，打开属性 在命令一行(exec)最前面加上 sudo 就可以直接打开辣
再来试一试，点击图标 成了！

#### 感激

感谢以下文章的指导，排名不分先后

[乌合之众——linux下编译gcc6.2.0](https://www.cnblogs.com/oloroso/p/5984985.html) 

[violick——CENTOS 7 安装网易云音乐](https://blog.csdn.net/kkaazz/article/details/78768462) 

<div class="666">
<img src="/posts_img/netease_desktop.png" width="100%" >
</div>

补一张安装成功的截图
