# mapstruct

是一个java API，提供了类之间转换的方法，省去了自己编写大量的set方法和写反射工具类。

示例：



1. 创建一个转换器接口（Converter）

```java
/*
* 将 GoodsES 转换为 GoodsDTO
* 使用默认的转换规则
*/
public class GoodsES{
    String name;
}
public class GoodsDTO{
    String name;
}
//@Mapper表明这是一个转换器
@Mapper
public interface CommonConverter{
	//必须
	CommonConverter INSTANCE = Mappers.getMapper(CommonConverter.class);
    
    /**
    * 同名、同类型会自动转换；
    * 同名、但不同类型，若是以下情况，也会自动转换：
    * 1. 基本类型及包装类（比如int和Integer）
    * 2. 基本类型及String
    * 3. 日期类型和String
    **/
    @Mapping()
    GoodsDTO esToDTO(GoodsES goodsES);

}
```

```java
/*
* 将 GoodsES 转换为 GoodsDTO
* 使用自定义的转换规则
*/
public class GoodsES{
    String name;
    String code;
    BigDecimal price;
    Date createDate;
}
public class GoodsDTO{
    String name;
    String goodsCode;
    String price;
    String createDate;
    
}
//@Mapper表明这是一个转换器
@Mapper
public interface CommonConverter{
	//必须
	CommonConverter INSTANCE = Mappers.getMapper(CommonConverter.class);
    
    
    //不同字段名转换
    @Mapping(target="goodsCode",source="code")
    //日期格式转换
    @Mapping(target="createDate",source="createDate",dateFormat="yyyy-MM-dd")
    //数字格式转换
    @Mapping(target="price",source="price",numberFormat="$#.00")
    //无论source里name是什么，都赋值为常量001
    @Mapping(target="name",constant="001")
    //source里name为null，则赋值为001
    @Mapping(target="name",source="name"defaultValue="001")
    //忽略name字段
    @Mapping(target="name",ignore=true) 
    GoodsDTO convert(GoodsES goodsES);
    
    //另一种写法，通过@MappingTarget表明哪一个是需要被注入属性的字段
    @Mapping(target="name",ignore=true) 
    @Mapping(target="goodsCode",source="code")
    void updateModel(@MappingTarget GoodsDTO goodsDTO,GoodsES goodsES);
    
    //只转换@Mapping中定义的字段，其他不转换
    //下面只转换了code，没有转换name
    @BeanMapping(ignoreByDefault = true) 
    @Mapping(target="goodsCode",source="code")
    GoodsDTO convert2(GoodsES goodsES);
    
    
    //-------------多个类转换为一个类-------------
    //多个类时，需要表明source字段来自于哪个类，
    @Mapping(target="goodsCode",source="goodsES.code")
    GoodsDTO convert3(GoodsES goodsES,CustomerDTO customerDTO);
    
    
    //-------------集合转换-------------
    //需要有上面实体转换的支持，否则不能转换
    List<GoodsDTO> convertToList(List<GoodsES> goodsES);
    
    Map<String,GoodsDTO> convertToMap(Map<String,GoodsES> goodsES);
    
    Set<GoodsDTO> convertToSet(Set<GoodsES> goodsES);
}
```

```java
/*
* 将 GoodsES 转换为 GoodsDTO
* 含有非基本数据类型时的转换：比如goodsES中含有一个List<Catagory>
*/
public class GoodsES{
    String name;
    String code;
    List<Catagory> catagoryList;
}
public class GoodsDTO{
    String name;
    String goodsCode;
     List<CatagoryDTO> catagoryDTOList;
}
public class Catagory{
    String name;
    String code;
}
public class CatagoryDTO{
    String name;
    String code;
}

//@Mapper表明这是一个转换器
@Mapper
public interface CatagoryConverter{
	//必须
	CatagoryConverter INSTANCE = Mappers.getMapper(CatagoryConverter.class);
    
    @Mapping() 
    CatagoryDTO convert(Catagory catagory);

}

//@Mapper表明这是一个转换器
//【重要】使用use：表明转换时需要使用另一个转换器（可添加多个）
@Mapper(uses = {CatagoryConverter.class})
public interface GoodsConverter{
	//必须
	GoodsConverter INSTANCE = Mappers.getMapper(GoodsConverter.class);
    
    @Mapping(target="name",source="name") 
    @Mapping(target="goodsCode",source="code")
    GoodsDTO convert(GoodsES goodsES);

}
```

```java
/*
* 将 GoodsES 转换为 GoodsDTO
* 使用自定义的转换规则
*/
public class GoodsES{
    String name;
    String code;
    BigDecimal price;
    LocalDateTime createDate;
}
public class GoodsDTO{
    String name;
    String goodsCode;
    String price;
    LocalDateTime createDate;
    
}
//@Mapper表明这是一个转换器
@Mapper(import = {UUID.class,LocalDateTime.class})
public interface CommonConverter{
	//必须
	CommonConverter INSTANCE = Mappers.getMapper(CommonConverter.class);
    
    
    //使用表达式
   @Mapping(target="goodsCode",expression="java(UUID.randomUUID().toString())")
   @Mapping(target="createDate",expression="java(LocalDateTime.now())")
    GoodsDTO convert(GoodsES goodsES);
    
}
```



2. 使用转换器

```java
GoodsES goodsES = new GoodsES();
GoodsDTO goodsDTO = CommonConverter.INSTANCE.esToDTO(goodsES);
```



*注册进Spring

```java
//@Mapper表明这是一个转换器
@Mapper(componentModel = "spring")
public interface GoodsConverter{
    //无需再定义INSTANCE
    GoodsDTO convert(GoodsES goodsES);
}


//使用转换器（通过Spring依赖注入）
public class Test{
    @Autowired
    private GoodsConverter goodsConverter;
}
```





#### Mapstruct的坑

以下两个类，若A直接转为B会报错。

因为A比B多了个C字段，转换时需要增加@Mapping(target ="C" ignore=true)

```java
@Data
class A {
    String A;
    String B;
    String C;
} 

@Data
class B {
    String A;
    String B;
} 
```

