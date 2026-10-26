---
title: "基于 Agent Skill 的自动化问题诊断框架"
permalink: /reflections/zh-cn/automated-troubleshooting-framework/
---

# 基于 Agent Skill 的自动化问题诊断框架

最近一直在小规模实践和思考如何在大模型时代做自动的生产环境问题分析诊断。
本文的前提是生产环境的数据收集、分析、整理、告警等基础设施已经完善，使用智能体（Agent）来代理人工分析诊断问题的过程，并不断优化和迭代诊断能力。

虽然大家的终极信念都是人工智能（Artificial Intelligence，AI）自动运维，排除 human-in-the-loop，但是现阶段这还是一个理想。尤其在生产环境，一次误操作可能比诊断不出问题的风险更大。

相比于原来的工具都部署在生产环境，现在的 Agent 大多数都部署在本地 corporate 环境。

## 核心设计理念

1. 大模型有通用的分析诊断能力，尤其对于开源产品和通用的技术栈。
2. 公司内部的相对静态信息，需要通过检索增强生成（Retrieval-Augmented Generation，RAG）或者 Skill 来获取，RAG 和 Skill 不断添加、迭代更新。
3. 公司内部的相对动态信息，Skill 可以调用公司的应用程序编程接口（Application Programming Interface，API）或者模型上下文协议（Model Context Protocol，MCP）工具来获取，主要对接 metrics、logs、traces、topology、config 等。

## 基本流程

告警 -> Agent -> Skill/RAG -> API/MCP -> metrics/logs/traces/topology/config。

其中会涉及 Agent 的编排、Memory 的共享、Skill 的层次结构组织及更新、RAG 文档的更新迭代、API 和 MCP 的权限和认证等。

使用 Agent + Skill 的方式，其实是把之前由人完成的事情转换成 Agent 要做的事情：

1. 把人的判断分析改成大模型的分析判断。
2. 把人要做的收集、整理、获取信息改成 Agent 要做的收集、整理、获取信息，Agent 可以并行处理并且更快。
3. 大模型不知道的公司内部相对静态信息，要通过 RAG 或者 Skill 里面的信息获取。
4. 大模型不知道的公司内部相对动态信息，要通过 Skill 使用的 API 或者 MCP 工具来获取。

人工操作和思考都比较慢，Agent 可以做大规模并行收集、分析和验证。

## 难点

1. 人喜欢一图胜千言，迅速浏览一批 dashboard 就能发现可疑的异常点，但 Agent 不擅长读取 metrics 图表。即便喂给它原始数据，它也需要借助其他工具来分析，并且现在没有现成的工具来提供数据 insights，更不用说并行查看多张图表的效率。
2. 要把之前给人看的各种成熟 dashboard 转换成 Agent 能够理解的形式。短文本问题不大，但是对于图表，需要做非常大的改动。
3. 授权和认证以前都是在各种工具上完成的，登录、认证、授权都以人为基础。现在 Agent 虽然可以继承人的身份，但是面向人的、基于用户界面（User Interface，UI）的双因素认证，与基于 API 或者 MCP 的认证完全不同。
4. 对于这个诊断系统的操作边界，要在 Skill 里面加以约束；但要做到边界清晰，还要从 API 和 MCP 层面强制鉴权和授权。

## 事后工作

1. 根据以往经验，人通常只会记录关键结论，并且主动省略走弯路的部分。一旦事情解决，就没有动力记录过程；但是 Agent 可以把整个过程都记录下来，并在后续分析中使用。
2. 事后整理的 trajectory 可能要用到实时数据，这些当时的实时数据要同步保留，供以后重跑并验证 Skill 的能力。

## 实施方式

从实施角度来讲，需要有一个地方保留有用的 Skill，并且可以不断升级迭代。
另外要根据 Skill 的 scope 做分层和分类整理，便于管理和调用。
至于 Agent 的并行能力，现在 Claude 和 OpenCode 提供的能力在一定程度上已经够用。

[返回 Reflections]({{ '/reflections/' | relative_url }})
