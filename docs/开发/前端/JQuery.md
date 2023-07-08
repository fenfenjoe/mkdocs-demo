# JQUERY





通过JQUERY获取HTML组件：

* 获取所有p组件 $("p")
* 获取所有p组件，并将其隐藏  $("p").hide();
* 获取id为test的组件，并将其隐藏 $("#test").hide();
* 获取class为test的组件，并将其隐藏 $(".test").hide();
* 获取class为test的所有p组件，并将其隐藏 $("p.test").hide();
* 根据组件id，获取select组件 $("select[id='appIdSelect']")
* 根据组件name，获取input组件 $("input[name='appIdSelect']")
* 获取name为searchType，且被选中的单选框      $("input[name='searchType']:checked")



组件的常用操作：

* 触发事件
  * 点击事件 $("p").**click**(function(){         $(this).hide();          })
  * 双击事件 $("p").**dbclick**(function(){         $(this).hide();          })
  * 鼠标经过事件  $("p").**mouseenter**(function(){         $(this).hide();          })
  * 鼠标离开事件  $("p").**mouseleave**(function(){         $(this).hide();          })
  * 获得焦点事件  $("p").**focus**(function(){         $(this).hide();          })
  * 失去焦点事件  $("p").**blur**(function(){         $(this).hide();          })



* DOM操作

  * 获取value                $("p").**val**()
  * 获取文本内容           $("p").**text**()
  * 获取文本内容，包括HTML标签           $("p").**html**()
  * 获取属性                 $("p").**attr**("href")
  * 设置value的值（其他同理）                        $("p").**val**("Hello world!")
  * 删除属性                  $("p").**removeAttr**("href")
  * 通过回调函数，获取旧的值 
    * $("p").**val**( function(i,origText){       return "i = 当前元素下标，origText为当前元素的旧值。返回的是希望显示的新值"                    })





**$(function(){   })**

类似于Java中的Main()方法，会在HTML把全部DOM元素加载完毕后，自动执行function里面的JS语句