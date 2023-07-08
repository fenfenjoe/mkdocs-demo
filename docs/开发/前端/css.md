# CSS



#### css【盒模型】层次

```
上

border
边框
content
内容
padding
内边距
background-image
背景图
background-color
背景颜色
margin
外边距

下
```



#### 【伪元素】与【伪类】

伪类
属性	描述	CSS
:active	向被激活的元素添加样式。	1
:focus	向拥有键盘输入焦点的元素添加样式。	2
:hover	当鼠标悬浮在元素上方时，向元素添加样式。	1
:link	向未被访问的链接添加样式。	1
:visited	向已被访问的链接添加样式。	1
:first-child	向元素的第一个子元素添加样式。	2
:lang	向带有指定 lang 属性的元素添加样式。	2
伪元素
属性	描述	CSS
:first-letter	向文本的第一个字母添加特殊样式。	1
:first-line	向文本的首行添加特殊样式。	1
:before	在元素之前添加内容。	2
:after	在元素之后添加内容。	2



#### 选择器

根据标签
h2 {}
根据id
#id {}
根据class
.class {}
根据属性
[title] {}



#### 样式声明

样式
【外边距】margin
【水平居中】auto
可以是 宽度值、百分比值或 'auto' 这3者之一
【边框】border
【border-width/border-style/border-color】3px solid blue
【内边距】padding
【元素高】height
【元素宽】width
【定位类型】position
【绝对定位（相对于父元素）】absolute
【绝对定位（相对于浏览器）】fixed
【相对定位（相对于本身位置）】relative
【默认值。没有定位】static
【继承父元素的position属性】inherit
【相对父元素的边距】left、right
【50像素】50px
【在30%的位置】30%
背景
【颜色】background-color:red
【背景图】background-image:url(/doc/pic.jpg)
【背景平铺】background-repeat:repeat-x/repeat-y/no-repeat
【背景位置】background-position:center/left/right/bottom/top
【背景滚动】background-attachment:
文本
【文本对齐方式】text-align
center