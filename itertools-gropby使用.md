---
title: itertools.gropby使用
date: 2016-11-21 16:36:33
tags: 
- itertools
- gropby
---

----

## 问题背景

本周主要写了各种类型的财务报表，利用python的csv模块进行输出。

在编写财务报表的过程中，出现了这样一种需求。

| 姓名   |  月份  |   电费   |  水费   |  燃气费  |
| ---- | :--: | :----: | :---: | :---: |
| 张三   |  1   | 100.00 |       |       |
| 张三   |  1   |        | 30.00 |       |
| 张三   |  1   |        |       | 40.00 |
| 张三   |  1   |        | 10.00 |       |
| 张三   |  2   | 70.00  |       |       |
| 李四   |  3   | 66.00  |       |       |
| 李四   |  3   |        |       | 20.00 |

类似于这样的报表，要将姓名和月份一致的信息，合并到一条中去。结果像这样：

| 姓名   |  月份  |   电费   |  水费   |  燃气费  |
| ---- | :--: | :----: | :---: | :---: |
| 张三   |  1   | 100.00 | 30.00 | 40.00 |
| 张三   |  2   | 70.00  |       |       |
| 李四   |  3   | 66.00  |       | 20.00 |

我们使用`csv`模块 的 `DictWriter`

表1对应的数据结构为：

```python
[{
    'name': '张三',
    'month': 1,
    'electricity_fee': 100.00,
}, {
    'name': '张三',
    'month': 1,
    'water_fee': 30.00,
}, {
    'name': '张三',
    'month': 1,
    'gas_fee': 40.00,
}, {
    'name': '张三',
    'month': 1,
    'water_fee': 10.00,
}, {
    'name': '张三',
    'month': 2,
    'water_fee': 70.00,
}, {
    'name': '李四',
    'month': 3,
    'electricity_fee': 66.00,
}, {
    'name': '李四',
    'month': 3,
    'gas_fee': 20.00,
}]
```

表2：

```python
[{
    'name': '张三',
    'month': 1,
    'electricity_fee': 100.00,
    'water_fee': 40.00,
    'gas_fee': 40.00,
}, {
    'name': '张三',
    'month': 2,
    'water_fee': 70.00,
}, {
    'name': '李四',
    'month': 3,
    'electricity_fee': 66.00,
    'gas_fee': 20.00,
}]
```

## 如何用简介优雅的代码将表1的数据结构转化为表2？

```python
from itertools import groupby

def get_key(data):
    return data['name'] + str(data['month'])

fees = ('electricity_fee', 'water_fee', 'gas_fee')

rows_out = []
for key, group in groupby(rows, key=get_key):
    row = group[0]
    for fee in fees:
        row[fee] = sum(item[fee] for item in group if fee in item)
        rows_out.append(row)

```