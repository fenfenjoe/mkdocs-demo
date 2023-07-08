# Jakarta

该包的目的：

​	优雅地增加参数验证的逻辑。



使用场景：

​	校验入参是否为null、是否为true、校验数字值是否在某个区间（min、max）、校验是否正确的邮箱地址等。

​    若校验不通过，默认会抛出异常。



原理：

​	注解+AOP



使用限制：

​	需要配合Spring使用



#### 示例代码

```java
//1.先在入参中增加校验规则（通过@NotBlank、@NotNull等注解）
public class GoodsReqDTO{
    //NotBlank:不能为空
    @NotBlank
    private String goodsCode;
}


//2.在需要校验入参的地方开启校验。
//开启方法1（AOP方式）：增加@Valid或者@Validated注解
public class GoodsController{
    
    //需要校验的入参，增加@Valid注解
    public boolean createGoods(@Valid GoodsReqDTO goodsReqDTO){
        return true;
    }
}


//开启方法2（手动校验）：
public class SpringValidation {

    public static final String COMMON_MSG = "参数输入有误!";

    /**
     * 参数校验
     * @param validateData
     * @param <T>
     * @return
     */
    public static <T> String verifyParameters(T validateData){
        return verifyParametersByGroup(validateData,new Class[0]);
    }

    /**
     * 分组参数校验
     * @param validateData
     * @param validateGroup
     * @param <T>
     * @return
     */
    public static <T> String verifyParametersByGroup(T validateData, Class<?>...validateGroup){
        ValidatorFactory validatorFactory= Validation.buildDefaultValidatorFactory();
        Set<ConstraintViolation<T>> validate = validatorFactory.getValidator().validate(validateData,validateGroup);
        if(CollectionUtils.isNotEmpty(validate)){
            String message =validate.stream().map(ConstraintViolation::getMessage)
                    .reduce((m1,m2)->m1+";"+m2).orElse(COMMON_MSG);
            return message;
        }
        return null;
    }
}


public class GoodsController{
    
    //需要校验的入参，调用SpringValidation
    public boolean createGoods(GoodsReqDTO goodsReqDTO){
        String msg = SpringValidation.verifyParameters(goodsReqDTO);
        if(CollectionUtils.isNotEmpty(msg)){
            return false;
        }
        return true;
    }
}


```



#### 常用注解

* @AssertFalse / @AssertTrue
* @DecimalMax / @DecimalMin
* @Digits
* @Email
* @Future / @Past / @FutureOrPresent / @PastOrPresent
* @Max / @Min
* @NotEmpty / @NotNull / @Null
* @Positive / @Negative / @PositiveOrZero / @NegativeOrZero
* @Size