## IDEA根据数据表自动生成实体类配置

### IDEA右侧，点击Database

<img src="E:\WorkSpaces\DailyImprove\docs\Java\img\idea_1.png" alt="idea_1"  />

### 按要求填写配置内容

![idea_2](E:\WorkSpaces\DailyImprove\docs\Java\img\idea_2.png)

### 填写完点Test Connection会报错，缺少mysql驱动，点击底部“Download”进行下载，如果下载失败(如图)

![idea_3](E:\WorkSpaces\DailyImprove\docs\Java\img\idea_3.png)

### 手动下载添加驱动

![idea_4_1](E:\WorkSpaces\DailyImprove\docs\Java\img\idea_4_1.png)

![idea_4_2](E:\WorkSpaces\DailyImprove\docs\Java\img\idea_4_2.png)

### 点击Test Connection又会报错

![idea_6](E:\WorkSpaces\DailyImprove\docs\Java\img\idea_6.png)

### 报错意思是：Mysql没有设置时区

```java
// 查询时区,显示SYSTEM说明没有设置时区
mysql> show variables like'%time_zone';
+------------------+--------+
| Variable_name    | Value  |
+------------------+--------+
| system_time_zone |        |
| time_zone        | SYSTEM |
+------------------+--------+
2 rows in set, 1 warning (0.00 sec)
    
// 设置时区
set global time_zone = '+8:00';
```

### 再次测试连接，测试OK

### 添加schema

![idea_8](E:\WorkSpaces\DailyImprove\docs\Java\img\idea_8.png![idea_7](E:\WorkSpaces\DailyImprove\docs\Java\img\idea_7.png)

![idea_8](E:\WorkSpaces\DailyImprove\docs\Java\img\idea_8.png)

### 添加自定义groovy（自带的生成没有注释、没有注解），需要自己定制

![idea_9](E:\WorkSpaces\DailyImprove\docs\Java\img\idea_9.png)

### 生成实体类(生成是可选择生成的地址)

![idea_10](E:\WorkSpaces\DailyImprove\docs\Java\img\idea_10.png)