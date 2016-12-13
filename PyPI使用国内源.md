---
title: PyPI使用国内源
date: 2016-10-26 22:20:33
tags: 
- PyPI
---
# PyPI使用国内源

- 阿里云 http://mirrors.aliyun.com/pypi/simple/
- 中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/ 
- 豆瓣(douban) http://pypi.douban.com/simple/ 
- 清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/
- 中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/

使用方法很简单，直接 -i 加 url 即可！如下：

```
pip install web.py -i http://pypi.douban.com/simple
```

如果有如下报错：

```python
pip install web.py -i http://pypi.douban.com/simple --trusted-host pypi.douban.com
```

如果想配置成默认的源，添加配置文件

- linux下`~/.pip/pip.conf`
- windows下`%HOMEPATH%\pip\pip.ini`

修改内容为：

```
[global]
index-url = http://pypi.douban.com/simple
[install]
trusted-host=pypi.douban
```

