# POI

POI是一个Office JAVA API，可以用来新建、编辑Excel、Work、Powerpoint等文档


### 参考资料
易佰——POI教程：[https://www.yiibai.com/apache_poi](https://www.yiibai.com/apache_poi)  
官方文档：[https://poi.apache.org/components/spreadsheet/quick-guide.html](https://poi.apache.org/components/spreadsheet/quick-guide.html)  
POI详解：[https://www.cnblogs.com/huajiezh/p/5467821.html](https://www.cnblogs.com/huajiezh/p/5467821.html)
### Excel

```java
//创建Excel、Sheet，创建一列、创建一个单元格

InputStream in = this.getClass().getResourceAsStream("/excel/test.xlsx");
Workbook workbook = WorkbookFactory.create(in);

Sheet sheet = workbook.getSheet("sheet名字");

Row row = sheet.createRow(0);

Cell cell = row.createCell(0);
```


```java
//单元格操作

//插入值
cell.setCellValue("test");

//设置单元格样式（对齐、边框）
CellStyle cellStyleOne = workbook.createCellStyle();
cellStyleOne.setDataFormat(workbook.createDataFormat().getFormat("0.00%"));
cell.setCellStyle(cellStyleOne);

//插入公式
cell.setCellFormula("A1 + B1");

```


```java
//行操作

//设置行高
row.setHeight(short height);


```



```java
//sheet操作

//合并单元格
sheet.addMergedRegion(CellRangeAddress region);



```