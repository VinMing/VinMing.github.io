---
layout:     post
title:      SQLAlchemy ORM 
subtitle:    "\"sqlalchemy orm\""
date:       2020-01-28
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - ORM

---
## 前言

使用orm控制数据库

sqlalchemy的orm的高级用法，分组，排序，聚合等方法

从SQLAlchemy的“缓存”问题说起


## 环境
#### 系统环境
```text
Distributor ID:	Ubuntu
Description:	Ubuntu 18.04.4 LTS
Release:	18.04
Codename:	bionic
Linux version :       5.3.0-46-generic ( buildd@lcy01-amd64-013 ) 
Gcc version:         7.5.0  ( Ubuntu 7.5.0-3ubuntu1~18.04 )
```


#### 软件信息
```text
version : 	
     Python 3.7.9
     SQLAlchemy 1.4.11
```

## 正文

#### 1、SQLAlchemy的orm的高级用法，分组，排序，聚合等方法

##### models.py

```python
# pip install  sqlalchemy
import datetime
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Integer, String, Text, ForeignKey, DateTime, UniqueConstraint, Index
from sqlalchemy.orm import relationship
Base = declarative_base()

class Users(Base):
    __tablename__ = 'users'  # 数据库表名称
    id = Column(Integer, primary_key=True)  # id 主键
    name = Column(String(32), index=True, nullable=False)  # name列，索引，不可为空
    age = Column(Integer)
    #email = Column(String(32), unique=True)
    #datetime.datetime.now#不能加括号，加了括号，以后永远是当前时间
    #ctime = Column(DateTime, default=datetime.datetime.now)
    #extra = Column(Text, nullable=True)

    __table_args__ = (
        # UniqueConstraint('id', 'name', name='uix_id_name'), #联合唯一
        # Index('ix_id_name', 'name', 'email'), #索引
    )

    def __repr__(self):  # 作用类似于__str__, 但是__repr__的作用更加底层，__str__则打印不出来
        return self.name


def init_db():
    """
    根据类创建数据库表
    :return:
    """
    engine = create_engine(
        "mysql+pymysql://root:123456@127.0.0.1:3306/python13?charset=utf8",
        #"什么数据库（mysql,orcal）+用什么取链接数据库（pymysql）://数据库用户名:密码@mysqlip:端口/数据库名？charset=字符集"
        max_overflow=0,  # 超过连接池大小外最多创建的连接
        pool_size=5,  # 连接池大小
        pool_timeout=30,  # 池中没有线程最多等待的时间，否则报错
        pool_recycle=-1  # 多久之后对线程池中的线程进行一次连接的回收（重置）
    )

    Base.metadata.create_all(engine)

def drop_db():
    """
    根据类删除数据库表
    :return:
    """
    engine = create_engine(
         "mysql+pymysql://root:123456@127.0.0.1:3306/python13?charset=utf8",
        max_overflow=0,  # 超过连接池大小外最多创建的连接
        pool_size=5,  # 连接池大小
        pool_timeout=30,  # 池中没有线程最多等待的时间，否则报错
        pool_recycle=-1  # 多久之后对线程池中的线程进行一次连接的回收（重置）
    )

    Base.metadata.drop_all(engine)

if __name__ == '__main__':
    drop_db()
    init_db()
```

##### orm的and，or, between, 取反查询，like，分组，排序，聚合

```python
from sqlalchemy.orm import sessionmaker
from sqlalchemy import create_engine
from models import Users
#"mysql+pymysql://root@127.0.0.1:3306/aaa"
engine = create_engine("mysql+pymysql://root:123456@127.0.0.1:3306/python13", max_overflow=0, pool_size=5)
Connection = sessionmaker(bind=engine)

# 每次执行数据库操作时，都需要创建一个Connection
session = Connection()

# 条件
ret =  session.query(Users).filter_by(name = "esb").all()

#表达式，and 条件链接
ret  = session.query(Users).filter(Users.name == "sb",Users.age ==14).first()
# print(ret.age,ret.name)

# 表示的between（包括头尾），条件,30<=age<=40
ret = session.query(Users).filter(Users.age.between(30,40)).all()
# print(ret)


# sql查询的in_操作,相当于django中的__in
ret =session.query(Users).filter(Users.id.in_([9,11,13])).all()
# print(ret)


# # sql查询取反
ret1 = session.query(Users).filter(~Users.id.in_([9,11,13])).all()
# print(ret1)


#or查询 ，or和and ,做整合
# 想要使用，的向导入
from sqlalchemy import or_, and_
#
ret = session.query(Users).filter(or_(Users.id == 9, Users.name=="jsb")).all()
ret = session.query(Users).filter(and_(Users.id == 9,Users.name=="lsb1")).all()

# 也可以包裹在一起使用
ret =  session.query(Users).filter(or_(
    Users.id == 9,
    and_(Users.name=="jsb",Users.id==13),

    )
).all()


# like查询,模糊配置
#必须以b开头
ret = session.query(Users).filter(Users.name.like("b%")).all()

# #第二字母是b
ret = session.query(Users).filter(Users.name.like("_b%")).all()

#不以b开头
ret = session.query(Users).filter(~Users.name.like("b%")).all()


#排序
#desc从大到小排序
ret = session.query(Users).filter(Users.id>1).order_by(Users.id.desc()).all()

#asc从小到大排序
ret = session.query(Users).filter(Users.id>1).order_by(Users.id.asc()).all()

#多条件排序,先以年纪从大到小排，如果年龄相同，再以id从小到大排
ret = session.query(Users).filter(Users.id>1).order_by(Users.age.desc(),Users.id.asc()).all()
# print(ret)


#分组查询
ret  = session.query(Users).group_by(Users.name).all()

# 再分组的时候如果要用聚合操作，就要导入func
from sqlalchemy.sql import func  # func 就类似于django的聚合查询

#选出组内最小年龄要大于等于30的组
ret  = session.query(Users).group_by(Users.name).having(func.min(Users.age)>=30).all()

#选出组内最小年龄要大于等于30的组,查询组内的最小年龄，最大年纪，年纪之和，
ret = session.query(
    func.min(Users.age),
    func.max(Users.age),
    func.sum(Users.age),
    Users.name
).group_by(Users.name).having(func.min(Users.age)>=30).all()
print(ret)
```

#### 2、从SQLAlchemy的“缓存”问题说起

###### 问题描述

最近在排查一个问题，为了方便说明，我们假设现在有如下一个API：

```python
@app.route("/sqlalchemy/test", methods=['GET'])
def sqlalchemy_test_api():
    data = {}
    # 获取商品价格
    product = Product.query.get(1)
    data['old_price'] = product.present_price
    # 休眠10秒，等待外部修改价格
    time.sleep(10)
    product = Product.query.get(1)
    data['new_price'] = product.present_price
    return jsonify(status='ok', data=data)
```

这里我们的后台使用了[Flask](https://link.jianshu.com?t=http://flask.pocoo.org/)作为服务端框架，[SQLAlchemy](https://link.jianshu.com?t=https://www.sqlalchemy.org/)作为数据库ORM框架。Product是一张商品表的ORM模型，假设原来id=1的商品价格为10，在程序休眠的10秒内价格被修改为20，那么你觉得返回的结果是多少？

old_price显然是10，那么new_price呢？讲道理的话由于外部修改价格为20了，同时程序在sleep后立刻又query了一次，你可能觉得new_price应该是20。但结果并不是，真实测试的结果是10，给人感觉就像是SQLAlchemy“缓存”了上一次的结果。

另外在测试的过程还发现一个现象，虽然在第一次API调用时两个price都是10，但是在第二次调用API时，读到的price是20。也就是说，在一个新的API开始时，之前“缓存”的结果被清除了。

###### SQLAlchemy的session状态管理

之前我们提出了一个猜测：第二次查询是否“缓存”了第一次查询。为了验证这个猜想，我们可以把`SQLALCHEMY_ECHO`这个配置项打开，这是个全局配置项，官方文档定义如下：

| 配置项            | 说明                                                         |
| ----------------- | :----------------------------------------------------------- |
| `SQLALCHEMY_ECHO` | If set to True SQLAlchemy will log all the statements issued to stderr which can be useful for debugging. |

在这个配置项打开的情况下，我们可以看到查询语句输出到终端下。我们再次调用API，可以发现第一次查询会输出类似`SELECT * FROM product WHERE id = 1`的语句，而第二次查询则没有这样的输出。如此看来，SQLAlchemy确实缓存了上次的结果，在第二次查询的时候直接使用了上次的结果。

实际上，当执行第一句`product = Product.query.get(1)`时，product这个对象处于持久状态(persistent)了，我们可以通过一些工具看到ORM对象目前处于的状态。详细的状态列表可在[官方文档](https://link.jianshu.com?t=http://docs.sqlalchemy.org/en/latest/orm/session_state_management.html#quickie-intro-to-object-states)中找到。

```python
>>> from sqlalchemy import inspect
>>> insp = inspect(product)
>>> insp.persistent
True
>>> product.__dict__
{
  'id': 1, 'present_price': 10,
  '_sa_instance_state': <sqlalchemy.orm.state.InstanceState object at 0x1106a3350>,
}

```

为了清除该对象的缓存，程度从低到高有下面几种做法。`expire`会清除对象里缓存的数据，这样下次查询时会直接从数据库进行查询。`refresh`不仅清除对象里缓存的数据，还会立刻触发一次数据库查询更新数据。`expire_all`的效果和`expire`一样，只不过会清除session里所有对象的缓存。`flush`会把所有本地修改写入到数据库，但没有提交。`commit`不仅把所有本地修改写入到数据库，同时也提交了该事务。

```python
db.session.expire(product)
db.session.refresh(product)
db.session.expire_all()
db.session.flush()
db.session.commit()

```

我们对这几种方法依次做实验，结果发现这5个操作都会让下次查询直接从数据库进行查询，但只有`commit`会读到最新的price。那这个又是什么原因呢，我们已经强制每次查询走数据库，为何还是读到“缓存”的数据。这个就要用数据库的事务隔离机制来解释了。




## 后记

@[TOC](这里写自定义目录标题)

![Image text](/img/Ubuntu_filename.png)

