---
layout: post
title: Model Context Protocol（MCP）简介
date: 2025-04-09 00:22
author: admin
comments: true
categories: [AI]
tags: [MCP, AI]
---


近日，GitHub 正式开源了一款全新的Model Context Protocol（MCP）服务器，与 GitHub API 无缝集成，为开发者带来更高效的工作流体验。据悉，该服务器由 GitHub 联合 Anthropic 开发，采用 Go 语言重写，相较旧版功能更强大且易用。它保留了旧版服务器的所有功能，并新增了对自动化 GitHub 工作流的支持，能够从 GitHub 仓库中提取问题、获取信息等。

本文介绍 Model Context Protocol（MCP）概念及作用。

<!-- more -->


## MCP的概念

MCP 的全称是Model Context Protocol，也就是模型上下文协议，是由Claude的母公司Anthropic于2024年低开源发布的，MCP就是AI大模型的标准化工具箱，可以把它想象成 AI 大模型和外部数据、工具之间沟通的 “通用语言” 或者 “万能插座”，大模型可以利用这些工具与外界互动，获取信息并且完成具体任务。


MCP 是一个开放协议，它为应用程序向 LLM 提供上下文的方式进行了标准化。可以将 MCP 想象成 AI 应用程序的 USB-C 接口。就像 USB-C 为设备连接各种外设和配件提供了标准化的方式一样，MCP 为 AI 模型连接各种数据源和工具提供了标准化的接口。

MCP可以代替人类访问并且操作外部工具，每个MCP服务或者叫做MCP Server都专精于一类的工作：比如有的负责读写浏览器、有的负责读写本地文件、有的操作Git仓库等等，MCP Server 通常就是运行在本地的一段 Java 或者 Python 程序。MCP 提供包括 Python、TypeScript、Java、Kotlin、C# 等在内的众多SDK。


MCP 帮助在 LLM 的基础上构建代理（agents）和复杂的工作流。LLM 经常需要与数据和工具集成，而 MCP 提供了：

* 持续增长的预构建集成列表，LLM 可直接使用
* 灵活切换不同的 LLM 提供商和厂商
* 在你的基础设施内安全地处理数据的最佳实践



## MCP通用架构

MCP 核心采用CS（客户端-服务器）架构，如下图所示，主机应用可以连接多个服务器：


![MCP通用架构](/images/post/20250409-mcp.png)


* MCP Hosts: 如 Claude Desktop、IDE 或 AI 工具，希望通过 MCP 访问数据的程序
* MCP Clients: 维护与服务器一对一连接的协议客户端
* MCP Servers: 轻量级程序，通过标准的 Model Context Protocol 提供特定能力
* Local Data Sources: MCP 服务器可安全访问的计算机文件、数据库和服务
* Remote Services: MCP 服务器可连接的互联网上的外部系统（如通过 APIs）


## MCP生态

GitHub MCP 服务器的开源标志着 AI 与开发工具整合的进一步深化。无论是优化代码管理还是实现自动化任务，它都为开发者提供了强大支持。

MCP 服务器可在多种环境中运行，包括 VS Code 的 Agent Mode、Claude Desktop，以及任何支持 MCP 协议的平台。随着 MCP 生态的日益完善，其灵活性和扩展性受到广泛关注。未来，随着更多开发者加入 MCP 生态，其应用场景有望进一步扩展。

如果你感兴趣的话，其他 MCP 服务器可以在这个[MCP服务市场](https://smithery.ai)找到。



## MCP与Function calling的区别和联系

大模型的两大缺陷：大模型受限于计算资源和训练时间，导致信息滞后；其基于统计规律的回答缺乏真正的逻辑推理能力。


OpenAI 在2023年6月份时给出了解决方案——[Function calling](https://openai.com/index/function-calling-and-other-api-updates/)（函数调用） 。大语言模型本身相当于一个被关在铁笼子里的专家，无法了解外部信息也无法和外部交互，但Function calling相当于提供了一个跑腿小弟给这个专家，由他代劳信息搜集和数据交互的工作，详细的知识图解见下。



![Function calling原理](/images/post/20250409-mcp-002.png)


Function calling简单来说就是AI调用外部工具的能力，是某些大模型（如 GPT-4）提供的专有接口特性。它允许模型直接生成结构化的函数调用请求（如查询天气、计算数值），由宿主应用执行并返回结果。本质是模型内部的功能扩展，依赖于特定厂商的实现，无强制标准协议。每一个 Function 都是需要开发，每一个大语言模型需要依赖的外部工具都需要开发者写好对应的函数来适配，因此，开发 Function 成本很高。

MCP的最大优点是集合了之前各家大模型不同的 Function calling 的标准，整合成了一个统一的标准协议，都可以接入MCP，类似于 AI 领域的 “USB-C 接口”。它通过统一的通信规范（如 JSON-RPC 2.0），解决大模型与外部工具、数据源之间的兼容性问题。核心目标是让不同模型和工具能高效互操作，开发者只需按 MCP 标准开发一次接口，即可被多个模型调用。例如，开发者 A 依照MCP规范写出来的 Function A，可以直接开放给开发者 B 使用，相当于把原本独立的 Function 开发过程变成一定意义上的协同开发，大幅提升了开发效率，不用再去重复造轮子。

简而言之，MCP 解决 “如何让 AI 连接万物” 的通用性问题，与 Function calling 互补协作，共同支撑 AI 从对话走向自动化任务执行。


![MCP与Function calling的区别和联系](/images/post/20250409-mcp-003.png)




## 参考资料



* 《跟老卫学AI大模型开发》开源免费教程：<https://github.com/waylau/ai-large-model-tutorial/>
* GitHub MCP 服务器: <https://github.com/github/github-mcp-server>
* Model Context Protocol规范：<https://modelcontextprotocol.io/>
* MCP——为你的大模型插上翅膀：<https://www.woshipm.com/ai/6193315.html>
