---
title: 通过GitHook部署Hexo至VPS空间
date: 2016-12-11 16:23:26
tags: 
- VPS
- nginx
- git hook
- hexo
---
# Deploy Hexo Blog to VPS By Git Hook

## 基本思路

- 将Hexo生成的博客静态文件利用git上传到VPS中的git仓库中
- 服务器端git仓库收到git push 之后，通过git hook 触发 一段shell 脚本
- shell脚本将git push过来的网页静态文件复制到已经配置好nginx的文件目录下
- 通过nginx转发，则可以正常访问博客

## git安装配置
###1. 安装git
```bash
# root用户下
yum install git
```

###2.新建git用户
```bash
#root用户下
#添加用户
adduser git
# 修改密码
passwd git
```

###3.配置git用户
```bash
# git用户下
# 添加ssh公钥
mkdir -p ~/.ssh
vi ~/.ssh/authorized_keys
# 添加文件及目录权限
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
# 禁用密码登录
PasswordAuthentication no
```

###4.建立git仓库
```bash
# git用户下
cd ~
mkdir blog.git
cd blog.git
git init --bare
```
## nginx安装配置
### 安装
```bash
yum install nginx
```
### 启动并设置开机启动
```bash
systemctl start nginx.service
systemctl enable nginx.service
```
### 打开防火墙
```bash
firewall-cmd --permanent --zone=public --add-service=http
firewall-cmd --permanent --zone=public --add-service=https
firewall-cmd --reload
```
### 配置nginx
```bash
vi /etc/nginx/conf.d/blog.conf
```

```bash
# blog.conf
server {
  root /home/wwwroot/hexo; # 网站目录
  index index.html index.htm;
  server_name www.***.com *.***.com ; # 域名 或 IP
  location / {
    try_files $uri $uri/ /index.html;
  }
}
```
### 重启nginx
```bash
nginx -s reload
```

## git hook 配置

VPS的 hexo网站目录为 `/home/wwwroot/hexo/`

这个目录要给 git 用户访问，`hexo.git` 仓库收到提交后，git 用户要把提交的内容再 checkout 到 `/home/wwwroot/hexo/` 目录，所以要把这个目录的所有权交给 git 用户：

```
# root 下
chown git:git /home/wwwroot/hexo/
```

建立一个仓库内容转移的缓存目录：

```
mkdir ~/tmp/hexo_tmp
```

然后处理 hexo.git 提交的事件，自动更新内容到 hexo网站目录。在 git 用户下执行：

```
cd ~/hexo.git/hooks
touch post-receive
vi post-receive
```

编辑这个 post-receive 文件：

```
#!/bin/bash -l
GIT_REPO=/home/git/hexo.git
TMP_GIT_CLONE=/home/git/tmp/hexo_tmp
PUBLIC_WWW=/home/wwwroot/hexo
rm -rf ${TMP_GIT_CLONE}
git clone $GIT_REPO $TMP_GIT_CLONE
rm -rf ${PUBLIC_WWW}/*
cp -rf ${TMP_GIT_CLONE}/* ${PUBLIC_WWW}
```

这个脚本可以实现完全更新，即先删除原网站内所有内容，再拷贝新仓库内容到网站目录，可以避免一些不必要的问题。

还要给脚本加上执行权限：

```
chmod +x post-receive
```

## 配置 Hexo 的部署信息

打开 `_config.yml` 文件，找到 **deploy** 字段，修改如下：

```yaml
deploy: 
- type: git
  repo: git@**.**.**.**:hexo.git
  branch: master
- type: git
  repo: git@github.com:enominecn/enominecn.github.io.git 
  branch: master
```