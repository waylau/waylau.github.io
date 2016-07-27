---
layout: post
title:  使用 Apache POI 处理 Microsoft Office 文档
date: 2015-01-25 01:21
author: admin
comments: true
categories: [POI]
tags: [POI]
---

##POI 概述

Apache POI 项目的使命是创造和维护 Java API 操纵各种格式的文件,其中包括基于 Office Open XML 标准（OOXML）和微软的OLE 2 Compound Document 格式（OLE2）。总之，你可以使用 Java 读写 MS Excel 文件。此外，您可以使用 Java 读取和写入 MS Word 和 MS PowerPoint 文件。Apache POI 是你的 Java Excel 解决方案（用于Excel 97-2008）。包含了一个完整的 API 用于移植其他 OOXML 和OLE2 格式。

OLE2 文件包括了  Microsoft Office 文件，比如 XLS, DOC, PPT 以及 MFC 的序列化 API 为基础的文件格式。项目提供 [OLE2 Filesystem (POIFS)](http://poi.apache.org/poifs/index.html) 和 [OLE2 Document Properties (HPSF)](http://poi.apache.org/hpsf/index.html) 等 API。

Office OpenXML Format 是  Microsoft Office 2007 和 2008 中新的基于 XML 的标准。包括 XLSX, DOCX 和 PPTX。该项目提供了一个低级别的 API 使用 [openxml4j](http://poi.apache.org/oxml4j/index.html)来支持  Open Packaging Conventions（开放打包约定）。

针对每个 现存的 MS Office 模块组件，试图提供一个共同的高级别 Java api 给  OLE2 和 OOXML 文档格式。Excel (SS=HSSF+XSSF)
Word (HWPF+XWPF)，PowerPoint (HSLF+XSLF)，Outlook (HSMF)， Visio (HDGF), TNEF (HMEF), 和 Publisher (HPBF).

该项目尽可能与其他项目合作提供此功能。比如：[Cocoon](http://xml.apache.org/cocoon)提供 HSSF 的序列化;与[Open Office.org](http://www.openoffice.org/)合作处理 XLS 格式;和 [Tika](http://tika.apache.org/)/ [Lucene](http://lucene.apache.org/)提供格式解释器。

<!-- more -->

##组件

<table class="ForrestTable" cellspacing="1" cellpadding="4">
<tbody><tr class="b">
<th colspan="1" rowspan="1">组件</th>
<th colspan="1" rowspan="1">应用类型</th>
<th colspan="1" rowspan="1">Maven artifactId</th>
<th colspan="1" rowspan="1">备注</th>
<tr class="a">
<td colspan="1" rowspan="1"><a href="http://poi.apache.org/poifs/index.html">POIFS</a></td>
<td colspan="1" rowspan="1">OLE2 Filesystem</td>
<td colspan="1" rowspan="1"><em>poi</em></td>
<td colspan="1" rowspan="1">Required to work with OLE2 / POIFS based files</td>
</tr>
<tr class="b">
<td colspan="1" rowspan="1"><a href="http://poi.apache.org/hpsf/index.html">HPSF</a></td>
<td colspan="1" rowspan="1">OLE2 Property Sets</td>
<td colspan="1" rowspan="1">poi</td>
<td colspan="1" rowspan="1">&nbsp;</td>
</tr>
<tr class="a">
<td colspan="1" rowspan="1"><a href="http://poi.apache.org/spreadsheet/index.html">HSSF</a></td>
<td colspan="1" rowspan="1">Excel XLS</td>
<td colspan="1" rowspan="1">poi</td>
<td colspan="1" rowspan="1">For HSSF only, if common SS is needed see below</td>
</tr>
<tr class="b">
<td colspan="1" rowspan="1"><a href="http://poi.apache.org/slideshow/index.html">HSLF</a></td>
<td colspan="1" rowspan="1">PowerPoint PPT</td>
<td colspan="1" rowspan="1">poi-scratchpad</td>
<td colspan="1" rowspan="1">&nbsp;</td>
</tr>
<tr class="a">
<td colspan="1" rowspan="1"><a href="http://poi.apache.org/document/index.html">HWPF</a></td>
<td colspan="1" rowspan="1">Word DOC</td>
<td colspan="1" rowspan="1">poi-scratchpad</td>
<td colspan="1" rowspan="1">&nbsp;</td>
</tr>
<tr class="b">
<td colspan="1" rowspan="1"><a href="http://poi.apache.org/hdgf/index.html">HDGF</a></td>
<td colspan="1" rowspan="1">Visio VSD</td>
<td colspan="1" rowspan="1">poi-scratchpad</td>
<td colspan="1" rowspan="1">&nbsp;</td>
</tr>
<tr class="a">
<td colspan="1" rowspan="1"><a href="http://poi.apache.org/hpbf/index.html">HPBF</a></td>
<td colspan="1" rowspan="1">Publisher PUB</td>
<td colspan="1" rowspan="1">poi-scratchpad</td>
<td colspan="1" rowspan="1">&nbsp;</td>
</tr>
<tr class="b">
<td colspan="1" rowspan="1"><a href="http://poi.apache.org/hsmf/index.html">HSMF</a></td>
<td colspan="1" rowspan="1">Outlook MSG</td>
<td colspan="1" rowspan="1">poi-scratchpad</td>
<td colspan="1" rowspan="1">&nbsp;</td>
</tr>
<tr class="a">
<td colspan="1" rowspan="1"><a href="http://poi.apache.org/oxml4j/index.html">OpenXML4J</a></td>
<td colspan="1" rowspan="1">OOXML</td>
<td colspan="1" rowspan="1">poi-ooxml plus either poi-ooxml-schemas or<br>ooxml-schemas and ooxml-security</td>
<td colspan="1" rowspan="1">see below for differences</td>
</tr>
<tr class="b">
<td colspan="1" rowspan="1"><a href="http://poi.apache.org/spreadsheet/index.html">XSSF</a></td>
<td colspan="1" rowspan="1">Excel XLSX</td>
<td colspan="1" rowspan="1">poi-ooxml</td>
<td colspan="1" rowspan="1">&nbsp;</td>
</tr>
<tr class="a">
<td colspan="1" rowspan="1"><a href="http://poi.apache.org/slideshow/index.html">XSLF</a></td>
<td colspan="1" rowspan="1">PowerPoint PPTX</td>
<td colspan="1" rowspan="1">poi-ooxml</td>
<td colspan="1" rowspan="1">&nbsp;</td>
</tr>
<tr class="b">
<td colspan="1" rowspan="1"><a href="http://poi.apache.org/document/index.html">XWPF</a></td>
<td colspan="1" rowspan="1">Word DOCX</td>
<td colspan="1" rowspan="1">poi-ooxml</td>
<td colspan="1" rowspan="1">&nbsp;</td>
</tr>
<tr class="a">
<td colspan="1" rowspan="1"><a href="http://poi.apache.org/spreadsheet/index.html">Common SS</a></td>
<td colspan="1" rowspan="1">Excel XLS and XLSX</td>
<td colspan="1" rowspan="1">poi-ooxml</td>
<td colspan="1" rowspan="1">WorkbookFactory and friends all require poi-ooxml, not just core poi</td>
</tr>
</tbody></table>

##使用

下面展示使用 HSSF 来创建 excle 的案例

###创建一个 POI 项目

 	mvn archetype:create -DgroupId=com.waylau.poi -DartifactId=poi-demos
  
###引入相关 POI 依赖

本例子是版本 3.11 

	<dependency>
		<groupId>org.apache.poi</groupId>
		<artifactId>poi</artifactId>
		<version>3.11</version>
	</dependency>

###第一个 HelloWorld 程序

新增一个 HelloWorld.java 

HSSFWorkbook() 用来创建工作簿

    Workbook wb = new HSSFWorkbook();  // 或 new XSSFWorkbook();

wb.createShee 是用来创建 Sheet

    Sheet sheet1 = wb.createSheet("第1个 sheet");
    Sheet sheet2 = wb.createSheet("第2个 sheet");

有 Sheet 的命名具有一定的限制性，比如，不能用 `*` 、`？` 等,为了方便、安全的命名，可以使用 
`org.apache.poi.ss.util.WorkbookUtil#createSafeSheetName` 方法，

 	String safeName = WorkbookUtil.createSafeSheetName("[WayLau's Blog*?]"); // 返回 " WayLau's Blog   "

    Sheet sheet3 = wb.createSheet(safeName);

创建数据 行。Row 是从  0 开始的

    Row row = sheet1.createRow((short)0);

创建 Cell 放值进去

    Cell cell0 = row.createCell(0);
    cell0.setCellValue(1);

更简单的做法是在一行代码实现

    row.createCell(1).setCellValue(1.2);
    row.createCell(2).setCellValue(true);

可以用 CreationHelper 处理实例化具体类的不同实例

  	CreationHelper createHelper = wb.getCreationHelper();
    row.createCell(3).setCellValue(createHelper.createRichTextString("This is a string"));

使用 CellStyle,用来设置 Cell 的样式

    CellStyle cellStyle = wb.createCellStyle();
    cellStyle.setDataFormat(
        createHelper.createDataFormat().getFormat("m/d/yy h:mm"));
    
    Cell cell4 = row.createCell(4);
    cell4.setCellValue(new Date());
    cell4.setCellStyle(cellStyle);

最后生成 excle 文件

    FileOutputStream fileOut = new FileOutputStream("helloword.xls");
    wb.write(fileOut);
    fileOut.close();

效果

![](http://99btgc01.info/uploads/2015/01/poi.jpg)

##项目源码
 
见<https://github.com/waylau/poi-demos> 项目中 [HelloWorld.java](https://github.com/waylau/poi-demos/blob/master/poi-demos/src/main/java/com/waylau/poi/hssf/HelloWorld.java) 示例

##参考：

* <http://poi.apache.org/index.html>
* <http://poi.apache.org/overview.html#components>