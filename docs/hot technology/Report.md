# **报表专题**

### 报表简介

报表功能从技术上来说分

1. 复杂SQL查询语句
2. 报表文档格式生成技术（导入导出，Excel格式poi 、PDF格式IText 、PDF格式【绘制报表页面IReport +JasperReport 】）
3. 客户端报表技术通常显示一些图表ECharts、Highcharts （柱状图、饼图…）

### POI报表技术

##### POI介绍

Apache POI是用Java编写的免费开源的跨平台的Java API，Apache POI提供API给Java程序对Microsoft Office格式档案读和写的功能，其中使用最多的就是使用POI操作Excel文件。

POI文档结构：

<img src="/img/poi-1.png">

**注**：Microsoft Excel XLS是.xlsh后缀的excel文档，Microsoft Excel OOXML XLSX格式档案指的是.xlsx后缀的excle文档。

POI操作Excel表格封装了几个核心对象：

​	XSSFWorkbook： 工作簿

​	XSSFSheet  ： 工作表

​	Row    ： 行

​	Cell    ： 单元格

***poi报表技术实现Excel解析(解析Excel逻辑：工作簿---sheet---row---cell)***:

​	  HSSFWorkbook    xls的文件   

 	 sheet     一张excel表格

  	rows      行数据（每一行表示一条数据）

 	 cell      行中的某一列

  	真正的数据，则是保存在每一个Cell中

##### POI解析Excel文件的步骤（导入）：

```java
1）将上传成功的file文件关联到Excel工作簿对象

​    workbook = new HSSFWorkbook(new FileInputStream(file2))

2）通过workbook对象获得 Sheet工作表对象

​    Sheet sheet = workbook.getSheet(下标）（通常只用读取第一个sheet即下标为0的sheet）

3）再通过sheet对象来获取到每一行的数据

​    Rows row = sheet.getRow(下标）来得到指定下标位置的row

​    也可直接遍历： for(Row row :　sheet )

4）读取每一行row中的列数据

​    Cell cell = row.getCell(下标）

5）通过cell来读取列中保存的数据

​    String cellData = cell.getStringCellValue();
```

#####  向Excel文件写入数据（导出）

使用POI可以在内存中创建一个Excel文件并将数据写入到这个文件，最后通过输出流将内存中的Excel文件下载到磁盘:

```java
//在内存中创建一个Excel文件对象

XSSFWorkbook workbook = new XSSFWorkbook();

//创建工作表对象，指定工作表名称

XSSFSheet sheet = workbook.createSheet("传智播客");

//创建行对象，0表示第一行

XSSFRow row = sheet.createRow(0);

//创建单元格对象，0表示第一个单元格

row.createCell(0).setCellValue("编号");

row.createCell(1).setCellValue("名称");

row.createCell(2).setCellValue("年龄");

XSSFRow row1 = sheet.createRow(1);

row1.createCell(0).setCellValue("1");

row1.createCell(1).setCellValue("小明");

row1.createCell(2).setCellValue("10");

XSSFRow row2 = sheet.createRow(2);

row2.createCell(0).setCellValue("2");

row2.createCell(1).setCellValue("小王");

row2.createCell(2).setCellValue("20");

//通过输出流将workbook对象下载到磁盘

FileOutputStream out = new FileOutputStream("D:\\itcast.xlsx");

workbook.write(out);

out.flush();

out.close();

workbook.close();
//以上下载到磁盘是下载到本地磁盘，要是在网络上，需要用response获得流对象
```

### Echarts技术

##### Echarts简介

ECharts缩写来自Enterprise Charts，商业级数据图表，是**百度**的一个开源的使用JavaScript实现的数据可视化工具，可以流畅的运行在 PC 和移动设备上，兼容当前绝大部分浏览器（IE8/9/10/11ChromeFirefox，Safari等），底层依赖轻量级的矢量图形库 ZRender，提供直观、交互丰富、可高度个性化定制的数据可视化图表，包括折线图，柱状图，饼图，坐标图以及3D图等各种丰富的图标。

<img src="/img/echarts-1.png">

##### Echarts使用

去官网下载Echarts包，解压后我们只需要将dist目录下的echarts.js文件引入到页面上就可以使用了

<img src="/img/echarts-2.png">

在实际应用中，我们要展示的数据往往存储在数据库中，所以我们可以发送ajax请求获取数据库中的数据并转为图表所需的数据即可。

#### 5.9.4 Highcharts技术

Highcharts 是一个非常流行，界面美观的纯Javascript 图表库。它主要包括两个部分：Highcharts 和Highstock。Highcharts 可以为您的网站或Web 应用程序提供直观，互动式的图表。目前支持线，样条，面积，areaspline，柱形图，条形图，饼图和散点图类型。Highstock 可以为您方便地建立股票或一般的时间轴图表。它包括先进的导航选项，预设的日期范围，日期选择器，滚动和平移等等。

使用不过多介绍，和Echarts差不多，现在使用的比较多的是Echarts

有兴趣的可以去官网看看：https://www.highcharts.com.cn/demo/highcharts