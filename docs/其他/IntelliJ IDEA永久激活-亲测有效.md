>不管是从eclipse还是从其他别的IDE转过来的码哥码姐们，相信你只要你使用过IDEA就一定会和博主一样对它爱不释手。在使用过程中你可能遇到过遇到过这样尴尬的问题，当你刚要打开idea想要狠狠撸它几百行神秘代码的时候，却发现idea的激活失效了，这时你可能会有想砸电脑的冲动。。。莫慌！为了避免这种尴尬，今天我们来聊一聊关于IDEA近乎永久激活的方法（截止2100年到期），废话不多说，接下来开始我们的教程：
* 教程基于 IntelliJ IDEA 2018.2.1 (Ultimate Edition) 版本进行讲解。

### 1.IDEA 下载：

官网下载地址：https://www.jetbrains.com/idea/download/
百度网盘下载地址：https://pan.baidu.com/s/1da9F0jj_i3TdJmm1qYZmHQ  
提取码：1ag6

### 2.IDEA安装

无脑式安装，一路next到底，到最后点击Finish完成安装

### 3.激活（重点来了）

* 首先下载激活插件：JetbrainsCrack-release-enc.jar
百度网盘下载地址：https://pan.baidu.com/s/1v0tn0KHuG6FNShauM2UnYA
提取码：6wys
下载内容包含两个文件：JetbrainsCrack-release-enc.jar、IDEA激活码.txt
* 讲下载下来的jar包拷贝到idea安装目录的bin路径下
![image](https://github.com/tony-wnx/DailyImprove/blob/master/docs/images/copy_to_dir.png)
* 在IDEA的安装目录的bin路径下找到 idea.exe.vmoptions 和 idea64.exe.vmoptions 这两个文件，用记事本打开，在这两个文件的最后各自追加以下内容：
-javaagent:安装路径\bin\JetbrainsCrack-release-enc.jar，修改完后保存退出。
例如我的追加内容为：
-javaagent:D:\IntelliJ IDEA 2018\bin\JetbrainsCrack-release-enc.jar
![image](https://github.com/tony-wnx/DailyImprove/blob/master/docs/images/modify_files.png)
idea64.exe.vmoptions文件中的内容：
```
-Xms128m
-Xmx750m
-XX:ReservedCodeCacheSize=240m
-XX:+UseConcMarkSweepGC
-XX:SoftRefLRUPolicyMSPerMB=50
-ea
-Dsun.io.useCanonCaches=false
-Djava.net.preferIPv4Stack=true
-Djdk.http.auth.tunneling.disabledSchemes=""
-XX:+HeapDumpOnOutOfMemoryError
-XX:-OmitStackTraceInFastThrow
-javaagent:D:\IntelliJ IDEA 2018\bin\JetbrainsCrack-release-enc.jar

```
idea.exe.vmoptions文件中的内容：

```
-server
-Xms128m
-Xmx512m
-XX:ReservedCodeCacheSize=240m
-XX:+UseConcMarkSweepGC
-XX:SoftRefLRUPolicyMSPerMB=50
-ea
-Dsun.io.useCanonCaches=false
-Djava.net.preferIPv4Stack=true
-Djdk.http.auth.tunneling.disabledSchemes=""
-XX:+HeapDumpOnOutOfMemoryError
-XX:-OmitStackTraceInFastThrow
-javaagent:D:\IntelliJ IDEA 2018\bin\JetbrainsCrack-release-enc.jar
```

* 打开IDEA，选择激活码激活，将最开始下载下来的文件：**IDEA激活码.txt** 中的内容全部拷贝到输入框中 ，点击ok完成激活！
![image](https://github.com/tony-wnx/DailyImprove/blob/master/docs/images/input_verifyCode.png)

### 4.激活完成

激活后会看到到期时间变成2100年（80年够不够老哥，手动滑稽）
![image](https://github.com/tony-wnx/DailyImprove/blob/master/docs/images/active_status.png)

### 5.其他激活方法

如果上述方法激活失败的话，可能是由于版本的原因，你可以到按1.中百度网盘的地址去下载和我相同的版本，或者参考其他激活方法：https://blog.csdn.net/zhw0596/article/details/81394870