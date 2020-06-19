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

#### MyGenerate POJOs.groovy

```java
import com.intellij.database.model.DasTable
import com.intellij.database.model.ObjectKind
import com.intellij.database.util.Case
import com.intellij.database.util.DasUtil
import java.io.*
import java.text.SimpleDateFormat

/*
 * Available context bindings:
 *   SELECTION   Iterable<DasObject>
 *   PROJECT     project
 *   FILES       files helper
 */

packageName = ""
typeMapping = [
        (~/(?i)tinyint|smallint|mediumint/)      : "Integer",
        (~/(?i)int/)                             : "Long",
        (~/(?i)bool|bit/)                        : "Boolean",
        (~/(?i)float|double|decimal|real/)       : "Double",
        (~/(?i)datetime|timestamp|date|time/)    : "Date",
        (~/(?i)blob|binary|bfile|clob|raw|image/): "InputStream",
        (~/(?i)/)                                : "String"
]

def generate(table, dir) {
  def className = javaClassName(table.getName(), true)
  def fields = calcFields(table)
  // new File(dir, className + ".java").withPrintWriter { out -> generate(out, className, fields,table) }
  // 生成路径
  packageName = getPackageName(dir)
  PrintWriter printWriter = new PrintWriter(new OutputStreamWriter(new FileOutputStream(new File(dir, className + ".java")), "UTF-8"))
  printWriter.withPrintWriter {out -> generate(out, className, fields,table)}
}

FILES.chooseDirectoryAndSave("Choose directory", "Choose where to store generated files") { dir ->
  SELECTION.filter { it instanceof DasTable && it.getKind() == ObjectKind.TABLE }.each { generate(it, dir) }
}

// 获取包所在文件夹路径
def getPackageName(dir) {
  return dir.toString().replaceAll("\\\\", ".").replaceAll("/", ".").replaceAll("^.*src(\\.main\\.java\\.)?", "") + ";"
}

def generate(out, className, fields,table) {
  out.println "package $packageName"
  out.println ""
  // 根据需要自行添加
  // out.println "import java.io.Serializable;"
  // out.println "import lombok.Getter;"
  // out.println "import lombok.Setter;"
  // out.println "import lombok.ToString;"
  out.println "import lombok.AllArgsConstructor;"
  out.println "import lombok.Data;"
  out.println "import lombok.NoArgsConstructor;"
  out.println "import javax.persistence.Id;"
  out.println "import javax.persistence.Table;"

  Set types = new HashSet()
  fields.each() {
    types.add(it.type)
  }

  if (types.contains("Date")) {
    out.println "import java.util.Date;"
  }

  if (types.contains("InputStream")) {
    out.println "import java.io.InputStream;"
  }

  out.println ""
  out.println "/**\n" +
          " * @ClassName  $className \n" +
          " * @Description  \n" +
          " * @Author  yangxiongqian\n" +
          " * @Date "+ new SimpleDateFormat("yyyy/MM/dd").format(new Date()) + " \n" +
          " * @Version V1.0" + " \n" +
          " */"
  // 实体类上边的注解，需要哪个自行添加
  out.println "@Data"
  out.println "@NoArgsConstructor"
  out.println "@AllArgsConstructor"
  out.println "@Table ( name =\""+table.getName() +"\" )"
  out.println "public class $className {"
  // 实现序列化
  // out.println "public class $className implements Serializable {"
  // 生成序列化SerialID
  // out.println genSerialID()
  fields.each() {
    out.println ""
    // 自动生成注释
    if (isNotEmpty(it.commoent)) {
      out.println "\t/**"
      out.println "\t * ${it.commoent.toString()}"
      out.println "\t */"
    }

    // if (it.annos != "") out.println "   ${it.annos.replace("[@Id]", "")}"
    if (it.annos != "") out.println "    @Id"

    // 生成成员变量
    out.println "\tprivate ${it.type} ${it.name};"
  }

  // 生成get/set方法
  // fields.each() {
  //     out.println ""
  //     out.println "\tpublic ${it.type} get${it.name.capitalize()}() {"
  //     out.println "\t\treturn this.${it.name};"
  //     out.println "\t}"
  //     out.println ""

  //     out.println "\tpublic void set${it.name.capitalize()}(${it.type} ${it.name}) {"
  //     out.println "\t\tthis.${it.name} = ${it.name};"
  //     out.println "\t}"
  // }
  out.println ""
  out.println "}"
}

def calcFields(table) {
  DasUtil.getColumns(table).reduce([]) { fields, col ->
    def spec = Case.LOWER.apply(col.getDataType().getSpecification())

    def typeStr = typeMapping.find { p, t -> p.matcher(spec).find() }.value
    def comm =[
            colName : col.getName(),
            name :  javaName(col.getName(), false),
            type : typeStr,
            commoent: col.getComment(),
            annos: ""]
            // 成员变量上的注释
            // annos: "\t@Column(name = \""+col.getName()+"\" )"]
    if("id".equals(Case.LOWER.apply(col.getName())))
      comm.annos +=["@Id"]
    fields += [comm]
  }
}

// 根据表明生成实体类名（表名sub_开头，做了截取处理。根据表名，自行选择长度截取）
def javaClassName(str, capitalize) {
  def s = com.intellij.psi.codeStyle.NameUtil.splitNameIntoWords(str)
          .collect { Case.LOWER.apply(it).capitalize() }
          .join("")
          .replaceAll(/[^\p{javaJavaIdentifierPart}[_]]/, "_")
  // 从第三位开始，截取到最后
  s = s[3..s.size() - 1]
  capitalize || s.length() == 1? s : Case.LOWER.apply(s[0]) + s[1..-1]
}

def javaName(str, capitalize) {
  def s = com.intellij.psi.codeStyle.NameUtil.splitNameIntoWords(str)
          .collect { Case.LOWER.apply(it).capitalize() }
          .join("")
          .replaceAll(/[^\p{javaJavaIdentifierPart}[_]]/, "_")
  capitalize || s.length() == 1? s : Case.LOWER.apply(s[0]) + s[1..-1]
}

def isNotEmpty(content) {
  return content != null && content.toString().trim().length() > 0
}

static String changeStyle(String str, boolean toCamel){
  if(!str || str.size() <= 1)
    return str
  if(toCamel){
    String r = str.toLowerCase().split('_').collect{cc -> Case.LOWER.apply(cc).capitalize()}.join('')
    return r[0].toLowerCase() + r[1..-1]
  }else{
    str = str[0].toLowerCase() + str[1..-1]
    return str.collect{cc -> ((char)cc).isUpperCase() ? '_' + cc.toLowerCase() : cc}.join('')
  }
}

// 生成序列化SerialID
static String genSerialID() {
  return "\tprivate static final long serialVersionUID =  "+Math.abs(new Random().nextLong())+"L;"
}
```



### 生成实体类(生成是可选择生成的地址)

![idea_10](E:\WorkSpaces\DailyImprove\docs\Java\img\idea_10.png)