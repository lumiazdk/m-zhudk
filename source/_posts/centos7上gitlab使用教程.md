---
title: centos 7 上gitlab使用教程
tags:
  - git
  - gitLab
categories: 工具
abbrlink: 18159
date: 2019-04-28 14:01:54
---
## 1安装gitlab
 ``` shell
curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash 
sudo yum install gitlab-ce 
```

## 修改配置
``` shell
vim /etc/gitlab/gitlab.rb
 ```
### 常用gitlab命令
## 启动
``` shell
sudo gitlab-ctl start
 ```
## 停止
``` shell
 sudo gitlab-ctl stop
 ```
## 重启
```shell
 sudo gitlab-ctl restart
 ```
## 重置配置
```shell
 sudo gitlab-ctl reconfigure 
```
<!-- more -->

## 2安装 gitlab-runner
## 添加repository
```shell
 curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-ci-multi-runner/script.rpm.sh | sudo bash
 ```
## 安装包
```shell
 yum install gitlab-runner
 ```
## 注册runner
```shell
 sudo gitlab-runner register
 ```
> 执行上述命令之后，之后的流程如下：
>第一处红线：输入部署完成的gitlab地址比如http://111.111.111.111:8080/
>第二处红线: 输入token，token的值可以在登录gitlab之后，下图所示位置找到，先点击右上方Admin Settings，再找到左侧列表的runners
<fancybox>
![](https://res.cloudinary.com/lumiazdk/image/upload/v1554635068/hmpoapgta6jrvljd25md.png)
![](https://res.cloudinary.com/lumiazdk/image/upload/v1554635084/hjswdrq7ajbjod0ssuw5.png)
</fancybox>


>选executor时不了解docker的可以选shell，根据提示输入账户密码即可注册成功
>最后gitlab中会出现runner

<fancybox>
![](https://res.cloudinary.com/lumiazdk/image/upload/v1554635100/le8faa2sg5nrgci7gmvg.png)
</fancybox>

# 运行
> sudo gitlab-ci-multi-runner run
>配置成功！
