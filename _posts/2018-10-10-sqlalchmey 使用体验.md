---
layout: post
title: "sqlalchemy 使用体验(一) 安装和链接数据库"
date: 2018-10-10 19:00：00 +0300
categories:
- sqlalchemy,Python
tags:
- sqlalchemy,Python
---
# sqlalchemy 使用体验(一) 安装和链接数据库
sqlalchemy 是python web 最为流行的orm之一，甚至flask 有专门的flask-sqlalchemy 拓展来帮助程序员使用。使用orm的好处有很多这里就不在赘述，反正会来看这种文章的肯定也是知道的。下面就简单写点使用的体验。

## 1 安装
```
    pip install sqlalchemy
```
## 2 
安装完成之后就进入到最基本的使用，python3 基本都自带了sqlite，接下来就以sqlite为例

### 1. 和数据库链接
```python
from sqlalchemy import create_engine
engine = create_engine('sqlite:///blog.db', echo=True)
```
engine 是作为数据库的一个链接，作为一个与数据库交互的核心接口，你可以通过这个接口转换成sql语句和数据库进行交互。

### 2. 建立声明映射
建立连接后，需要建立一个声明映射，通过这个映射可以将python class 与数据库的table 联系起来

```python
from sqlalchemy.ext.declarative import declarative_base
Base = declarative_base(bind=engine)
```
也可以不在Base声明时指定bind ,但那是另一个话题
Base 在声明完成后就可以作为基类被模型类继承，接下来我们写一个用户类
```python
class User(Base):
    __tablename__ = 'users'

    id = Column(Integer, primary_key=True)
    name = Column(String)
    email = Column(String)
    passwd = Column(String)

    def __repr__(self):
        return '<User(id ={}, name={}, email={},passwd={}>'.format(self.id, self.name, self.email, self.passwd)

```

然后使用
```python
Base.metadata.create_all()
```
来创建数据库表
```
python models.py
2018-10-10 12:42:11,759 INFO sqlalchemy.engine.base.Engine SELECT CAST('test plain returns' AS VARCHAR(60)) AS anon_1
2018-10-10 12:42:11,759 INFO sqlalchemy.engine.base.Engine ()
2018-10-10 12:42:11,762 INFO sqlalchemy.engine.base.Engine SELECT CAST('test unicode returns' AS VARCHAR(60)) AS anon_1
2018-10-10 12:42:11,762 INFO sqlalchemy.engine.base.Engine ()
2018-10-10 12:42:11,763 INFO sqlalchemy.engine.base.Engine PRAGMA table_info("users")
2018-10-10 12:42:11,763 INFO sqlalchemy.engine.base.Engine ()
2018-10-10 12:42:11,764 INFO sqlalchemy.engine.base.Engine
CREATE TABLE users (
        id INTEGER NOT NULL,
        name VARCHAR,
        email VARCHAR,
        passwd VARCHAR,
        PRIMARY KEY (id)
)
```

