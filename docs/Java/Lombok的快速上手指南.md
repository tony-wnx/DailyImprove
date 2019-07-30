>最近空余时间在做自己一个小项目的升级改造，由于之前的entity类中都在使用get/set方法，这些方法和字段混合在一起，看起来不是很优雅，所以决定使用Lombok来改善一下。

本教程是基于IDEA上的使用说明。

#### Lombok简介
Lombok是一个可以通过简单的注解形式来帮助我们简化消除一些必须有但显得很臃肿的Java代码的工具，通过使用对应的注解，可以在编译源码的时候生成对应的方法。

简单来说，我们平时开发过程中总是要花很多时间为Java Bean 去创建getter和setter方法，当类里面的属性很多时则创建的getter和setter就很多，代码就很长。而lombok就可以为我们省去创建getter和setter方法的麻烦，代码也会更加简洁。

Lombok官方地址: https://projectlombok.org/

#### Lombok的使用

* **安装插件**
打开 IDEA 的 Settings 面板，并选择 Plugins 选项，然后点击 “Search in repositories”，在输入框输入”Lombok”，找到Lombok，点击安装，然后安装提示重启 IDEA，安装完成。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190730124027834.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dueF81MjA1NQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019073012415524.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dueF81MjA1NQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190730124050636.png)

* **引入依赖**
在 pom 文件里面添加 依赖：

```
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.16.22</version>
    <scope>provided</scope>
</dependency>
```

* **现在，你就就可以在项目中使用Lombok了**
直接在实体类上引入相关的注解就行：
@Data
注解在 类 上；提供类所有属性的 get 和 set 方法，此外还提供了equals、canEqual、hashCode、toString 方法。

```
示例：
// 源文件
@Data
public class Menu implements Serializable {
    private static final long serialVersionUID = 489914127235951698L;
    private Integer menuId;
    private String menuName;
    private String menuUrl;
    private Integer menuLevel;
    private String menuIcon;
    private Integer menuOrder;
}

// 编译过后的.class文件内容：
public class Menu implements Serializable {
    private static final long serialVersionUID = 489914127235951698L;
    private Integer menuId;
    private String menuName;
    private String menuUrl;
    private Integer menuLevel;
    private String menuIcon;
    private Integer menuOrder;

    public Menu() {
    }

    public Integer getMenuId() {
        return this.menuId;
    }

    public String getMenuName() {
        return this.menuName;
    }

    public String getMenuUrl() {
        return this.menuUrl;
    }

    public Integer getMenuLevel() {
        return this.menuLevel;
    }

    public String getMenuIcon() {
        return this.menuIcon;
    }

    public Integer getMenuOrder() {
        return this.menuOrder;
    }

    public void setMenuId(Integer menuId) {
        this.menuId = menuId;
    }

    public void setMenuName(String menuName) {
        this.menuName = menuName;
    }

    public void setMenuUrl(String menuUrl) {
        this.menuUrl = menuUrl;
    }

    public void setMenuLevel(Integer menuLevel) {
        this.menuLevel = menuLevel;
    }

    public void setMenuIcon(String menuIcon) {
        this.menuIcon = menuIcon;
    }

    public void setMenuOrder(Integer menuOrder) {
        this.menuOrder = menuOrder;
    }

    public boolean equals(Object o) {
        if (o == this) {
            return true;
        } else if (!(o instanceof Menu)) {
            return false;
        } else {
            Menu other = (Menu)o;
            if (!other.canEqual(this)) {
                return false;
            } else {
                Object this$menuId = this.getMenuId();
                Object other$menuId = other.getMenuId();
                if (this$menuId == null) {
                    if (other$menuId != null) {
                        return false;
                    }
                } else if (!this$menuId.equals(other$menuId)) {
                    return false;
                }

                Object this$menuName = this.getMenuName();
                Object other$menuName = other.getMenuName();
                if (this$menuName == null) {
                    if (other$menuName != null) {
                        return false;
                    }
                } else if (!this$menuName.equals(other$menuName)) {
                    return false;
                }

                Object this$menuUrl = this.getMenuUrl();
                Object other$menuUrl = other.getMenuUrl();
                if (this$menuUrl == null) {
                    if (other$menuUrl != null) {
                        return false;
                    }
                } else if (!this$menuUrl.equals(other$menuUrl)) {
                    return false;
                }

                label62: {
                    Object this$menuLevel = this.getMenuLevel();
                    Object other$menuLevel = other.getMenuLevel();
                    if (this$menuLevel == null) {
                        if (other$menuLevel == null) {
                            break label62;
                        }
                    } else if (this$menuLevel.equals(other$menuLevel)) {
                        break label62;
                    }

                    return false;
                }

                label55: {
                    Object this$menuIcon = this.getMenuIcon();
                    Object other$menuIcon = other.getMenuIcon();
                    if (this$menuIcon == null) {
                        if (other$menuIcon == null) {
                            break label55;
                        }
                    } else if (this$menuIcon.equals(other$menuIcon)) {
                        break label55;
                    }

                    return false;
                }

                Object this$menuOrder = this.getMenuOrder();
                Object other$menuOrder = other.getMenuOrder();
                if (this$menuOrder == null) {
                    if (other$menuOrder != null) {
                        return false;
                    }
                } else if (!this$menuOrder.equals(other$menuOrder)) {
                    return false;
                }

                return true;
            }
        }
    }

    protected boolean canEqual(Object other) {
        return other instanceof Menu;
    }

    public int hashCode() {
        int PRIME = true;
        int result = 1;
        Object $menuId = this.getMenuId();
        int result = result * 59 + ($menuId == null ? 43 : $menuId.hashCode());
        Object $menuName = this.getMenuName();
        result = result * 59 + ($menuName == null ? 43 : $menuName.hashCode());
        Object $menuUrl = this.getMenuUrl();
        result = result * 59 + ($menuUrl == null ? 43 : $menuUrl.hashCode());
        Object $menuLevel = this.getMenuLevel();
        result = result * 59 + ($menuLevel == null ? 43 : $menuLevel.hashCode());
        Object $menuIcon = this.getMenuIcon();
        result = result * 59 + ($menuIcon == null ? 43 : $menuIcon.hashCode());
        Object $menuOrder = this.getMenuOrder();
        result = result * 59 + ($menuOrder == null ? 43 : $menuOrder.hashCode());
        return result;
    }

    public String toString() {
        return "Menu(menuId=" + this.getMenuId() + ", menuName=" + this.getMenuName() + ", menuUrl=" + this.getMenuUrl() + ", menuLevel=" + this.getMenuLevel() + ", menuIcon=" + this.getMenuIcon() + ", menuOrder=" + this.getMenuOrder() + ")";
    }
}
```

@Setter
注解在 属性 上；为单个属性提供 set 方法; 注解在 类 上，为该类所有的属性提供 set 方法， 都提供默认构造方法。

@Getter
注解在 属性 上；为单个属性提供 get 方法; 注解在 类 上，为该类所有的属性提供 get 方法，都提供默认构造方法。

@Log4j
注解在 类 上；为类提供一个 属性名为 log 的 log4j 日志对象，提供默认构造方法。

@AllArgsConstructor
注解在 类 上；为类提供一个全参的构造方法，加了这个注解后，类中不提供默认构造方法了。

@NoArgsConstructor
注解在 类 上；为类提供一个无参的构造方法。

@EqualsAndHashCode
注解在 类 上, 可以生成 equals、canEqual、hashCode 方法。

@NonNull
注解在 属性 上，会自动产生一个关于此参数的非空检查，如果参数为空，则抛出一个空指针异常，也会有一个默认的无参构造方法。

@Cleanup
这个注解用在 变量 前面，可以保证此变量代表的资源会被自动关闭，默认是调用资源的 close() 方法，如果该资源有其它关闭方法，可使用 @Cleanup(“methodName”) 来指定要调用的方法，也会生成默认的构造方法

@ToString
这个注解用在 类 上，可以生成所有参数的 toString 方法，还会生成默认的构造方法

@RequiredArgsConstructor
这个注解用在 类 上，使用类中所有带有 @NonNull 注解的或者带有 final 修饰的成员变量生成对应的构造方法。

@Value
这个注解用在 类 上，会生成含所有参数的构造方法，get 方法，此外还提供了equals、hashCode、toString 方法。

@SneakyThrows
这个注解用在 方法 上，可以将方法中的代码用 try-catch 语句包裹起来，捕获异常并在 catch 中用 Lombok.sneakyThrow(e) 把异常抛出，可以使用 @SneakyThrows(Exception.class) 的形式指定抛出哪种异常，也会生成默认的构造方法。

@Synchronized
这个注解用在 类方法 或者 实例方法 上，效果和 synchronized 关键字相同，区别在于锁对象不同，对于类方法和实例方法，synchronized 关键字的锁对象分别是类的 class 对象和 this 对象，而 @Synchronized 的锁对象分别是 私有静态 final 对象 lock 和 私有 final 对象 lock，当然，也可以自己指定锁对象，此外也提供默认的构造方法。

#### 总结
* 一般情况下，我们使用 @Data 就够用了。
* 使用lombok可以省去我们手动创建getter和setter方法的麻烦，同时使代码看起来更加整洁，开发更加高效，但同时使用lombok也会降低源代码文件的可读性和完整性。

-----------------------------------------------------

------ 感谢您的阅读 ------

如果您认为文章对您有所帮助的话，劳烦您动动您可爱的小手点个赞，您的认同是我创作做大的动力!
