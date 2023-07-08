# JDBC

```java
//第一步,注册驱动程序
Class.forName("数据库驱动完整名");
//第二步，获取一个数据库的连接
Connection conn = DriverManager.getConnection("数据库地址","用户名","密码");
//第三步，创建一个会话
Statement stmt=conn.createStatement();
//第四步，执行SQL语句
stmt.executeUpdate("SQL语句");
//或者查询记录
ResultSet rs= stmt.executeQuery("查询记录的SQL语句");
//第五步对查询结果进行处理
while(rs.next()){
//操作
}
//第六步，关闭连接
rs.close();
stmt.close();
conn.close();
```