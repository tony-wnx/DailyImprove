### 1.@RestController


>@RestController是Spring4之后新加入的注解。原来返回json需要@ResponseBody和@Controller配合。使用@RestController可以替换@ResponseBody和@Controller的组合注解。

* **源码解析**
```
 @Target(value=TYPE)
 @Retention(value=RUNTIME)
 @Documented
 @Controller
 @ResponseBody
 public @interface RestController

A convenience annotation that is itself annotated with @Controller and @ResponseBody.

Types that carry this annotation are treated as controllers where @RequestMapping methods assume @ResponseBody semantics by default.

**NOTE: **@RestController is processed if an appropriate HandlerMapping-HandlerAdapter pair is configured such as the RequestMappingHandlerMapping-RequestMappingHandlerAdapter pair which are the default in the MVC Java config and the MVC namespace.

Since:4.0
```

* **实例解析**
```
@RestController
public class HelloController {

    @RequestMapping(value="/hello",method= RequestMethod.GET)
    public String sayHello(){
        return "hello";
    }
}

================ 等价于 ================

@Controller
@ResponseBody
public class HelloController {

    @RequestMapping(value="/hello",method= RequestMethod.GET)
    public String sayHello(){
        return "hello";
    }
}
```

### 2.@ConfigurationProperties

>可以把同类的配置信息自动映射到实体类属性当中

* **源码解析**
```
@Target({ElementType.TYPE,ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface ConfigurationProperties {    
    @AliasFor("prefix")    
    String value() default "";    
    
    @AliasFor("value")    
    String prefix() default "";
    ... ...
```

* **实例解析一：加载默认名称的配置文件**
>假设我们有这样一个场景，有一个属性值配置文件，我们要读取该配置文件中的属性值映射到一个实体类中的属性当中，然后生成该类的Bean对象放入容器中待后续使用。
```
// 配置文件内容,springboot默认加载配置文件名称：application.properties

jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.url = jdbc:mysql://localhost/demo?useUnicode=true&characterEncoding=utf-8
jdbc.username = root
jdbc.password = root
jdbc.maxActive= 50

// 注意：配置文件中的属性名必须和实体类中的属性名完全一致才可以，否则报错：
// java.lang.IllegalArgumentException: Could not resolve placeholder xxx in value xxx
// 这样在容器中就生成了该类的Bean对象，方便我们后续使用

@Component   // 使用方式一时添加
@ConfigurationProperties(prefix = "jdbc")     // 方式二时添加
public class ConnectionSettings {

    private String driverClassName;
    private String url;
    private String username ;
    private String password ;
    private String maxActive ;
    
    get/set方法省略。。。
}

// 使用Bean对象
// @EnableConfigurationProperties：该注解明确指定需要哪个实体类来装载配置信息

@SpringBootApplication
@EnableConfigurationProperties({ConnectionSettings.class})   // 方式二时添加
public class DemoApplication{

    // 使用方式一：
    @Autowired
    private ConnectionSettings connection;
   
    // 使用方式二：
   @Autowired
   private ConnectionSettings setting;
    
    // 使用方式三：  
    @Bean
    @ConfigurationProperties(prefix = "jdbc")
    public ConnectionSettings connectionSettings(){
        return new ConnectionSettings();
    }
    
    // main函数省略
}

```

* **实例解析二：加载自定义名称的配置文件**

```
// 自定义文件名：test.properties 
test.name=zhangsan
test.sex=23

// 对应实体类
// @PropertySource注解明确指出用哪个配置文件的内容进行属性值映射

@Configuration
@ConfigurationProperties(prefix="test")
@PropertySource("classpath:test.properties")
@Component
public class TestConfigBean {     
    private String name;     
    private int age;
    
    get/set方法省略
}
```