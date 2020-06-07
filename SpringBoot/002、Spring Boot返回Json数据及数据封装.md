# 002、Spring Boot返回Json数据及数据封装

在项目开发中，接口与接口之间，前后端之间数据的传输都使用 Json 格式，在 Spring Boot 中，接口返回 Json 格式的数据很简单，在 Controller 中使用`@RestController`注解即可返回 Json 格式的数据，`@RestController`也是 Spring Boot 新增的一个注解，我们点进去看一下该注解都包含了哪些东西。
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Controller
@ResponseBody
public @interface RestController {
    String value() default "";
}
```
可以看出， `@RestController` 注解包含了原来的 `@Controller` 和 `@ResponseBody` 注解，使用过 Spring 的朋友对 `@Controller` 注解已经非常了解了，这里不再赘述， `@ResponseBody` 注解是将返回的数据结构转换为 Json 格式。所以在默认情况下，使用了 `@RestController` 注解即可将返回的数据结构转换成 Json 格式，Spring Boot 中默认使用的 Json 解析技术框架是 jackson。我们点开 pom.xml 中的 `spring-boot-starter-web` 依赖，可以看到一个 `spring-boot-starter-json` 依赖：
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-json</artifactId>
    <version>2.0.3.RELEASE</version>
    <scope>compile</scope>
</dependency>
```
Spring Boot 中对依赖都做了很好的封装，可以看到很多 `spring-boot-starter-xxx` 系列的依赖，这是 Spring Boot 的特点之一，不需要人为去引入很多相关的依赖了，starter-xxx 系列直接都包含了所必要的依赖，所以我们再次点进去上面这个 `spring-boot-starter-json` 依赖，可以看到：
```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.6</version>
    <scope>compile</scope>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.datatype</groupId>
    <artifactId>jackson-datatype-jdk8</artifactId>
    <version>2.9.6</version>
    <scope>compile</scope>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.datatype</groupId>
    <artifactId>jackson-datatype-jsr310</artifactId>
    <version>2.9.6</version>
    <scope>compile</scope>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.module</groupId>
    <artifactId>jackson-module-parameter-names</artifactId>
    <version>2.9.6</version>
    <scope>compile</scope>
</dependency>
```
到此为止，我们知道了 Spring Boot 中默认使用的 json 解析框架是 jackson。下面我们看一下默认的 jackson 框架对常用数据类型的转 Json 处理。

## 1. Spring Boot 默认对Json的处理

在实际项目中，常用的数据结构无非有类对象、List对象、Map对象，我们看一下默认的 jackson 框架对这三个常用的数据结构转成 json 后的格式如何。

### 1.1 创建 User 实体类

为了测试，我们需要创建一个实体类，这里我们就用 User 来演示。
```java
public class User {
    private Long id;
    private String username;
    private String password;
	/* 省略get、set和带参构造方法 */
}
```
### 1.2 创建Controller类

然后我们创建一个 Controller，分别返回 `User`对象、`List<User>` 和 `Map<String, Object>`。
```java
import com.itcodai.course02.entity.User;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

@RestController
@RequestMapping("/json")
public class JsonController {

    @RequestMapping("/user")
    public User getUser() {
        return new User(1, "倪升武", "123456");
    }

    @RequestMapping("/list")
    public List<User> getUserList() {
        List<User> userList = new ArrayList<>();
        User user1 = new User(1, "倪升武", "123456");
        User user2 = new User(2, "达人课", "123456");
        userList.add(user1);
        userList.add(user2);
        return userList;
    }

    @RequestMapping("/map")
    public Map<String, Object> getMap() {
        Map<String, Object> map = new HashMap<>(3);
        User user = new User(1, "倪升武", "123456");
        map.put("作者信息", user);
        map.put("博客地址", "http://blog.itcodai.com");
        map.put("CSDN地址", "http://blog.csdn.net/eson_15");
        map.put("粉丝数量", 4153);
        return map;
    }
}
```
### 1.3 测试不同数据类型返回的json

OK，写好了接口，分别返回了一个 User 对象、一个 List 集合和一个 Map 集合，其中 Map 集合中的 value 存的是不同的数据类型。接下来我们依次来测试一下效果。

在浏览器中输入：`localhost:8080/json/user` 返回 json 如下：
```json
{"id":1,"username":"倪升武","password":"123456"}
```
在浏览器中输入：`localhost:8080/json/list` 返回 json 如下：
```json
[{"id":1,"username":"倪升武","password":"123456"},{"id":2,"username":"达人课","password":"123456"}]
```
在浏览器中输入：`localhost:8080/json/map` 返回 json 如下：
```json
{"作者信息":{"id":1,"username":"倪升武","password":"123456"},"CSDN地址":"http://blog.csdn.net/eson_15","粉丝数量":4153,"博客地址":"http://blog.itcodai.com"}
```
可以看出，map 中不管是什么数据类型，都可以转成相应的 json 格式，这样就非常方便。

### 1.4 jackson 中对null的处理

在实际项目中，我们难免会遇到一些 null 值出现，我们转 json 时，是不希望有这些 null 出现的，比如我们期望所有的 null 在转 json 时都变成 "" 这种空字符串，那怎么做呢？在 Spring Boot 中，我们做一下配置即可，新建一个 jackson 的配置类：
```java
import com.fasterxml.jackson.core.JsonGenerator;
import com.fasterxml.jackson.databind.JsonSerializer;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializerProvider;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.http.converter.json.Jackson2ObjectMapperBuilder;

import java.io.IOException;

@Configuration
public class JacksonConfig {
    @Bean
    @Primary
    @ConditionalOnMissingBean(ObjectMapper.class)
    public ObjectMapper jacksonObjectMapper(Jackson2ObjectMapperBuilder builder) {
        ObjectMapper objectMapper = builder.createXmlMapper(false).build();
        objectMapper.getSerializerProvider().setNullValueSerializer(new JsonSerializer<Object>() {
            @Override
            public void serialize(Object o, JsonGenerator jsonGenerator, SerializerProvider serializerProvider) throws IOException {
                jsonGenerator.writeString("");
            }
        });
        return objectMapper;
    }
}
```

然后我们修改一下上面返回 map 的接口，将几个值改成 null 测试一下：
```java
@RequestMapping("/map")
public Map<String, Object> getMap() {
    Map<String, Object> map = new HashMap<>(3);
    User user = new User(1, "倪升武", null);
    map.put("作者信息", user);
    map.put("博客地址", "http://blog.itcodai.com");
    map.put("CSDN地址", null);
    map.put("粉丝数量", 4153);
    return map;
}
```
重启项目，再次输入：`localhost:8080/json/map`，可以看到 jackson 已经将所有 null 字段转成了空字符串了。
```json
{"作者信息":{"id":1,"username":"倪升武","password":""},"CSDN地址":"","粉丝数量":4153,"博客地址":"http://blog.itcodai.com"}
```


## 2. 使用阿里巴巴FastJson的设置

### 2.1 jackson 和 fastJson 的对比

有很多朋友习惯于使用阿里巴巴的 fastJson 来做项目中 json 转换的相关工作，目前我们项目中使用的就是阿里的 fastJson，那么 jackson 和 fastJson 有哪些区别呢？根据网上公开的资料比较得到下表。
|         选项          | fastJson | jackson |
| :-------------------: | :------: | :-----: |
|     上手难易程度      |   容易   |  中等   |
|     高级特性支持      |   中等   |  丰富   |
| 官方文档、Example支持 |   中文   |  英文   |
|     处理json速度      |   略快   |   快    |

关于 fastJson 和 jackson 的对比，网上有很多资料可以查看，主要是根据自己实际项目情况来选择合适的框架。从扩展上来看，fastJson 没有 jackson 灵活，从速度或者上手难度来看，fastJson 可以考虑，我们项目中目前使用的是阿里的 fastJson，挺方便的。

### 2.2 fastJson依赖导入

使用 fastJson 需要导入依赖，本课程使用 1.2.35 版本，依赖如下：
```xml
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>fastjson</artifactId>
	<version>1.2.35</version>
</dependency>
```

### 2.2 使用 fastJson 处理 null

使用 fastJson 时，对 null 的处理和 jackson 有些不同，需要继承  `WebMvcConfigurationSupport` 类，然后覆盖 `configureMessageConverters` 方法，在方法中，我们可以选择对要实现 null 转换的场景，配置好即可。如下：


```java
import com.alibaba.fastjson.serializer.SerializerFeature;
import com.alibaba.fastjson.support.config.FastJsonConfig;
import com.alibaba.fastjson.support.spring.FastJsonHttpMessageConverter;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.MediaType;
import org.springframework.http.converter.HttpMessageConverter;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;

import java.nio.charset.Charset;
import java.util.ArrayList;
import java.util.List;

@Configuration
public class fastJsonConfig extends WebMvcConfigurationSupport {

    /**
     * 使用阿里 FastJson 作为JSON MessageConverter
     * @param converters
     */
    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
        FastJsonHttpMessageConverter converter = new FastJsonHttpMessageConverter();
        FastJsonConfig config = new FastJsonConfig();
        config.setSerializerFeatures(
                // 保留map空的字段
                SerializerFeature.WriteMapNullValue,
                // 将String类型的null转成""
                SerializerFeature.WriteNullStringAsEmpty,
                // 将Number类型的null转成0
                SerializerFeature.WriteNullNumberAsZero,
                // 将List类型的null转成[]
                SerializerFeature.WriteNullListAsEmpty,
                // 将Boolean类型的null转成false
                SerializerFeature.WriteNullBooleanAsFalse,
                // 避免循环引用
                SerializerFeature.DisableCircularReferenceDetect);

        converter.setFastJsonConfig(config);
        converter.setDefaultCharset(Charset.forName("UTF-8"));
        List<MediaType> mediaTypeList = new ArrayList<>();
        // 解决中文乱码问题，相当于在Controller上的@RequestMapping中加了个属性produces = "application/json"
        mediaTypeList.add(MediaType.APPLICATION_JSON);
        converter.setSupportedMediaTypes(mediaTypeList);
        converters.add(converter);
    }
}
```

## 3. 封装统一返回的数据结构

以上是 Spring Boot 返回 json 的几个代表的例子，但是在实际项目中，除了要封装数据之外，我们往往需要在返回的 json 中添加一些其他信息，比如返回一些状态码 code ，返回一些 msg 给调用者，这样调用者可以根据 code 或者 msg 做一些逻辑判断。所以在实际项目中，我们需要封装一个统一的 json 返回结构存储返回信息。 

### 3.1 定义统一的 json 结构

由于封装的 json 数据的类型不确定，所以在定义统一的 json 结构时，我们需要用到泛型。统一的 json 结构中属性包括数据、状态码、提示信息即可，构造方法可以根据实际业务需求做相应的添加即可，一般来说，应该有默认的返回结构，也应该有用户指定的返回结构。如下：
```java
public class JsonResult<T> {

    private T data;
    private String code;
    private String msg;

    /**
     * 若没有数据返回，默认状态码为0，提示信息为：操作成功！
     */
    public JsonResult() {
        this.code = "0";
        this.msg = "操作成功！";
    }

    /**
     * 若没有数据返回，可以人为指定状态码和提示信息
     * @param code
     * @param msg
     */
    public JsonResult(String code, String msg) {
        this.code = code;
        this.msg = msg;
    }

    /**
     * 有数据返回时，状态码为0，默认提示信息为：操作成功！
     * @param data
     */
    public JsonResult(T data) {
        this.data = data;
        this.code = "0";
        this.msg = "操作成功！";
    }

    /**
     * 有数据返回，状态码为0，人为指定提示信息
     * @param data
     * @param msg
     */
    public JsonResult(T data, String msg) {
        this.data = data;
        this.code = "0";
        this.msg = msg;
    }
    // 省略get和set方法
}
```

### 3.2 修改 Controller 中的返回值类型及测试

由于 JsonResult 使用了泛型，所以所有的返回值类型都可以使用该统一结构，在具体的场景将泛型替换成具体的数据类型即可，非常方便，也便于维护。在实际项目中，还可以继续封装，比如状态码和提示信息可以定义一个枚举类型，以后我们只需要维护这个枚举类型中的数据即可（在本课程中就不展开了）。根据以上的 JsonResult，我们改写一下 Controller，如下：
```java
@RestController
@RequestMapping("/jsonresult")
public class JsonResultController {

    @RequestMapping("/user")
    public JsonResult<User> getUser() {
        User user = new User(1, "倪升武", "123456");
        return new JsonResult<>(user);
    }

    @RequestMapping("/list")
    public JsonResult<List> getUserList() {
        List<User> userList = new ArrayList<>();
        User user1 = new User(1, "倪升武", "123456");
        User user2 = new User(2, "达人课", "123456");
        userList.add(user1);
        userList.add(user2);
        return new JsonResult<>(userList, "获取用户列表成功");
    }

    @RequestMapping("/map")
    public JsonResult<Map> getMap() {
        Map<String, Object> map = new HashMap<>(3);
        User user = new User(1, "倪升武", null);
        map.put("作者信息", user);
        map.put("博客地址", "http://blog.itcodai.com");
        map.put("CSDN地址", null);
        map.put("粉丝数量", 4153);
        return new JsonResult<>(map);
    }
}
```
我们重新在浏览器中输入：`localhost:8080/jsonresult/user` 返回 json 如下：
```json
{"code":"0","data":{"id":1,"password":"123456","username":"倪升武"},"msg":"操作成功！"}
```
输入：`localhost:8080/jsonresult/list`，返回 json 如下：
```json
{"code":"0","data":[{"id":1,"password":"123456","username":"倪升武"},{"id":2,"password":"123456","username":"达人课"}],"msg":"获取用户列表成功"}
```
输入：`localhost:8080/jsonresult/map`，返回 json 如下：
```json
{"code":"0","data":{"作者信息":{"id":1,"password":"","username":"倪升武"},"CSDN地址":null,"粉丝数量":4153,"博客地址":"http://blog.itcodai.com"},"msg":"操作成功！"}
```
通过封装，我们不但将数据通过 json 传给前端或者其他接口，还带上了状态码和提示信息，这在实际项目场景中应用非常广泛。

## 4. 总结

本节主要对 Spring Boot 中 json 数据的返回做了详细的分析，从 Spring Boot 默认的 jackson 框架到阿里巴巴的 fastJson 框架，分别对它们的配置做了相应的讲解。另外，结合实际项目情况，总结了实际项目中使用的 json 封装结构体，加入了状态码和提示信息，使得返回的 json 数据信息更加完整。
课程源代码下载地址：[戳我下载](https://gitee.com/eson15/springboot_study)

> 欢迎关注我的为微信公众号：武哥聊编程