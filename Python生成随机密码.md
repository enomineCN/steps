---
title: Python生成随机密码
date: 2016-06-22 14:11:25
tags: 
- random
- password
---
# Python生成随机密码

---

今天，工作中需要为客户生成一个8为的随机密码，包含大小写字母和数字，项目中实现如下：

```python
# -*- coding:utf-8 -*-
"""
生成随机密码，包括大小写字母、数字，可以指定密码长度
"""
# 生成随机密码s

from random import choice, sample
import string
def generate_pwd(length=8, chars=string.ascii_letters + string.digits):
    # return ''.join([choice(chars) for _ in range(length)])
    return ''.join(sample(chars, length))
if __name__ == "__main__":
    print(generate_pwd(8))
```


​	

