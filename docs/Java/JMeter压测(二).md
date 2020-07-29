## JMeter压测

#### JMeter中JSON Extractor用法

* https://www.cnblogs.com/Zhan-W/p/10985816.html
* https://www.cnblogs.com/loveapple/p/9852456.html
* https://blog.csdn.net/weixin_41665637/article/details/86482477

#### JMeter进行多用户并发压力测试

* https://www.cnblogs.com/mawenqiangios/p/8321047.html
* https://blog.csdn.net/fantasic_van/article/details/90769356

#### JMeter性能测试，完整入门篇

* https://blog.csdn.net/u012111923/article/details/80705141

#### JMeter的Html报告汉化及解析

* https://smooth.blog.csdn.net/article/details/100769262

#### JMeter聚合报告参数分析

* https://blog.csdn.net/automationwei/article/details/80734178

#### JMeter压测报错:import com.alibaba.fastjson.* 

```
2020-07-21 17:59:10,673 WARN o.a.j.e.BeanShellPostProcessor: Problem in BeanShell script: org.apache.jorphan.util.JMeterException: Error invoking bsh method: eval	Sourced file: inline evaluation of: ``import com.alibaba.fastjson.JSON; import com.alibaba.fastjson.JSONArray; import  . . . '' : Typed variable declaration : Class: JSONObject not found in namespace
2020-07-21 17:59:10,673 WARN o.a.j.e.BeanShellPostProcessor: Problem in BeanShell script: org.apache.jorphan.util.JMeterException: Error invoking bsh method: eval	Sourced file: inline evaluation of: ``import com.alibaba.fastjson.JSON; import com.alibaba.fastjson.JSONArray; import  . . . '' : Typed variable declaration : Class: JSONObject not found in namespace
2020-07-21 17:59:10,673 INFO o.a.j.t.JMeterThread: Thread is done: 登录线程 1-6
2020-07-21 17:59:10,674 INFO o.a.j.t.JMeterThread: Thread is done: 登录线程 1-8
2020-07-21 17:59:10,674 INFO o.a.j.t.JMeterThread: Thread finished: 登录线程 1-6
2020-07-21 17:59:10,674 ERROR o.a.j.u.BeanShellInterpreter: Error invoking bsh method: eval	Sourced file: inline evaluation of: ``import com.alibaba.fastjson.JSON; import com.alibaba.fastjson.JSONArray; import  . . . '' : Typed variable declaration : Class: JSONObject not found in namespace
2020-07-21 17:59:10,674 INFO o.a.j.t.JMeterThread: Thread finished: 登录线程 1-8
2020-07-21 17:59:10,674 WARN o.a.j.e.BeanShellPostProcessor: Problem in BeanShell script: org.apache.jorphan.util.JMeterException: Error invoking bsh method: eval	Sourced file: inline evaluation of: ``import com.alibaba.fastjson.JSON; import com.alibaba.fastjson.JSONArray; import  . . . '' : Typed variable declaration : Class: JSONObject not found in namespace
2020-07-21 17:59:10,674 ERROR o.a.j.u.BeanShellInterpreter: Error invoking bsh method: eval	Sourced file: inline evaluation of: ``import com.alibaba.fastjson.JSON; import com.alibaba.fastjson.JSONArray; import  . . . '' : Typed variable declaration : Class: JSONObject not found in namespace
2020-07-21 17:59:10,674 INFO o.a.j.t.JMeterThread: Thread is done: 登录线程 1-5
2020-07-21 17:59:10,674 WARN o.a.j.e.BeanShellPostProcessor: Problem in BeanShell script: org.apache.jorphan.util.JMeterException: Error invoking bsh method: eval	Sourced file: inline evaluation of: ``import com.alibaba.fastjson.JSON; import com.alibaba.fastjson.JSONArray; import  . . . '' : Typed variable declaration : Class: JSONObject not found in namespace
2020-07-21 17:59:10,674 INFO o.a.j.t.JMeterThread: Thread finished: 登录线程 1-5
2020-07-21 17:59:10,674 INFO o.a.j.t.JMeterThread: Thread is done: 登录线程 1-4
2020-07-21 17:59:10,674 INFO o.a.j.t.JMeterThread: Thread finished: 登录线程 1-4
2020-07-21 17:59:10,675 ERROR o.a.j.u.BeanShellInterpreter: Error invoking bsh method: eval	Sourced file: inline evaluation of: ``import com.alibaba.fastjson.JSON; import com.alibaba.fastjson.JSONArray; import  . . . '' : Typed variable declaration : Class: JSONObject not found in namespace
2020-07-21 17:59:10,675 WARN o.a.j.e.BeanShellPostProcessor: Problem in BeanShell script: org.apache.jorphan.util.JMeterException: Error invoking bsh method: eval	Sourced file: inline evaluation of: ``import com.alibaba.fastjson.JSON; import com.alibaba.fastjson.JSONArray; import  . . . '' : Typed variable declaration : Class: JSONObject not found in namespace
2020-07-21 17:59:10,675 INFO o.a.j.t.JMeterThread: Thread is done: 登录线程 1-3
2020-07-21 17:59:10,675 INFO o.a.j.t.JMeterThread: Thread finished: 登录线程 1-3
2020-07-21 17:59:10,676 ERROR o.a.j.u.BeanShellInterpreter: Error invoking bsh method: eval	Sourced file: inline evaluation of: ``import com.alibaba.fastjson.JSON; import com.alibaba.fastjson.JSONArray; import  . . . '' : Typed variable declaration : Class: JSONObject not found in namespace
2020-07-21 17:59:10,676 WARN o.a.j.e.BeanShellPostProcessor: Problem in BeanShell script: org.apache.jorphan.util.JMeterException: Error invoking bsh method: eval	Sourced file: inline evaluation of: ``import com.alibaba.fastjson.JSON; import com.alibaba.fastjson.JSONArray; import  . . . '' : Typed variable declaration : Class: JSONObject not found in namespace
2020-07-21 17:59:10,676 INFO o.a.j.t.JMeterThread: Thread is done: 登录线程 1-7
2020-07-21 17:59:10,676 INFO o.a.j.t.JMeterThread: Thread finished: 登录线程 1-7
2020-07-21 17:59:10,676 ERROR o.a.j.u.BeanShellInterpreter: Error invoking bsh method: eval	Sourced file: inline evaluation of: ``import com.alibaba.fastjson.JSON; import com.alibaba.fastjson.JSONArray; import  . . . '' : Typed variable declaration : Class: JSONObject not found in namespace
2020-07-21 17:59:10,676 WARN o.a.j.e.BeanShellPostProcessor: Problem in BeanShell script: org.apache.jorphan.util.JMeterException: Error invoking bsh method: eval	Sourced file: inline evaluation of: ``import com.alibaba.fastjson.JSON; import com.alibaba.fastjson.JSONArray; import  . . . '' : Typed variable declaration : Class: JSONObject not found in namespace
2020-07-21 17:59:10,677 INFO o.a.j.t.JMeterThread: Thread is done: 登录线程 1-1
2020-07-21 17:59:10,677 INFO o.a.j.t.JMeterThread: Thread finished: 登录线程 1-1
2020-07-21 17:59:10,677 INFO o.a.j.e.StandardJMeterEngine: Notifying test listeners of end of test
2020-07-21 17:59:10,677 INFO o.a.j.s.FileServer: Close: C:/Users/yangxiongqian/Desktop/压测/txhw/login.csv
2020-07-21 17:59:10,678 INFO o.a.j.g.u.JMeterMenuBar: setRunning(false, *local*)
```

* 解决办法

  下载fastJson.jar包并放入JMeter安装目录，D:\apache-jmeter-5.2.1\lib\ext中，重新启动JMeter，问题解决

  ![Image](C:\Users\yangxiongqian\Desktop\Image.png)

#### JMeter携带Cookie的请求，显示[no cookies]

![image-20200722144636997](C:\Users\yangxiongqian\AppData\Roaming\Typora\typora-user-images\image-20200722144636997.png)

报错内容：

```
2020-07-22 14:37:23,390 INFO o.a.j.e.StandardJMeterEngine: Running the test!
2020-07-22 14:37:23,390 INFO o.a.j.s.SampleEvent: List of sample_variables: []
2020-07-22 14:37:23,390 INFO o.a.j.t.TestPlan: added D:\apache-jmeter-5.2.1\lib\freemarker-2.3.29.jar to classpath
2020-07-22 14:37:23,391 INFO o.a.j.g.u.JMeterMenuBar: setRunning(true, *local*)
2020-07-22 14:37:23,478 INFO o.a.j.e.StandardJMeterEngine: Starting ThreadGroup: 1 : 请求线程
2020-07-22 14:37:23,478 INFO o.a.j.e.StandardJMeterEngine: Starting 10 threads for group 请求线程.
2020-07-22 14:37:23,478 INFO o.a.j.e.StandardJMeterEngine: Thread will continue on error
2020-07-22 14:37:23,478 INFO o.a.j.t.ThreadGroup: Starting thread group... number=1 threads=10 ramp-up=0 delayedStart=false
2020-07-22 14:37:23,482 INFO o.a.j.t.JMeterThread: Thread started: 请求线程 1-1
2020-07-22 14:37:23,482 INFO o.a.j.s.FileServer: Stored: C:/Users/yangxiongqian/Desktop/压测/txhw/user_info.csv
2020-07-22 14:37:23,484 INFO o.a.j.t.JMeterThread: Thread started: 请求线程 1-2
2020-07-22 14:37:23,487 INFO o.a.j.t.JMeterThread: Thread started: 请求线程 1-3
2020-07-22 14:37:23,489 INFO o.a.j.t.JMeterThread: Thread started: 请求线程 1-4
2020-07-22 14:37:23,492 INFO o.a.j.t.JMeterThread: Thread started: 请求线程 1-5
2020-07-22 14:37:23,494 INFO o.a.j.t.JMeterThread: Thread started: 请求线程 1-6
2020-07-22 14:37:23,497 INFO o.a.j.t.JMeterThread: Thread started: 请求线程 1-7
2020-07-22 14:37:23,499 INFO o.a.j.p.h.c.HC4CookieHandler: Not storing invalid cookie: <JOIN_TOKEN=eyJhbGciOiJIUzUxMiJ9.eyJBVVRPX0xPR0lOIjowLCJKT0lOX1VTRVIiOiI1ZjE3ZGVhMzI4Yjg0ZDBlZTA4MWIwOTMifQ.oRZNwEC1Ku2RAt7zCY-IeDyPKeaXStgy0_UJi9dzthXuIMainlMYpYlWQKz9GGcG84bZ0GXhnWIIX8gzGJDcBA;Path=/;Domain=.changyou.com;Expires=Wed, 22-Jul-2020 08:37:23 GMT;Max-Age=7200> for URL http://local-activity.changyou.com/teampk/mybackpack (Illegal 'domain' attribute "changyou.com". Domain of origin: "local-activity.changyou.com/teampk")
2020-07-22 14:37:23,500 INFO o.a.j.p.h.c.HC4CookieHandler: Not storing invalid cookie: <JOIN_TOKEN=eyJhbGciOiJIUzUxMiJ9.eyJBVVRPX0xPR0lOIjowLCJKT0lOX1VTRVIiOiI1ZjE3ZGVhMzI4Yjg0ZDBlZTA4MWIwOTQifQ.L-sjUsEEWJFvn2v91c8YdwL8n7aaMHI4X_B8fKOV2OKosmPwyq71qNuSI_JiQJMpfgBQfDFUniy87e4nRV4Eyw;Path=/;Domain=.changyou.com;Expires=Wed, 22-Jul-2020 08:37:23 GMT;Max-Age=7200> for URL http://local-activity.changyou.com/teampk/mybackpack (Illegal 'domain' attribute "changyou.com". Domain of origin: "local-activity.changyou.com/teampk")
2020-07-22 14:37:23,500 INFO o.a.j.t.JMeterThread: Thread started: 请求线程 1-8
2020-07-22 14:37:23,502 INFO o.a.j.t.JMeterThread: Thread is done: 请求线程 1-2
2020-07-22 14:37:23,502 INFO o.a.j.t.JMeterThread: Thread finished: 请求线程 1-2
2020-07-22 14:37:23,502 INFO o.a.j.t.JMeterThread: Thread is done: 请求线程 1-1
2020-07-22 14:37:23,502 INFO o.a.j.t.JMeterThread: Thread finished: 请求线程 1-1
2020-07-22 14:37:23,505 INFO o.a.j.t.JMeterThread: Thread started: 请求线程 1-9
2020-07-22 14:37:23,508 INFO o.a.j.t.ThreadGroup: Started thread group number 1
2020-07-22 14:37:23,508 INFO o.a.j.e.StandardJMeterEngine: All thread groups have been started
2020-07-22 14:37:23,509 INFO o.a.j.t.JMeterThread: Thread started: 请求线程 1-10
2020-07-22 14:37:23,513 INFO o.a.j.p.h.c.HC4CookieHandler: Not storing invalid cookie: <JOIN_TOKEN=eyJhbGciOiJIUzUxMiJ9.eyJBVVRPX0xPR0lOIjowLCJKT0lOX1VTRVIiOiI1ZjE3ZGVhMzI4Yjg0ZDBlZTA4MWIwOTYifQ.mkjNYxTQn_CGkPLwOrS69Mu0xu38k2aDhph_e4u2xuEV5wULdsTetXPKVIyZ4opGPzzj3-dKCO-DJMiB5Vx_JA;Path=/;Domain=.changyou.com;Expires=Wed, 22-Jul-2020 08:37:23 GMT;Max-Age=7200> for URL http://local-activity.changyou.com/teampk/mybackpack (Illegal 'domain' attribute "changyou.com". Domain of origin: "local-activity.changyou.com/teampk")
2020-07-22 14:37:23,513 INFO o.a.j.p.h.c.HC4CookieHandler: Not storing invalid cookie: <JOIN_TOKEN=eyJhbGciOiJIUzUxMiJ9.eyJBVVRPX0xPR0lOIjowLCJKT0lOX1VTRVIiOiI1ZjE3ZGVhMzI4Yjg0ZDBlZTA4MWIwOTUifQ.ZyPhkmP6BkcQwwU_F6KNiCyn54CYHGDbPjJsp-nsVgJhAZ9AxmtoVP6tsmA5vH41p9Gi3MU_Fs0l2DNE0oxI4Q;Path=/;Domain=.changyou.com;Expires=Wed, 22-Jul-2020 08:37:23 GMT;Max-Age=7200> for URL http://local-activity.changyou.com/teampk/mybackpack (Illegal 'domain' attribute "changyou.com". Domain of origin: "local-activity.changyou.com/teampk")
2020-07-22 14:37:23,514 INFO o.a.j.t.JMeterThread: Thread is done: 请求线程 1-3
```

* 问题原因

  * 重点看：Illegal 'domain' attribute "changyou.com". Domain of origin: "local-activity.changyou.com/teampk"

  * 从上边看，原始域名为：local-activity.changyou.com/teampk，而设置的Cookie域名为：changyou.com，两者不一致导致

    ![image-20200722145444800](C:\Users\yangxiongqian\AppData\Roaming\Typora\typora-user-images\image-20200722145444800.png)

    ![image-20200722145604616](C:\Users\yangxiongqian\AppData\Roaming\Typora\typora-user-images\image-20200722145604616.png)

* 问题解决

  * 去掉上图中通用域名的后缀/teampk

    ![image-20200722150025171](C:\Users\yangxiongqian\AppData\Roaming\Typora\typora-user-images\image-20200722150025171.png)

  * 修改后记得在请求接口中添加上前缀/teampk

    ![image-20200722150121533](C:\Users\yangxiongqian\AppData\Roaming\Typora\typora-user-images\image-20200722150121533.png)