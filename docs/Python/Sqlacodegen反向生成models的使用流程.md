
>最近在折腾自己的博客，把原来印象笔记中记录一些内容慢慢整理发布到自己的博客中，方便以后复习回忆
### 1.安装SQLAcodegen     

pip install sqlacodegen

### 2.执行以下命令

**模板：**

```
sqlacodegen mysql://root:123456@127.0.0.1:3306/test > models.py

该命令的含义是把当前配置的test数据库下的所有表结构反向生成model,并将生成结果写入当前目录下生成的models.py文件中
```

### 3.如果是python3的情况
会报错No module named 'MySQLdb'，这时执行下述命令：

```
pip install pymysql
```

进入虚拟环境对应的目录，F:\Git_Repository\Envs\project_name\Lib\site-packages\sqlacodegen，找到__init__.py文件，在里面手动添加下面的代码：

```
import pymysql

pymysql.install_as_MySQLdb()
```
再次执行，执行成功！