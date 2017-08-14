# bug-list

## 14. Map的keySet以及entrySet方法返回元素顺序问题：
测试用例：
``` java
@Test
    public void testEntrySetOrder3(){
        Map<String, String> map1 = new Hashtable<>();
        map1.put("3","3");
        map1.put("1","1");
        map1.put("2","2");
        map1.put("5","5");
        map1.put("4","4");

        Map<String, String> map2 = new HashMap<>();
        map2.put("3","3");
        map2.put("1","1");
        map2.put("2","2");
        map2.put("5","5");
        map2.put("4","4");

        Map<String, String> map3 = new LinkedHashMap<>();
        map3.put("3","3");
        map3.put("1","1");
        map3.put("2","2");
        map3.put("5","5");
        map3.put("4","4");

        Map<String, String> map4 = new TreeMap<>();
        map4.put("3","3");
        map4.put("1","1");
        map4.put("2","2");
        map4.put("5","5");
        map4.put("4","4");

        Map<String, String> map5 = new ConcurrentHashMap<>();
        map5.put("3","3");
        map5.put("1","1");
        map5.put("2","2");
        map5.put("5","5");
        map5.put("4","4");

        System.out.println("HashTable : " + map1.entrySet());
        System.out.println("HashMap : " + map2.entrySet());
        System.out.println("LinkedHashMap : " + map3.entrySet());
        System.out.println("TreeMap : " + map4.entrySet());
        System.out.println("ConcurrentHashMap : " + map5.entrySet());
    }
```

测试结果：

* JDK 1.7 下输出结果：
``` text
HashTable : [5=5, 4=4, 3=3, 2=2, 1=1]
HashMap : [3=3, 2=2, 1=1, 5=5, 4=4]
LinkedHashMap : [3=3, 1=1, 2=2, 5=5, 4=4]
TreeMap : [1=1, 2=2, 3=3, 4=4, 5=5]
ConcurrentHashMap : [1=1, 5=5, 3=3, 4=4, 2=2]
```

* JDK 1.8 下输出结果：
``` text
HashTable : [5=5, 4=4, 3=3, 2=2, 1=1]
HashMap : [1=1, 2=2, 3=3, 4=4, 5=5]
LinkedHashMap : [3=3, 1=1, 2=2, 5=5, 4=4]
TreeMap : [1=1, 2=2, 3=3, 4=4, 5=5]
ConcurrentHashMap : [1=1, 2=2, 3=3, 4=4, 5=5]
```

结论：
* JDK1.7中，keySet方法以及entrySet方法将会按照如下顺序排序：
> 1. HashTable: 按照key降序排序；
> 2. HashMap: 按照key的某种顺序排序（无论put元素的顺序如何，都会按照某种顺序排序），但是既不是升序，也不是降序，也不是原序；
> 3. LinkedHashMap: 按照key原序排序；
> 4. TreeMap：按照key升序排序；
> 5. ConcurrentHashMap：按照key的某种顺序排序（无论put元素的顺序如何，都会按照某种顺序排序），但是与HashMap的排序方式不同。

* JDK1.8中，keySet方法以及entrySet方法将会按照如下顺序排序：
> 1. HashTable: 按照key降序排序；
> 2. HashMap: 按照key升序排序（**与JDK1.7不同**）；
> 3. LinkedHashMap: 按照key原序排序；
> 4. TreeMap：按照key升序排序；
> 5. ConcurrentHashMap：按照key升序排序（**与JDK1.7不同**）。

## 13. Java使用mysql-jdbc连接MySQL出现如下警告：
  > Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
  
  原因是MySQL在高版本需要指明是否进行SSL连接。在mysql连接字符串url中加入ssl=true或者false即可:

  ``` url
   url=jdbc:mysql://127.0.0.1:3306/framework?characterEncoding=utf8&useSSL=true 
  ```

## 12. 使用Maven默认构建的web.xml文件将会导致jsp页面中无法读取requestScope中的内容。解决办法：将web.xml的声明替换为如下代码(这种方式可同时处理idea中Servlet等元素报错的问题)：

  ``` xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         id="WebApp_ID" version="2.5">
         ...
  <web-app/>
  ```

## 11. 编译时报错：

 `Error:java: javacTask: source release 8 requires target release 1.8`

 解决办法：在pom.xml文件中做出如下配置：
 
 ``` xml
    <build>
       ...
       <plugins>
            <!-- 指定Maven编译条件 否则会出现：
              Error:java: javacTask: source release 8 requires target release 1.8
             -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
 ```
    
## 10. Maven编译war文件时会忽略类路径下的*.xml(mapper.xml)文件导致出现如下异常（原因：没有找到映射文件，编译时在target文件夹中没有mapper.xml文件）：
    
    `Mapped Statements collection does not contain value for ...`
    
解决办法：在pom.xml中配置资源文件：
    
    ``` xml 
    <build>
        <!-- 指定要编译到target目录下的资源文件位置 -->
        <resources>
            <!-- 将src/main/java路径下的所有xml、properties文件添加到target中 -->
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                    <include>**/*.properties</include>
                </includes>
                <filtering>false</filtering>
            </resource>
            <!-- 将src/main/resources路径下的所有xml、properties文件添加到target中 -->
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.xml</include>
                    <include>**/*.properties</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
        ...
    <build/>
    ```
    
## 9. 如何查找依赖包
    
    链接地址：[http://mvnrepository.com/](http://mvnrepository.com/ "mvnrepository")
    
## 8. Spring容器只支持一个<context:property-placeholder/>配置，而且只会读取到第一个配置文件，其他的会被忽略。

解决办法：使用通配符导入配置文件
``` xml
    <context:property-placeholder location="classpath*:conf/conf*.properties"/>  
```

## 7. 使用Gson与服务器交互时间数据时，解决时间数据传递问题：
首先Gson生成时间需要格式化成yyyy-MM-dd HH:mm:ss的格式：

``` java
Gson gson = new GsonBuilder()
                .setDateFormat("yyyy-MM-dd HH:mm:ss")
                .create();
```

然后在服务端采用如下方法转换日期格式：

``` java
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
private Date birthday;
```

如果采用RequestBody来传递参数，就不能使用Spring提供的@DatatimeFormat注解来格式化日期格式，需要使用Jackson提供的
@JsonFormat标签来格式化时间

在JavaBean中,可以同时指定这两个注解，用于分别解决表单提交的Data类型数据和Json方式提交的Date类型数据的格式化问题：
``` java
@DateTimeFormat(pattern = "yyyy/MM/dd HH;mm;ss")
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
private Date birthday;
```
提交的Json对象中的Date数据：
``` json
{"birthday":"2016-11-05 13:13:13","id":0,"name":"张三","salary":1000.0}
```
表单中的Date数据
``` text
birthday:2016/12/20 13;13;13
```
这样服务器接收到的时间信息就可以被正确解析

## 6. 解决Post请求中的中文乱码问题：
在web.xml文件中配置过滤器：
``` xml
    <!-- 解决post请求参数乱码问题 -->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

## 5. 解决Get请求中文乱码问题：在Tomcat配置文件的Connector节点配置如下参数

``` xml
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443"
               URIEncoding="UTF-8" />
```
idea中需要去掉Tomcat配置中的VM Options中的参数：-Dfile.encoding=UTF-8

## 4. 解决@ResponseBody参数响应乱码问题

``` xml
    <!-- 开启注解 @RequestBody、@ResponseBody（） -->
    <mvc:annotation-driven>
        <!-- 解决@ResponseBody返回中文参数乱码问题 -->
        <mvc:message-converters register-defaults="true">
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <property name="supportedMediaTypes" value="text/html;charset=UTF-8"/>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
```

## 3. 引入Log4J2后控制台报错
``` text
    SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
```

解决办法： Placing one (**and only one**) of slf4j-nop.jar, slf4j-simple.jar, slf4j-log4j12.jar, slf4j-jdk14.jar or logback-classic.jar on the class path should solve the problem.

``` xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.21</version>
    </dependency>
```

## 2. Servlet Api与JSP支持包由Servlet容器（Tomcat）提供，所以在pom.xml文件中不用再次提供该文件，所以在配置文件中将其编译范围指定为provide（已提供）

``` xml
        <dependency>
            ...
            <scope>provided</scope>
        </dependency>

        <!-- JSP -->
        <dependency>
            ...
            <scope>provided</scope>
        </dependency>
```

## 1. 是否声明HTML文档的doctype将会对HTML文档样式造成影响，所以尽量声明HTML文档的DOCTYPE:
``` html
<!DOCTYPE html>
</html>
...
</html>
```



