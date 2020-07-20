## JMeter压测-生成报告

#### 配置环境变量

* D:\apache-jmeter-5.2.1
* 添加环境变量：JMETER_HOME = D:\apache-jmeter-5.2.1
* 将该变量添加到 Path变量中，%JMETER_HOME/bin%

#### 进行压测

> 进入到生成了xxx.jmx的文件夹目录(jmx文件可以使用JMeter录制保存生成)

###### .jtl文件存在

执行命令

```
jmeter -n -t tl_lbqz.jmx -l result.jtl -e -o C:\Users\xxx\Desktop\yace\ResultReport
```

* -n ：以非GUI形式运行Jmeter
* -t ：jmeter测试脚本.jmx 脚本路径 （当前目录可写相对路径 ，不在当前目录使用绝对路径）
* -l ：测试结果.jtl 运行结果保存路径（.jtl）,此文件必须不存在
* -e ：在脚本运行结束后生成html报告
* -o ：用于存放html报告的目录

###### .jtl文件不存在

```
jmeter -g result.jtl -o /ResultReport(可以是相对路径，也可以是绝对路径)
```

* -g : result.jtl 已经存在的.jtl文件的路径。
* -o ：用于存放html报告的目录

#### 报告

执行完命令，会在ResultReport路径中生成相应的报告