---
title: git 中文乱码
date: 2017-04-17 09:27:22
tags: 
- git
---
## git config

`git config --global core.quotepath false`

## oh-my-zsh

1、打开`oh-my-zsh`配置文件 `~/.zshrc` 。
2、在文件最后面添加如下代码：

```bash
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8
```

3、重启下终端（terminal）或输入 `source ~/.zshrc`