方法一：
```
DBSession = scoped_session(sessionmaker())
class _Base(object):
    query = DBSession.query_property()

Base = declarative_base(cls=_Base)

class SomeModel(Base):
    key   = Column(Unicode, primary_key=True)
    value = Column(Unicode)

# When querying
result = SomeModel.query.filter(...)
```

方法二：

```
DBSession = scoped_session(sessionmaker())
Base = declarative_base()

class SomeModel(Base):
    key   = Column(Unicode, primary_key=True)
    value = Column(Unicode)

# When querying
session = DBSession()
result = session.query(SomeModel).filter(...)
```

>* 在上面的代码中，没有区别。这是因为，在第一个例子的第3行：
>查询属性显式绑定到DBSession
>没有传递给query_property的自定义Query对象
>
>* 在第一个示例中定义模型之前，必须有一个会话可用，这可能会因应用程序的结构而有问题。
>* 如果您需要自定义查询，将自动添加查询参数添加到所有查询中，那么只有第一个示例将允许。从sqlalchemy.orm.query.Query继承的自定义查询类可以作为参数传query_property。
即使模型对象具有在其上定义的自定义查询属性，则在使用session.query进行查询时也不会使用该属性，如第二个示例中的最后一行。这意味着如果您需要自定义查询类，第一个示例就是唯一的选项。