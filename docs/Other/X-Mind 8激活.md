>**最近**电脑反应太慢，然后重装了一下系统，结果电脑反应速度是变快了，但是之前装的好多软件也都没了。今天在打算用X-Mind画思维导图的时候，发现还没进行安装，正好趁这个机会把安装激活过程记录一下。

* 教程基于 XMind 8 Update 8 (R3.7.8.201807240049)版本进行讲解。

### 1.XMind 8 下载：

官网下载地址：https://www.xmind.cn/
百度网盘下载地址：https://pan.baidu.com/s/1NI66-XMK_1ggsS4INOhFOw 
提取码：r6yk

### 2.XMind 8安装

无脑式安装，一路next到底，到最后点击Finish完成安装

### 3.激活（重点来了）
* 首先下载激活插件：XMindCrack.jar
百度网盘下载地址：https://pan.baidu.com/s/1OaSY8fIBYKtLwfHgbEBU6Q
提取码：wldd
* 将下载下来的jar包拷贝到XMind 8安装目录的bin路径下
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019071913043783.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dueF81MjA1NQ==,size_16,color_FFFFFF,t_70)
* 用记事本或其他文本编辑器打开安装目录下的XMind.ini文件
* 在XMind.ini 文件最后添加-javaagent，然后加上XMindCrack.jar的绝对路径。

修改后的XMind.ini文件内容
```
-configuration
@user.home/Application Data/XMind/configuration-cathy_win32-R3.7.8.201807240049
-data
@user.home/Application Data/XMind/workspace-cathy
-startup
plugins/org.eclipse.equinox.launcher_1.3.200.v20160318-1642.jar
--launcher.library
plugins/org.eclipse.equinox.launcher.win32.win32.x86_1.1.400.v20160518-1444
--launcher.defaultAction
openFile
-eclipse.keyring
@user.home/Application Data/XMind/secure_storage
-vmargs
-Dfile.encoding=UTF-8
-Xms128m
-Xmx1024m
-XX:MaxPermSize=256m
-javaagent:D:\XMind\XMindCrack.jar     // 改成你自己的安装目录

```
* 打开 XMind, 点击帮助 ----> 序列号，然后输入以下序列号 ，邮箱随便填![在这里插入图片描述](https://img-blog.csdnimg.cn/20190719131232169.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dueF81MjA1NQ==,size_16,color_FFFFFF,t_70)
输入下述序列号：

```
XAka34A2rVRYJ4XBIU35UZMUEEF64CMMIYZCK2FZZUQNODEKUHGJLFMSLIQMQUCUBXRENLK6NZL37JXP4PZXQFILMQ2RG5R7G4QNDO3PSOEUBOCDRYSSXZGRARV6MGA33TN2AMUBHEL4FXMWYTTJDEINJXUAV4BAYKBDCZQWVF3LWYXSDCXY546U3NBGOI3ZPAP2SO3CSQFNB7VVIY123456789012345
```

### 4.激活完成
激活后会看到到期时间变成2100年（80年够不够老哥，手动滑稽）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190719131618411.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dueF81MjA1NQ==,size_16,color_FFFFFF,t_70)
如果上述方法激活失败的话，可能是由于版本的原因，你可以到按1.中百度网盘的地址去下载和我相同的版本

---------------
**------ 感谢您的阅读 ------**
*如果您认为文章对您有所帮助的话，劳烦您动动您可爱的小手点个赞，您的认同是我创作做大的动力!*