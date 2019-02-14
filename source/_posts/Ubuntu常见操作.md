---
title: Ubuntu常见操作
date: 2019-02-14 10:10:02
tags:
    - 快查手册
    - Ubuntu
---

>因为上手时间不长，用得少，很多配置容易忘记。所以做了快查手册，以便自己及时获取，快速解决问题。

#### 添加远程分支并取别名
```
git remote add github git@github.com:JoyBanana/Pan_163.git
#这里添加别名为github.   删除远程分支，将add替换为为remove即可
```

#### 查看远程分支
```git
$ git remote -v
banwagong       ssh://git@huanghaha.cc:29280/home/git/pan_163.git (fetch)
banwagong       ssh://git@huanghaha.cc:29280/home/git/pan_163.git (push)
github  git@github.com:JoyBanana/Pan_163.git (fetch)
github  git@github.com:JoyBanana/Pan_163.git (push)
```

#### 拉取远程分支代码
```
git pull banwagong master
```

#### 推送本地代码到远程分支
```
git push banwagong master
```


#### 删除远程分支文件但是不影响本地
```
git rm --cached filename/-r directory
git commit -m "xxxx"
git push
```
删除远程分支的文件夹时要加 `-r`参数，表示递归
