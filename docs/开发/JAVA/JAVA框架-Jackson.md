# Jackson

一个JSON处理的开源工具库。  

写代码时，我们一般选用fastjson框架对对象进行序列化、反序列化；  
而Spring默认使用Jackson框架作JSON处理，因此我们在写Controller时有机会接触到Jackson。


包含3个核心包：
* jackson-databind
* jackson-annotations
* jackson-core

因为jackson-databind已经依赖了另外两个包，因此我们在使用时只需要依赖jackson-databind一个包即可。


添加maven依赖
```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.13.3</version>
</dependency>
```


### Jackson的作用

最主要的作用是：Java对象 <--> Json对象（字符串） 的互相转换。包括：

* Apple -> Json 
* java.util.Date -> Json 
* Json -> Apple
* Json -> List<Apple>
* Json -> Map<String,Object>


### Jackson的使用场景

* Spring默认的json工具



### 使用Jackson的常见问题

1. 日期转换格式问题

默认情况下，Date 转换成json后会变成一个时间戳。  
假如我们想转换成yyyy-MM-dd这种格式的，我们可以为字段添加@JsonFormat注解：
```java
@Data
public class Person {
    @JsonFormat(pattern="yyyy-MM-dd")
    private Date createDate;
}
```



2. 字段名称不一致问题

假如在Java对象的name字段转为Json对象的personName字段，我们可以这样：
```java
@Data
public class Person {
    @JsonSetter(value = "personName")
    private String name="test";

    @JsonGetter(value = "personName")
    public String getName(){
        return this.name;
    }
}
```

```json
{
    "personName":"test"
}
```


3. 多余字段问题




4. Spring如何配置Jackson参数



### Jackson的用法简介

```java
//先定义一个POJO
@Data
public class Person {
    private String name;
    private Integer age;
    private List<String> skillList;
}
```

```java
//序列化
class PersonTest {

    ObjectMapper objectMapper = new ObjectMapper();

    @Test
    void pojoToJsonString() throws JsonProcessingException {
        Person person = new Person();
        person.setName("aLng");
        person.setAge(27);
        person.setSkillList(Arrays.asList("java", "c++"));

        String json = objectMapper.writeValueAsString(person);
        System.out.println(json);
        String expectedJson = "{\"name\":\"aLng\",\"age\":27,\"skillList\":[\"java\",\"c++\"]}";
        Assertions.assertEquals(json, expectedJson);
    }
}

```


```java
//反序列化
class PersonTest {

    ObjectMapper objectMapper = new ObjectMapper();

    @Test
    void jsonStringToPojo() throws JsonProcessingException {
        String expectedJson = "{\"name\":\"aLang\",\"age\":27,\"skillList\":[\"java\",\"c++\"]}";
        Person person = objectMapper.readValue(expectedJson, Person.class);
        System.out.println(person);
        Assertions.assertEquals(person.getName(), "aLang");
        Assertions.assertEquals(person.getSkillList().toString(), "[java, c++]");
    }
}
```