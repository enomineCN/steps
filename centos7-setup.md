---
title: CentOS7 Setup
date: 2016-12-01 11:12:22
tags: 
- CentOS7
---
```bash
#!/bin/bash

# 进入 screen

screen

#添加用户
adduser deploy

# 修改密码
passwd deploy


# 允许 deploy su 到 root
gpasswd -a deploy wheel

# 添加登录公钥
ssh-copy-id deploy@SERVER_IP_ADDRESS


vi /etc/ssh/sshd_config
# 禁用 root 登录
PermitRootLogin no
# 禁用密码登录
PasswordAuthentication no
# 修改登录端口
Port 2121
# 重启服务
systemctl reload sshd

cp /usr/lib/firewalld/services/ssh.xml /etc/firewalld/services/
vi /etc/firewalld/services/ssh.xml
firewall-cmd --reload

# !!!!! 连接确认


yum -y update

echo "your host name" > /etc/hostname
echo "x.x.x.x your host name" >> /etc/hosts

reboot

timedatectl set-timezone Asia/Shanghai
# 设置 UTC 时间
timedatectl set-local-rtc 0

yum groupinstall 'Development Tools'

# install external release
yum -y install epel-release

yum -y install nginx

# required by pillow
yum -y install freetype-devel libjpeg-devel libpng-devel

# required by sentry
yum install libxslt-devel python-devel

yum -y install npm

# required in bcrypt
yum -y install libffi-devel

# mysqlclient
yum -y install mariadb-devel


# install supervisor
pip install supervisor --pre

#install python3
wget https://www.python.org/ftp/python/3.5.2/Python-3.5.2.tgz
tar xf Python-3.5.2.tgz && cd Python-3.5.2 && ./configure && make && make install


su deploy
cd ~
# 重建 ssh keys (todo)

# 创建虚拟环境
pyvenv venv3

echo_supervisord_conf > supervisord.conf
```