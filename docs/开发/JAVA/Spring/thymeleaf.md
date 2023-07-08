# thymeleaf



#### 简介

读法：Time leaf

新一代Java模板引擎，与JSP、Velocity、Freemarker类似。

主要作用也是将数据与模板结合，生成最终需要的文件。

支持各种格式的文件的生成：

html、xml

javascript、css、raw（文本文件）

**模板**

```html
<!--假设我们现在想通过模板生成 一封信的内容-->
hi,[({name})],...
```

**调用模板，并往模板输入数据**

```java
public class StudyThymeleaf {
    public static void main(String[] args) {
        //1. 获取模板
        String template = "hi,[(${name})]";

        //2.定义数据
        Context context = new Context();
        context.setVariable("name","John");

        //3.数据+模板 生成文件
        TemplateEngine engine = new TemplateEngine();
        String result = engine.process(template,context);

        //4.输出内容(hi,John)
        System.out.println(result);
    }
}
```



而在Web应用开发时，一般用该引擎去做html的模板，去做动态页面的开发。

配合SpringMVC、HTML、Javascript使用。



#### 特点

* 动静结合
* 开箱即用
* 多方言支持
* SpringBoot完美整合



#### 语法

##### 标准表达式

* 变量表达式：${...}
  * 获取对象的属性或方法： ${person.lastName}
  * 使用内置对象（见下面）：${#session.getAttribute('map')}  或者 ${session.map}
  * 使用内置的工具对象（见下面）：${#strings.equals('哈哈',name)}
* 选择变量表达式：*{...}：配合th:object使用，获取某个对象的属性。 *{firstName}
* 链接表达式：@{...}：url
* 国际化表达式：#{...}：读取properties中配置的返回信息
* 片段引用表达式：~{...}：引用其他模板的片段。~{templateName::fragmentName}

> 在哪里用标准表达式？
>
> 1. js脚本里。这样实现了java的属性能传到JS中（需要配合th:inline属性）
> 2. th标签中。增加逻辑语句，如循环语句、判断语句



##### th属性

| 属性              | 描述                               | 用例                                                         |
| ----------------- | ---------------------------------- | ------------------------------------------------------------ |
| th:id             | 替换html标签的id值                 |                                                              |
| th:text           | 替换文本值                         |                                                              |
| th:utext          | 替换文本值，支持html注入           |                                                              |
| th:object         | 注入对象，子标签可用对象的属性     | <div th:object="${user}"><br />Name: <span th:text="${name}"/><br />Age: <span th:text="${age}"/><br /></div> |
| th:value          | 替换value值                        |                                                              |
| th:link           | 替换css                            |                                                              |
| th:src            | 替换资源地址（JS引用、图片显示等） | <img th:src="@{/captcha/captchaImage}"/>                     |
| th:href           | 替换超链接（按钮）                 | <aa th:href="@{/register}">立即注册</a>                      |
| th:with           | 局部变量赋值运算                   |                                                              |
| th:click          | 点击事件                           |                                                              |
| th:each           | 遍历                               | 遍历List:<br /><tr th:each="item:${userList}"><br /><td th:text="${item.username}"></td><br /></tr><br />遍历Map:<br /><tr th:each="item:${myMap}"><br /><td th:text="${item.key}"></td><br /><td th:text="${item.value}"></td><br /></tr><br />遍历Set：<br />略 |
| th:if             | 条件判断                           |                                                              |
| th:switch th:case | 条件判断                           |                                                              |
| th:insert         | 嵌套插入一个页面                   |                                                              |
| th:replace        | 嵌套替换一个页面                   |                                                              |
| th:include        | 在JS中使用标准表达式（也称为内联） | <script th:inline="javascript"> var firstName = [[ ${user.firstName}]] ;</script> |
| th:fragment       | 定义一个片段块                     |                                                              |
| th:classappend    | 追加css class                      | <div class="row mainContent" th:classappend="${mainClass}"></div> |







##### 内置对象

* #ctx：上下文对象
* #vars：上下文变量？
* #locale：上下文的语言环境
* #request：HttpServletRequest
* #response：HttpServletResponse
* #session：HttpSession
* #servletContext：ServletContext





##### 内置的工具对象

* #strings
* #numbers
* #bools
* #arrays
* #lists/sets
* #maps
* #dates