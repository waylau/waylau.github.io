---
layout: post
title: 使用SonarQube社区插件cxx检测项目 
声纳四周
date: 2026-08-11 00:22
author: admin
comments: true
categories: [SonarQube]
tags: [SonarQube]
---

SonarQube社区版未不直接提供C++项目的检测。除了Sonar商业版所提供CFamily之外，还可以使用SonarQube社区插件cxx来实现检测C++项目的目的。

本节介绍cxx插件的安装、使用。

<!-- more -->

## 特性

* 支持解析器：
    * `C++03`, `C++11`, `C++14`,`C++17`, `C++20`, `C++23`
    * `C89`, `C99`, `C11`, `C17`
* 编译器专用扩展
    * Microsoft扩展： ，`C++/CLI`、`Attributed ATL`
    * GNU 扩展
    * CUDA 扩展
* Microsoft Windows 和 Linux 运行环境


## 下载插件


务必选择与你的 SonarQube 服务版本相兼容的 cxx 插件版本。插件版本与 SonarQube 的版本兼容关系，请查阅：<https://github.com/SonarOpenCommunity/sonar-cxx/wiki/Compatibility-Matrix>


不匹配的版本会造成 SonarQube 无法启动、功能异常。安装前建议备份。

## 安装插件


1. 前往 cxx 的 [GitHub Releases](https://github.com/SonarOpenCommunity/sonar-cxx/releases) 发布页，下载对应版本的 jar 包，例如：`sonar-cxx-plugin-2.3.0.1496.jar`
2. 将该 jar 文件复制到 SonarQube 服务端的插件目录：`SONARQUBE_HOME/extensions/plugins`
3. **完全停止 SonarQube 服务**。
4. 删除旧版本 sonar‑cxx 的 jar 文件（如果有旧版本）。
5. 重新启动 SonarQube。


## 校验是否安装成功


在SonarQube的 `Administration > Marketplace` 查看已安装插件，列表中出现 `C++ (Community)` 即为安装完成。


## 卸载插件

1. 停止 SonarQube 服务。
2. 在 `extensions/plugins/` 目录删除 `sonar-cxx-plugin-2.3.0.1496.jar`。
3. 重启 SonarQube。

---



## 源代码扫描

最简单的场景下，由 cxx 插件的**编程语言传感器**读取源代码，再通过 SonarScanner 将数据上传至 SonarQube 进行分析。

编程语言传感器负责将源代码切分为词法符号，并依据语法生成抽象语法树（AST）。基于抽象语法树，就可以计算软件度量指标并发现代码问题。

在 SonarQube 中，每一种文件后缀**只能唯一分配给一门编程语言**。如果系统同时运行多个 C/C++ 相关插件，配置时必须注意这一点（参考 `sonar.cxx.file.suffixes` 参数）。

> 💡提示：**只能有一个编程语言传感器读取同一个源文件！**

源文件读取（索引）是后续读取并可视化外部工具报告的前提条件。



> 流程图说明：源代码 → SonarScanner（读取配置）→ SonarQube数据库 → SonarQube服务端（读取配置）→ SonarQube前端UI；数据库存储：问题、度量指标、历史记录、质量规则集、质量门禁；部分语言支持内置静态代码分析，能力取决于SonarQube版本。

## 项目配置
当你的构建系统没有专属扫描器时，就使用 SonarScanner。
在项目根目录创建配置文件 `sonar‑project.properties`：
```properties
# 在SonarQube实例内必须唯一
sonar.projectKey=my:project

# 默认为 projectKey
#sonar.projectName=My project
# 默认为 "not provided"
#sonar.projectVersion=1.0

# 路径相对于 sonar‑project.properties 文件，默认值为 .
#sonar.sources=.

# 源代码编码，默认使用系统默认编码
#sonar.sourceEncoding=UTF‑8

# 必填：指定交由 cxx 插件处理的文件后缀
sonar.cxx.file.suffixes=.h,.cpp
```

## 通过压缩包方式运行 SonarScanner
从官网下载 SonarScanner 并解压（同时阅读前置依赖要求）。编辑解压目录下 `conf/sonar‑scanner.properties`，修改全局配置指向你的 SonarQube 服务端：
```properties
#----- 默认SonarQube服务地址
#sonar.host.url=http://localhost:9000
```

cxx 插件要求运行环境为 **Java 11 或 Java 17**，请安装 JDK11 或更高版本。启动扫描器前，设置环境变量 `JAVA_EXEC` 指向要使用的Java可执行程序。

为方便使用，可以把 `解压目录/bin` 添加到系统环境变量PATH。新开终端执行下面命令验证安装是否成功：
- Linux/macOS：`sonar‑scanner -h`
- Windows：`sonar‑scanner.bat -h`

## 使用Demo项目进行测试
在“Hello World”示例目录中提供了可用于初次验证的样例项目。将示例代码复制到本地目录，在项目根目录执行下面命令，传入你的身份认证令牌：
```bash
sonar‑scanner -Dsonar.login=myAuthenticationToken
```

如果执行成功，输出类似如下：
```
...
17:00:53.401 INFO: Analysis total time: 3.886 s
17:00:53.404 INFO: ------------------------------------------------------------------------
17:00:53.404 INFO: EXECUTION SUCCESS
17:00:53.404 INFO: ------------------------------------------------------------------------
17:00:53.404 INFO: Total time: 5.416s
17:00:53.470 INFO: Final Memory: 23M/481M
17:00:53.471 INFO: ------------------------------------------------------------------------
```

# 故障排查
- 需要更多细节，可以**开启调试日志**。
- 如果扫描失败，请查阅「配置故障排查」页面。
- 如果扫描结果不完整，请参考「检测并修复解析错误」给出的处理建议。
- 「缩小扫描范围」文档介绍如何配置，精确控制哪些内容参与分析。

# 理解扫描器日志文件
下面给出扫描日志样例，并解释关键步骤。想要获取完整信息，**扫描时务必开启调试模式**。

日志第一部分输出扫描器版本、加载的配置，同时可以看到Java版本、SonarQube服务端版本。
```
01 INFO: SonarScanner 4.5.0.2216
02 INFO: Java 11.0.2 Oracle Corporation (64‑bit)
03 INFO: Scanner configuration file: /tmp/sonar‑scanner‑4.5.0.2216/conf/sonar‑scanner.properties
04 INFO: Project root configuration file: /home/projects/cppcheck_sample/sonar‑project.properties
05 INFO: Analyzing on SonarQube server 8.7.0
```

`Plugins` 段落列出全部已加载插件及其版本，这里应当可以看到 `C++ (Community)`（cxx插件）。
```
07 DEBUG: Plugins:
08 DEBUG: * C++ (Community) 2.0.0.1234 (cxx)
```

`Project key` 是该项目在SonarQube服务中的唯一标识。**基准目录(base dir)** 尤为重要：所有相对路径都以此目录作为参照。
```
10 INFO: Project key: cppcheck_sample
11 INFO: Base dir: /home/projects/cppcheck_sample
12 INFO: Working dir: /home/projects/cppcheck_sample/.scannerwork
13 DEBUG: Project global encoding: UTF‑8, default locale: en_US
```

`Available languages` 列出SonarQube服务支持的编程语言。`Declared extensions` 展示绑定到各语言的文件后缀。**每个文件后缀只能分配给一门语言**。
```
15 DEBUG: Available languages:
16 DEBUG: * CXX => "cxx"
17 DEBUG: Declared extensions of language CXX were converted to sonar.lang.patterns.cxx : **/*.cxx,**/*.cpp,**/*.cc,**/*.c,**/*.hxx,**/*.hpp,**/*.hh,**/*.h
```

项目分析的第一步永远是 `Indexing files`（文件索引）：依据文件后缀把文件分配给对应编程语言。不在列表中的文件不会在SonarQube前端页面展示。文件过滤规则可查阅「缩小扫描范围」文档。
```
19 INFO: Indexing files...
20 INFO: Project configuration:
21 DEBUG: 'src/component1.hh' indexed with language 'cxx'
22 DEBUG: 'src/component1.cc' indexed with language 'cxx'
23 DEBUG: 'src/main.cc' indexed with language 'cxx'
24 INFO: 3 files indexed
```

`Run sensors on project` 阶段首先判断哪些传感器需要运行，未配置的传感器直接跳过；之后依次执行各个传感器。
```
28 INFO: ------------- Run sensors on project
29 DEBUG: 'CXX Bullseye coverage report import' skipped because one of the required properties is missing
30 ...
31 DEBUG: Sensors : CXX -> CXX Cppcheck report import -> Zero Coverage Sensor
```

每个传感器的执行日志以 `Sensor Name` 开头，以 `Sensor Name (done)` 结尾。cxx插件的传感器名称为 `CXX`。
示例日志中会先输出度量阈值；`global include directories` 列出预处理器搜索头文件的目录；`global macros` 显示预处理器使用的宏定义。每个待处理单元会输出 `process unit`，发生的错误都归属到上一个正在处理的单元。
```
32 INFO: Sensor CXX [cxx]
33 DEBUG: sonar.cxx.metric.api.file.suffixes: [.hxx, .hpp, .hh, .h]
34 DEBUG: 'Complex Functions' metric threshold (cyclomatic complexity): 10
35 DEBUG: 'Big Functions' metric threshold (LOC): 20
36 DEBUG: global include directories: [/home/projects/cppcheck_sample/src]
37 DEBUG: global macros: [{__STDC__:1}, {__TIME__:"??:??:??"}, {__STDC_HOSTED__:1}, {__FILE__:"file"}, {__DATE__:"??? ?? ????"}, {__has_include:1}, {__cplusplus:201103L}, {__LINE__:1}]
38 DEBUG: process unit '/home/projects/cppcheck_sample/src/component1.hh'
39 DEBUG: 'Public API' metric for 'component1.hh': total=3, undocumented=3
40 DEBUG: process unit '/home/projects/cppcheck_sample/src/component1.cc'
41 DEBUG: process unit '/home/projects/cppcheck_sample/src/main.cc'
42 INFO: Sensor CXX [cxx] (done) | time=808ms
```

源代码分析与报告导入全部成功完成后，日志末尾输出 `EXECUTION SUCCESS`。关于报告文件导入的更多内容，参考「扫描报告文件」文档。
```
44 INFO Analysis total time: 11.435 s
45 INFO: ------------------------------------------------------------------------
46 INFO: EXECUTION SUCCESS
47 INFO: ------------------------------------------------------------------------
48 INFO: Total time: 13.426s
49 INFO: Final Memory: 7M/27M
50 INFO: ------------------------------------------------------------------------
```




## 参考引用

* <https://github.com/SonarOpenCommunity/sonar-cxx/wiki/Install-the-Plugin>
* <https://github.com/SonarOpenCommunity/sonar‑cxx/wiki/Scan‑Source‑Code>