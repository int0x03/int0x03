---
title: 时序数据异常检测
permalink: /time-series-anomaly-detection/zh-cn/
layout: math-page
lang: zh-CN
---
# 时序异常检测：新手入门课程

不用先背一堵算法名词墙，也能学会发现时序数据中的异常行为。每一课都从一个具体的监控故事开始，一次只增加一个概念，最后给出一份可执行的检查清单。

[English]({{ '/time-series-anomaly-detection/' | relative_url }})

## 贯穿课程的故事

假设某个服务每分钟上报请求量：深夜通常安静，午间总会繁忙；发布新版本有时会改变基线；传感器损坏时可能不断重复同一个值。因此，“异常”不只是“数值很大”。本课程会逐步为这个服务建立检测器。

```text
原始读数 → 可信时间线 → 基线 → 异常分数 → 阈值
		 → 事件评估 → 告警 → 解释与反馈
```

## 学习路线

初次接触异常检测时，请按顺序阅读。第 1～4 课构成必学的统计方法主线；第 5～8 课解释何时以及如何使用更复杂的方法。

| 课次 | 课程 | 核心问题 | 本课产出 |
| --- | --- | --- | --- |
| 1 | [到底什么是异常？]({{ '/time-series-anomaly-detection/zh-cn/lessons/01-what-is-an-anomaly/' | relative_url }}) | 问题属于单点、上下文、序列形状还是状态改变？ | 一份准确的异常定义 |
| 2 | [建立可信的时间线]({{ '/time-series-anomaly-detection/zh-cn/lessons/02-prepare-time-series-data/' | relative_url }}) | 不偷看未来时，数据还可信吗？ | 按时间排序的数据划分 |
| 3 | [建立第一个统计基线]({{ '/time-series-anomaly-detection/zh-cn/lessons/03-statistical-baselines/' | relative_url }}) | 当前值离正常有多远？ | Z-score、中位数绝对偏差（Median Absolute Deviation，MAD）和四分位距（Interquartile Range，IQR）基线 |
| 4 | [处理季节性与持续变化]({{ '/time-series-anomaly-detection/zh-cn/lessons/04-seasonality-and-change/' | relative_url }}) | 它在“这个时刻”异常，还是“正常状态”已经改变？ | 残差分数与变化分数 |
| 5 | [把滑动窗口变成机器学习特征]({{ '/time-series-anomaly-detection/zh-cn/lessons/05-classical-machine-learning/' | relative_url }}) | 异常对象是否是一段多步形状？ | 无泄漏的特征流水线 |
| 6 | [理解深度时序模型]({{ '/time-series-anomaly-detection/zh-cn/lessons/06-deep-time-series-models/' | relative_url }}) | 何时必须考虑复杂时间依赖或传感器间关系？ | 按评分机制选择模型 |
| 7 | [选择阈值并评估事件]({{ '/time-series-anomaly-detection/zh-cn/lessons/07-thresholds-and-evaluation/' | relative_url }}) | 高异常分数能否变成有用的告警？ | 经过校准的事件级评估 |
| 8 | [让检测器走进生产环境]({{ '/time-series-anomaly-detection/zh-cn/lessons/08-production-and-llms/' | relative_url }}) | 上线之后，检测器如何长期保持有效？ | 可监控的检测流程 |

## 始终分清三个问题

只说算法名称，不能完整描述一个解决方案：

1. **异常单元是什么？** 单个点、窗口、事件，还是持续性的状态变化。
2. **怎样产生异常分数？** 规则、距离、预测误差、重构误差、概率或学习到的表示。
3. **模型怎样学习？** 专家规则、无标签数据、已知正常历史、带标签样本或自监督任务。

例如，一个预测模型可以通过自监督任务学习，输出残差分数，最终检测由许多相邻异常点组成的事件。

## 快速选型地图

| 场景 | 建议起点 | 不要忘记 |
| --- | --- | --- |
| 稳定的单一指标 | MAD 或 IQR | 明确处理离散程度为零的情况。 |
| 每日或每周周期 | 季节性残差加 MAD | 只比较处于相同时间位置的数据。 |
| 小幅但持续的偏移 | 指数加权移动平均（Exponentially Weighted Moving Average，EWMA）或累积和（Cumulative Sum，CUSUM） | 验证可以接受的检测延迟。 |
| 异常的窗口形状 | 特征加 Isolation Forest 或局部离群因子（Local Outlier Factor，LOF） | 所有变换都只能在过去数据上拟合。 |
| 相关传感器或长期依赖 | 预测、重构或图模型 | 证明它优于简单基线。 |
| 数值告警伴随丰富日志 | 先用数值检测器，再用大语言模型（Large Language Model，LLM）分诊 | 不要把流畅的文字当作经过校准的分数。 |

## 一条实践原则

从能够表示趋势和季节性的最简单检测器开始。只有当后续从未参与训练的测试时段证明事件召回率、误报或检测延迟得到实质改善时，才增加复杂度，而不能只看训练损失是否更低。

[开始第 1 课 →]({{ '/time-series-anomaly-detection/zh-cn/lessons/01-what-is-an-anomaly/' | relative_url }})

[返回首页]({{ '/' | relative_url }})
