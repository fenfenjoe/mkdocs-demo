# Swing

Java提供的界面开发框架。

### 参考资料

java swing一篇轻松学习:[https://blog.csdn.net/feng8403000/article/details/125215102](https://blog.csdn.net/feng8403000/article/details/125215102)


### 常用组件类

* JFrame
* Container
* JPanel
* JLabel
* JTextField
* JButton

> 更多组件见：JComponent的继承类



### 示例代码

demo1:打开窗体，并显示hello world
```java
 public static void demo1(){
        JFrame f = new JFrame();
        f.setTitle("标题");//设置标题
        f.setSize(400,400); //设置界面大小，单位是像素(px)
        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); //设置窗口是否可关闭（默认是最小化：HIDE_ON_CLOSE）


        Container container = f.getContentPane();//内容窗口，组件往这里添加

        JLabel l = new JLabel("Hello world!");
        container.add(l);

        f.setVisible(true);//设置窗口为可见
        
        //执行完毕，会显示一个窗口
        //窗口位置在左上角
}
```


demo2:学习swing的布局
```java
public static void demo2(){
        JFrame f = new JFrame();
        f.setTitle("标题");//设置标题
        f.setSize(400,400); //设置界面大小，单位是像素(px)
        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); //设置窗口是否可关闭（默认是最小化：HIDE_ON_CLOSE）

        f.setLayout(new BorderLayout());//使用边框布局
        /**
         * 其他常见布局：
         *2、流式布局（FlowLayout）
 		 *3、网格布局（GridLayout）
 		 *4、盒子布局（BoxLaYout）
    	 *5、空布局（null）
         * 更多布局见：LayoutManager2接口的实现类
         */

        Container container = f.getContentPane();//内容窗口，组件往这里添加
        container.add(new JButton("North"),BorderLayout.NORTH);
        container.add(new JButton("West"),BorderLayout.WEST);
        container.add(new JButton("South"),BorderLayout.SOUTH);
        container.add(new JButton("Center"),BorderLayout.CENTER);
        container.add(new JButton("East"),BorderLayout.EAST);
        f.setVisible(true);//设置窗口为可见

        //执行完毕，会显示一个窗口
        //窗口位置在左上角
    }
```

> 边框布局BorderLayout：分5个区域，东、南、西、北以及中间
> 流式布局FlowLayout：组件从上到下、从左到右排序
> 卡片布局CardLayout：有点像单页面web应用，在一个Frame里可以跳转到多个不同的Panel
> 网格布局GridLayout