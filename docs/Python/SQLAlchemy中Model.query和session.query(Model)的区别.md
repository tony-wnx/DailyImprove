
>今天在看以前的代码的时候发现了一个问题，就是在进行数据查询的时候有的地方用到的写法不一致，虽然处理的结果都没有问题，但是秉着一致的原则（也可能是强迫症...）还是决定将用法统一一下。
> 
>首先用到的两种写法分别是：
>1. result = SomeModel.query.filter(...)
>2. result = session.query(SomeModel).filter(...)
>
>由于当时写代码的时候也没有太在意，今天回过头来看时，发现它们两个还是有些差别的，正好趁着这个机会查了一下它们的区别，在此总结一下，以便之后复习回忆。

### 1. 首先我们假定有这么一个需求：

我们需要 Table_A、Table_B、Table_C多个表关联查询，但是我们最终只需要Table_A中的所有字段，这时该怎样进行查询？

**方式一：**

```
result = session.query(Model_A.col1,Model_A.col2... ...,Model_B).join(Model_B,Model_B.id == Model_A.id).filter(...)
```
这样你只需要把要查询的所有字段全部明确列出即可，但是这样可能会有个问题，如果表中的字段特别多的话那是不是要在这里写一大堆东西，这岂不是很麻烦？这时使用第二种方式会方便很多。

**方式二：**

```
result = Model_A.query.join(Model_B,Model_B.id == Model_A.id).filter(...)
```
这种方式会返回一个Model_A的对象，包含该表中所有字段，不包含其他表中的任何字段，而且你不需要再将需要的字段一一列出，是不是方便很多。
当然，你一可以根据需要只查询指定的字段，这时你可以使用下面这种方式：

**查询指定字段：**

```
result = Model_A.query.join(Model_B,Model_B.id == Model_A.id).filter(...).with_entities(Model_A.col1, Model_A.col2，Model_B.col1，Model_B.col2)
```

### 2.源码解析：
在 Flask-SQLAlchemy 提供的 Model 对象中，可以使用 Model.query 这样的语法来直接得到一个查询对象，这是由于 Flask-SQLAlchemy 中存在一个 _QueryProperty 类，每次调用 Model.__get__ 时，会自动生成一个基于当前 session 的 query 对象。

```
// 模型
class CircleClasscircle(db.Model):
		... ...

// db.Model
class SQLAlchemy(object):
		    def __init__(self, app=None, use_native_unicode=True, session_options=None,
                 metadata=None, query_class=BaseQuery, model_class=Model,
                 engine_options=None):

		        self.use_native_unicode = use_native_unicode
		        self.Query = query_class
		        self.session = self.create_scoped_session(session_options)
		        self.Model = self.make_declarative_base(model_class, metadata)
		        self._engine_lock = Lock()
		        self.app = app
		        self._engine_options = engine_options or {}
		        _include_sqlalchemy(self, query_class)

		        if app is not None:
		            self.init_app(app)

		def make_declarative_base(self, model, metadata=None):
				... ... 
				... ...
				model.query = _QueryProperty(self)
        		return model

class _QueryProperty(object):
    def __init__(self, sa):
        self.sa = sa

    def __get__(self, obj, type):
        try:
            mapper = orm.class_mapper(type)
            if mapper:
                return type.query_class(mapper, session=self.sa.session())
        except UnmappedClassError:
            return None
```

### 3.案例解析：

```
// 部分代码，打印原生sql语句
@bp.route("/")
def test():
    baby_id = '57723c14cf7a42e89dbc694b98231e68'

    sql = CircleClasscircle.query.join(
        CircleStory, CircleClasscircle.id == CircleStory.circle_id).filter(
        CircleStory.baby_id == baby_id)

    sql2 = CircleClasscircle.query.join(
        CircleStory,
        CircleClasscircle.id == CircleStory.circle_id).filter(
        CircleStory.baby_id == baby_id).with_entities(
            CircleClasscircle.id,
        CircleClasscircle.baby_id)

    sql3 = db.session.query(CircleClasscircle,
        CircleStory).join(CircleStory,
        CircleClasscircle.id == CircleStory.circle_id).filter(
        CircleStory.baby_id == baby_id).order_by(
        CircleClasscircle.create_date.desc())


    print(sql)
    print("===================")
    print(sql2)
    print("===================")
    print(sql3)
```
**打印sql语句注意点：**
不要在检索语句的最后添加.all()或.first()等类似的方法，否则打印SQL语句失败。

```
// 打印失败（不要这样写）
 sql = CircleClasscircle.query.join(CircleStory, CircleClasscircle.id==CircleStory.circle_id).filter(... ...).all()   
 print(sql)
 
// 打印结果：[<CircleClasscircle c31731546399969124>] ，打印的是查询结果
```
**sql1、sql2、sql3的打印结果**

```
// 1.打印出的sql1的结果： 检索结果是CircleClasscircle表中的所有字段
SELECT
	classcircle.id AS id,
	... ... 省略
FROM
	classcircle
	INNER JOIN story ON classcircle.id = story.circle_id 
WHERE
	story.baby_id = % ( baby_id_1 ) s

// 2.打印出的sql2的结果： 检索结果是指定的字段
SELECT
	classcircle.id AS classcircle_id,
	classcircle.baby_id AS classcircle_baby_id 
FROM
	classcircle
	INNER JOIN story ON classcircle.id = story.circle_id 
WHERE
	story.baby_id = % ( baby_id_1 ) s
	
// 3.打印出的sql3的结果：检索结果是CircleClasscircle和CircleStory两个表中的所有字段
SELECT
	classcircle.id AS classcircle_id,
	... ... 省略
	story.id AS story_id,
	... ... 省略
FROM
	classcircle
	INNER JOIN story ON classcircle.id = story.circle_id 
WHERE
	story.baby_id = % ( baby_id_1 ) s 
ORDER BY
	classcircle.create_date DESC
	
```
