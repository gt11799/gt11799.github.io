title: Python3+pymysql的utf8的编码问题
date: 2019-04-06 20:42:57
tags: Python
---

Python3弱化了utf8的编码问题，但是倒是有些让我手足无措，不知道该怎么处理编码问题<!--more-->

```python
data = {
    "我": "一个学生"
}
data = json.dumps(data)
sql = "insert into the_table (data) values ('%s')" % data
cursor.execute(sql)
```

发现存的数据应该是`\u6d88`，但是实际上却是`u6d88`，查了好多资料，发现其实解决方式很简单

```python
sql = "insert into the_table (data) values (%s)"
cursor.execute(sql, (data, ))
```

关键点在于，数据的填充交给mysql库，也不要加引号。

感觉手越来越生了。
