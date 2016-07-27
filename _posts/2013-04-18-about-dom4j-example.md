---
layout: post
title: DOM4J介绍与代码示例
date: 2013-04-18 01:30
author: admin
comments: true
categories: [Java]
tags: [Dom4J, Java]
---
原文摘自：http://zhangjunhd.blog.51cto.com/113473/126310

DOM4J是dom4j.org出品的一个开源XML解析包。Dom4j是一个易用的、开源的库，用于XML，XPath和XSLT。它应用于Java平台，采用了Java集合框架并完全支持DOM，SAX和JAXP。
<div>DOM4J下载jar包：http://downloads.sourceforge.net/dom4j/dom4j-1.6.1.jar</div>
<div>JAXEN（对XPath的支持）：http://dist.codehaus.org/jaxen/distributions/jaxen-1.1.1.zip</div>
<div><b>1.DOM4J</b><b>主要接口</b><b></b></div>
<div>DOM4J主要接口都在org.dom4j这个包里定义。</div>
<div></div>
<div></div>
<div>-Node为所有的dom4j中XML节点定义了多态行为；</div>
<div></div>
<div>-Branch为能够包含子节点的节点如XML元素(Element)和文档(Docuemnts)定义了一个公共的行为；</div>
<div>|-Element 定义XML 元素；</div>
<div>|-Document定义了XML文档；</div>
<div></div>
<div>-DocumentType 定义XML DOCTYPE声明；</div>
<div>-Entity定义 XML entity；</div>
<div>-Attribute定义了XML的属性；</div>
<div>-ProcessingInstruction 定义 XML 处理指令；</div>
<div></div>
<div>-CharacterData是一个标识借口，标识基于字符的节点。如CDATA，Comment, Text；</div>
<div>|- CDATA 定义了XML CDATA 区域；</div>
<div>|-Text 定义XML 文本节点；</div>
<div>|- Comment 定义了XML注释的行为；</div>
<div><b>2.</b><b>创建</b><b>XML</b><b>文档</b><b></b></div>
<div>示例xml：students.xml</div>
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td valign="top" width="568">
<div align="left">&lt;?xml version=<i>"1.0"</i> encoding=<i>"UTF-8"</i>?&gt;</div>
<div align="left">&lt;?xml-stylesheet type=<i>"text/xsl"</i> href=<i>"students.xsl"</i>?&gt;</div>
<div align="left">&lt;students&gt;</div>
<div align="left">&lt;!--A Student Catalog--&gt;</div>
<div align="left">&lt;student sn=<i>"01"</i>&gt;</div>
<div align="left">&lt;name&gt;sam&lt;/name&gt;</div>
<div align="left">&lt;age&gt;18&lt;/age&gt;</div>
<div align="left">&lt;/student&gt;</div>
<div align="left">&lt;student sn=<i>"02"</i>&gt;</div>
<div align="left">&lt;name&gt;lin&lt;/name&gt;</div>
<div align="left">&lt;age&gt;20&lt;/age&gt;</div>
<div align="left">&lt;/student&gt;</div>
<div>&lt;/students&gt;</div></td>
</tr>
</tbody>
</table>
<div></div>
<div>下面是用dom4j创建上述文档，通过两种方式创建，一种是调用dom4j提供的方法，一种是通过字符串转换。</div>
<div>XmlGen.java</div>
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td valign="top" width="568">
<div align="left"><b>import</b> java.io.File;</div>
<div align="left"><b>import</b> java.io.FileWriter;</div>
<div align="left"><b>import</b> java.io.IOException;</div>
<div align="left"><b>import</b> java.util.HashMap;</div>
<div align="left"><b>import</b> java.util.Map;</div>
<div align="left"></div>
<div align="left"><b>import</b> org.dom4j.Document;</div>
<div align="left"><b>import</b> org.dom4j.DocumentException;</div>
<div align="left"><b>import</b> org.dom4j.DocumentHelper;</div>
<div align="left"><b>import</b> org.dom4j.Element;</div>
<div align="left"><b>import</b> org.dom4j.io.XMLWriter;</div>
<div align="left"></div>
<div align="left"><b>public</b> <b>class</b> XmlGen {</div>
<div align="left"><b>public</b> Document generateDocumentByMethod() {</div>
<div align="left">Document document = DocumentHelper.<i>createDocument</i>();</div>
<div align="left">// ProcessingInstruction</div>
<div align="left">Map&lt;String, String&gt; inMap = <b>new</b> HashMap&lt;String, String&gt;();</div>
<div align="left">inMap.put("type", "text/xsl");</div>
<div align="left">inMap.put("href", "students.xsl");</div>
<div align="left">document.addProcessingInstruction("xml-stylesheet", inMap);</div>
<div align="left">// root element</div>
<div align="left">Element studentsElement = document.addElement("students");</div>
<div align="left">studentsElement.addComment("An Student Catalog");</div>
<div align="left">// son element</div>
<div align="left">Element stuElement = studentsElement.addElement("student");</div>
<div align="left">stuElement.addAttribute("sn", "01");</div>
<div align="left">Element nameElement = stuElement.addElement("name");</div>
<div align="left">nameElement.setText("sam");</div>
<div align="left">Element ageElement = stuElement.addElement("age");</div>
<div align="left">ageElement.setText("18");</div>
<div align="left">// son element</div>
<div align="left">Element anotherStuElement = studentsElement.addElement("student");</div>
<div align="left">anotherStuElement.addAttribute("sn", "02");</div>
<div align="left">Element anotherNameElement = anotherStuElement.addElement("name");</div>
<div align="left">anotherNameElement.setText("lin");</div>
<div align="left">Element anotherAgeElement = anotherStuElement.addElement("age");</div>
<div align="left">anotherAgeElement.setText("20");</div>
<div align="left"></div>
<div align="left"><b>return</b> document;</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left"><b>public</b> Document generateDocumentByString() {</div>
<div align="left">String text = "&lt;?xml version=\"1.0\" encoding=\"UTF-8\"?&gt;" +</div>
<div align="left">"&lt;?xml-stylesheet type=\"text/xsl\" href=\"students.xsl\"?&gt;" +</div>
<div align="left">"&lt;students&gt;&lt;!--An Student Catalog--&gt; &lt;student sn=\"01\"&gt;" +</div>
<div align="left">"&lt;name&gt;sam&lt;/name&gt;&lt;age&gt;18&lt;/age&gt;&lt;/student&gt;&lt;student sn=\"02\"&gt;" +</div>
<div align="left">"&lt;name&gt;lin&lt;/name&gt;&lt;age&gt;20&lt;/age&gt;&lt;/student&gt;&lt;/students&gt;";</div>
<div align="left">Document document = <b>null</b>;</div>
<div align="left"><b>try</b> {</div>
<div align="left">document = DocumentHelper.<i>parseText</i>(text);</div>
<div align="left">} <b>catch</b> (DocumentException e) {</div>
<div align="left">e.printStackTrace();</div>
<div align="left">}</div>
<div align="left"><b>return</b> document;</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left"><b>public</b> <b>void</b> saveDocument(Document document, File outputXml) {</div>
<div align="left"><b>try</b> {</div>
<div align="left">// 美化格式</div>
<div align="left">OutputFormat format = OutputFormat.<i>createPrettyPrint</i>();</div>
<div align="left">/*// 缩减格式</div>
<div align="left">OutputFormat format = OutputFormat.createCompactFormat();*/</div>
<div align="left">/*// 指定XML编码</div>
<div align="left">format.setEncoding("GBK");*/</div>
<div align="left">XMLWriter output = <b>new</b> XMLWriter(<b>new</b> FileWriter(outputXml), format);</div>
<div align="left">output.write(document);</div>
<div align="left">output.close();</div>
<div align="left">} <b>catch</b> (IOException e) {</div>
<div align="left">System.<i>out</i>.println(e.getMessage());</div>
<div align="left">}</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left"><b>public</b> <b>static</b> <b>void</b> main(String[] argv) {</div>
<div align="left">XmlGen dom4j = <b>new</b> XmlGen();</div>
<div align="left">Document document = <b>null</b>;</div>
<div align="left">// document=dom4j.generateDocumentByMethod();</div>
<div align="left">document = dom4j.generateDocumentByString();</div>
<div align="left">dom4j.saveDocument(document, <b>new</b> File("output.xml"));</div>
<div align="left">}</div>
<div align="left">}</div></td>
</tr>
</tbody>
</table>
<div>方法generateDocumentByMethod()通过调用方法构建xml文档：</div>
<div>1.使用DocumentHelper得到Document实例</div>
<div>Document document = DocumentHelper.<i>createDocument</i>();</div>
<div>2.创建Processing Instruction</div>
<div>document.addProcessingInstruction("xml-stylesheet", inMap);</div>
<div>3.创建元素Element</div>
<div>Element studentsElement = document.addElement("students");</div>
<div>4.为元素添加注释Comment</div>
<div>studentsElement.addComment("An Student Catalog");</div>
<div>5.为元素添加属性</div>
<div>studentsElement.addComment("An Student Catalog");</div>
<div>6.为元素添加文本值Text</div>
<div>ageElement.setText("18");</div>
<div></div>
<div>方法generateDocumentByString()通过字符串转换直接构建xml文档，使用DocumentHelper.<i>parseText</i>()来实现.</div>
<div align="left">document = DocumentHelper.<i>parseText</i>(text);</div>
<div></div>
<div>方法saveDocument(Document document, File outputXml)将文档输出到文件保存，可指定字符编码，可指定格式化输出。</div>
<div><b>3.</b><b>修改</b><b>XML</b><b>文档</b><b></b></div>
<div>这里使用xpath来定位待修改的元素和属性，需要jaxen的支持。</div>
<div>示例中将students-gen.xml的第一个student元素的sn属性改为001，其子元素name内容改为jeff。</div>
<div>XmlMod.java</div>
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td valign="top" width="568">
<div align="left"><b>import</b> java.io.File;</div>
<div align="left"><b>import</b> java.io.FileWriter;</div>
<div align="left"><b>import</b> java.io.IOException;</div>
<div align="left"><b>import</b> java.util.Iterator;</div>
<div align="left"><b>import</b> java.util.List;</div>
<div align="left"></div>
<div align="left"><b>import</b> org.dom4j.Attribute;</div>
<div align="left"><b>import</b> org.dom4j.Document;</div>
<div align="left"><b>import</b> org.dom4j.DocumentException;</div>
<div align="left"><b>import</b> org.dom4j.Element;</div>
<div align="left"><b>import</b> org.dom4j.io.SAXReader;</div>
<div align="left"><b>import</b> org.dom4j.io.XMLWriter;</div>
<div align="left"></div>
<div align="left"><b>public</b> <b>class</b> XmlMod {</div>
<div align="left"><b>public</b> <b>void</b> modifyDocument(File inputXml) {</div>
<div align="left"><b>try</b> {</div>
<div align="left">SAXReader saxReader = <b>new</b> SAXReader();</div>
<div align="left">Document document = saxReader.read(inputXml);</div>
<div align="left">List list = document.selectNodes("//students/student/@sn");</div>
<div align="left">Iterator iter = list.iterator();</div>
<div align="left"><b>while</b> (iter.hasNext()) {</div>
<div align="left">Attribute attribute = (Attribute) iter.next();</div>
<div align="left"><b>if</b> (attribute.getValue().equals("01"))</div>
<div align="left">attribute.setValue("001");</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left">list = document.selectNodes("//students/student");</div>
<div align="left">iter = list.iterator();</div>
<div align="left"><b>while</b> (iter.hasNext()) {</div>
<div align="left">Element element = (Element) iter.next();</div>
<div align="left">Iterator iterator = element.elementIterator("name");</div>
<div align="left"><b>while</b> (iterator.hasNext()) {</div>
<div align="left">Element nameElement = (Element) iterator.next();</div>
<div align="left"><b>if</b> (nameElement.getText().equals("sam"))</div>
<div align="left">nameElement.setText("jeff");</div>
<div align="left">}</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left">XMLWriter output = <b>new</b> XMLWriter(<b>new</b> FileWriter(<b>new</b> File(</div>
<div align="left">"students-modified.xml")));</div>
<div align="left">output.write(document);</div>
<div align="left">output.close();</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left"><b>catch</b> (DocumentException e) {</div>
<div align="left">System.<i>out</i>.println(e.getMessage());</div>
<div align="left">} <b>catch</b> (IOException e) {</div>
<div align="left">System.<i>out</i>.println(e.getMessage());</div>
<div align="left">}</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left"><b>public</b> <b>static</b> <b>void</b> main(String[] argv) {</div>
<div align="left">XmlMod dom4jParser = <b>new</b> XmlMod();</div>
<div align="left">dom4jParser.modifyDocument(<b>new</b> File("students-gen.xml"));</div>
<div align="left">}</div>
<div>}</div></td>
</tr>
</tbody>
</table>
<div>1.使用File定位文件资源，并基于此获得Document实例</div>
<div align="left">SAXReader saxReader = <b>new</b> SAXReader();</div>
<div>Document document = saxReader.read(inputXml);</div>
<div>2.Document实例的selectNodes方法可以传入xpath，并返回一个List实例，基于此使用迭代器，完成特定的应用</div>
<div>List list = document.selectNodes("//students/student/@sn");</div>
<div><b>4.</b><b>遍历</b><b>XML</b><b>文档</b><b></b></div>
<div>这里提供两种遍历方法，一种是基于迭代的遍历，一种是基于Visitor模式的遍历。</div>
<div>XmlTra.java</div>
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td valign="top" width="568">
<div align="left"><b>import</b> java.io.File;</div>
<div align="left"><b>import</b> java.util.Iterator;</div>
<div align="left"></div>
<div align="left"><b>import</b> org.dom4j.Attribute;</div>
<div align="left"><b>import</b> org.dom4j.Document;</div>
<div align="left"><b>import</b> org.dom4j.DocumentException;</div>
<div align="left"><b>import</b> org.dom4j.Element;</div>
<div align="left"><b>import</b> org.dom4j.ProcessingInstruction;</div>
<div align="left"><b>import</b> org.dom4j.VisitorSupport;</div>
<div align="left"><b>import</b> org.dom4j.io.SAXReader;</div>
<div align="left"></div>
<div align="left"><b>public</b> <b>class</b> XmlTra {</div>
<div align="left"><b>private</b> File inputXml;</div>
<div align="left"></div>
<div align="left"><b>public</b> XmlTra(File inputXml) {</div>
<div align="left"><b>this</b>.inputXml = inputXml;</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left"><b>public</b> Document getDocument() {</div>
<div align="left">SAXReader saxReader = <b>new</b> SAXReader();</div>
<div align="left">Document document = <b>null</b>;</div>
<div align="left"><b>try</b> {</div>
<div align="left">document = saxReader.read(inputXml);</div>
<div align="left">} <b>catch</b> (DocumentException e) {</div>
<div align="left">e.printStackTrace();</div>
<div align="left">}</div>
<div align="left"><b>return</b> document;</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left"><b>public</b> Element getRootElement() {</div>
<div align="left"><b>return</b> getDocument().getRootElement();</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left"><b>public</b> <b>void</b> traversalDocumentByIterator() {</div>
<div align="left">Element root = getRootElement();</div>
<div align="left">// 枚举根节点下所有子节点</div>
<div align="left"><b>for</b> (Iterator ie = root.elementIterator(); ie.hasNext();) {</div>
<div align="left">System.<i>out</i>.println("======");</div>
<div align="left">Element element = (Element) ie.next();</div>
<div align="left">System.<i>out</i>.println(element.getName());</div>
<div align="left"></div>
<div align="left">// 枚举属性</div>
<div align="left"><b>for</b> (Iterator ia = element.attributeIterator(); ia.hasNext();) {</div>
<div align="left">Attribute attribute = (Attribute) ia.next();</div>
<div align="left">System.<i>out</i>.println(attribute.getName() + ":"</div>
<div align="left">+ attribute.getData());</div>
<div align="left">}</div>
<div align="left">// 枚举当前节点下所有子节点</div>
<div align="left"><b>for</b> (Iterator ieson = element.elementIterator(); ieson.hasNext();) {</div>
<div align="left">Element elementSon = (Element) ieson.next();</div>
<div align="left">System.<i>out</i>.println(elementSon.getName() + ":"</div>
<div align="left">+ elementSon.getText());</div>
<div align="left">}</div>
<div align="left">}</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left"><b>public</b> <b>void</b> traversalDocumentByVisitor() {</div>
<div align="left">getDocument().accept(<b>new</b> MyVisitor());</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left">/**</div>
<div align="left">* 定义自己的访问者类</div>
<div align="left">*/</div>
<div align="left"><b>private</b> <b>static</b> <b>class</b> MyVisitor <b>extends</b> VisitorSupport {</div>
<div align="left">/**</div>
<div align="left">* 对于属性节点，打印属性的名字和值</div>
<div align="left">*/</div>
<div align="left"><b>public</b> <b>void</b> visit(Attribute node) {</div>
<div align="left">System.<i>out</i>.println("attribute : " + node.getName() + " = "</div>
<div align="left">+ node.getValue());</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left">/**</div>
<div align="left">* 对于处理指令节点，打印处理指令目标和数据</div>
<div align="left">*/</div>
<div align="left"><b>public</b> <b>void</b> visit(ProcessingInstruction node) {</div>
<div align="left">System.<i>out</i>.println("PI : " + node.getTarget() + " "</div>
<div align="left">+ node.getText());</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left">/**</div>
<div align="left">* 对于元素节点，判断是否只包含文本内容，如是，则打印标记的名字和 元素的内容。如果不是，则只打印标记的名字</div>
<div align="left">*/</div>
<div align="left"><b>public</b> <b>void</b> visit(Element node) {</div>
<div align="left"><b>if</b> (node.isTextOnly())</div>
<div align="left">System.<i>out</i>.println("element : " + node.getName() + " = "</div>
<div align="left">+ node.getText());</div>
<div align="left"><b>else</b></div>
<div align="left">System.<i>out</i>.println("--------" + node.getName() + "--------");</div>
<div align="left">}</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left"><b>public</b> <b>static</b> <b>void</b> main(String[] argv) {</div>
<div align="left">XmlTra dom4jParser = <b>new</b> XmlTra(<b>new</b> File("students-gen.xml"));</div>
<div align="left">// dom4jParser.traversalDocumentByIterator();</div>
<div align="left">dom4jParser.traversalDocumentByVisitor();</div>
<div align="left">}</div>
<div align="left">}</div></td>
</tr>
</tbody>
</table>
<div>方法traversalDocumentByIterator()提供一种基于迭代的遍历实现，每个Element通过elementIterator()和attributeIterator()取代其子元素和属性的迭代器。</div>
<div>Visitor是GOF设计模式之一。其主要原理就是两种类互相保有对方的引用，并且一种作为Visitor去访问许多Visitable。DOM4J中的Visitor模式只需要自定一个类实现Visitor接口即可。</div>
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td valign="top" width="568">
<div align="left"><b>public</b> <b>class</b> MyVisitor <b>extends</b> VisitorSupport {</div>
<div align="left"><b>public</b> <b>void</b> visit(Element element) {</div>
<div align="left">System.<i>out</i>.println(element.getName());</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left"><b>public</b> <b>void</b> visit(Attribute attr) {</div>
<div align="left">System.<i>out</i>.println(attr.getName());</div>
<div align="left">}</div>
<div>}</div></td>
</tr>
</tbody>
</table>
<div>调用： root.accept(new MyVisitor())</div>
<div>Visitor接口提供多种Visit()的重载，根据XML不同的对象，将采用不同的方式来访问。上面是给出的Element和Attribute的简单实现，一般比较常用的就是这两个。VisitorSupport是DOM4J提供的默认适配器，Visitor接口的Default Adapter模式，这个模式给出了各种visit(*)的空实现，以便简化代码。</div>
<div>注意，这个Visitor是自动遍历所有子节点的。如果是root.accept(MyVisitor)，将遍历子节点。我第一次用的时候，认为是需要自己遍历，便在递归中调用Visitor，结果可想而知。</div>
<div><b>5.</b><b>使用</b><b>ElementHandler</b></div>
<div>XmlHandler.java</div>
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td valign="top" width="568">
<div align="left"><b>import</b> java.io.File;</div>
<div align="left"></div>
<div align="left"><b>import</b> org.dom4j.DocumentException;</div>
<div align="left"><b>import</b> org.dom4j.Element;</div>
<div align="left"><b>import</b> org.dom4j.ElementHandler;</div>
<div align="left"><b>import</b> org.dom4j.ElementPath;</div>
<div align="left"><b>import</b> org.dom4j.io.SAXReader;</div>
<div align="left"></div>
<div align="left"><b>public</b> <b>class</b> XmlHandler {</div>
<div align="left"><b>public</b> <b>static</b> <b>void</b> main(String[] args) {</div>
<div align="left">SAXReader saxReader = <b>new</b> SAXReader();</div>
<div align="left">File file = <b>new</b> File("students.xml");</div>
<div align="left"><b>try</b> {</div>
<div align="left">// 添加一个ElementHandler实例。</div>
<div align="left">saxReader.addHandler("/students/student", <b>new</b>StudentHandler());</div>
<div align="left">saxReader.read(file);</div>
<div align="left"></div>
<div align="left">} <b>catch</b> (DocumentException e) {</div>
<div align="left">System.<i>out</i>.println(e.getMessage());</div>
<div align="left">}</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left">/**</div>
<div align="left">* 定义StudentHandler处理器类，对&lt;student&gt;元素进行处理。</div>
<div align="left">*/</div>
<div align="left"><b>private</b> <b>static</b> <b>class</b> StudentHandler <b>implements</b> ElementHandler {</div>
<div align="left"><b>public</b> <b>void</b> .Start(ElementPath path) {</div>
<div align="left">Element elt = path.getCurrent();</div>
<div align="left">System.<i>out</i>.println("Found student: " + elt.attribut.ue("sn"));</div>
<div align="left">// 添加对子元素&lt;name&gt;的处理器。</div>
<div align="left">path.addHandler("name", <b>new</b> NameHandler());</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left"><b>public</b> <b>void</b> .End(ElementPath path) {</div>
<div align="left">// 移除对子元素&lt;name&gt;的处理器。</div>
<div align="left">path.removeHandler("name");</div>
<div align="left">}</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left">/**</div>
<div align="left">* 定义NameHandler处理器类，对&lt;student&gt;的&lt;name&gt;子元素进行处理。</div>
<div align="left">*/</div>
<div align="left"><b>private</b> <b>static</b> <b>class</b> NameHandler <b>implements</b> ElementHandler {</div>
<div align="left"><b>public</b> <b>void</b> .Start(ElementPath path) {</div>
<div align="left">System.<i>out</i>.println("path : " + path.getPath());</div>
<div align="left">}</div>
<div align="left"></div>
<div align="left"><b>public</b> <b>void</b> .End(ElementPath path) {</div>
<div align="left">Element elt = path.getCurrent();</div>
<div align="left">// 输出&lt;name&gt;元素的名字和它的文本内容。</div>
<div align="left">System.<i>out</i>.println(elt.getName() + " : " + elt.getText());</div>
<div align="left">}</div>
<div align="left">}</div>
<div>}</div></td>
</tr>
</tbody>
</table>
<div><b>6.</b><b>使用</b><b>XSLT</b><b>转换</b><b>XML</b></div>
<div>这里必须使用JAXP的支持。</div>
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td valign="top" width="568">
<div align="left"><b>import</b> javax.xml.transform.Transformer;</div>
<div align="left"><b>import</b> javax.xml.transform.TransformerFactory;</div>
<div align="left"></div>
<div align="left"><b>import</b> org.dom4j.Document;</div>
<div align="left"><b>import</b> org.dom4j.io.DocumentResult;</div>
<div align="left"><b>import</b> org.dom4j.io.DocumentSource;</div>
<div align="left"></div>
<div align="left">……</div>
<div align="left"><b>public</b> Document styleDocument(Document document, String stylesheet)</div>
<div align="left"><b>throws</b> Exception {</div>
<div align="left"></div>
<div align="left">// load the transformer using JAXP</div>
<div align="left">TransformerFactory factory = TransformerFactory.newInstance();</div>
<div align="left">Transformer transformer = factory.newTransformer(<b>new</b>StreamSource(stylesheet));</div>
<div align="left"></div>
<div align="left">// now lets style the given document</div>
<div align="left">DocumentSource source = <b>new</b> DocumentSource(document);</div>
<div align="left">DocumentResult result = <b>new</b> DocumentResult();</div>
<div align="left">transformer.transform(source, result);</div>
<div align="left"></div>
<div align="left">// return the transformed document</div>
<div align="left">Document transformedDoc = result.getDocument();</div>
<div align="left"><b>return</b> transformedDoc;</div>
<div>}</div>
<div>……</div>
&nbsp;</td>
</tr>
</tbody>
</table>
