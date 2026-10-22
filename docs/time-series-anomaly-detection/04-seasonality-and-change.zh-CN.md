---
title: "第 4 课：处理季节性与持续变化"
permalink: /time-series-anomaly-detection/zh-cn/lessons/04-seasonality-and-change/
layout: math-page
lang: zh-CN
---

# 第 4 课：处理季节性与持续变化

[English]({{ '/time-series-anomaly-detection/lessons/04-seasonality-and-change/' | relative_url }})

## 大数值也可能完全正常

同样是每分钟 200 个请求，含义可能完全不同：

```text
03:00 基线：  18  22  20  [200]  ← 对这个时刻来说异常
12:00 基线： 190 205 198  [200]  ← 对这个时刻来说正常
```

全局阈值会忘记时间上下文。季节基线应该把周一 12:00 与过去几个周一的 12:00 前后比较，而不是与历史上的每一分钟比较。

## 先去掉预期部分，再为剩余部分评分

一种分解方法把观测值 $$x_t$$ 写成趋势 $$T_t$$、季节项 $$S_t$$ 和残差 $$r_t$$：

$$
x_t = T_t + S_t + r_t
$$

```text
观测值：  重复的山峰 + 一次尖峰
预期值：  重复的山峰
残差：    接近平线   + 清晰尖峰
```

只使用过去数据估计预期行为，计算 $$r_t = x_t - T_t - S_t$$，再使用中位数绝对偏差（Median Absolute Deviation，MAD）等稳健方法为残差评分。节假日等日历效应可能需要独立上下文，不能假设“星期几”会自动解释它们。

## 预测区间表达不断变化的不确定性

预测模型给出预期值 $$\hat{x}_t$$。预测残差为：

$$
e_t = x_t - \hat{x}_t
$$

预测区间不用固定误差上限，而是在未来更不确定时变宽：

```text
上界  · · · · · · · · · · ·       × 观测值落在区间外
预测  ────────────────
下界  · · · · · · · · · · ·
```

指数平滑、状态空间模型或自回归积分滑动平均（Autoregressive Integrated Moving Average，ARIMA）模型都可以产生预测。必须在更晚的数据上检查区间覆盖率：当模型与现实不匹配时，理论上的 95% 区间未必真能覆盖 95%。

## 寻找小幅但持续的偏移

逐点检测器可能忽略请求量从 20 持续变成 23 的变化。两种经典过程监控工具特别关注持续性：

- **指数加权移动平均（Exponentially Weighted Moving Average，EWMA）**平滑观测值，同时让近期数据拥有更高权重。它能显示渐进移动，又不会追随每个噪声尖峰。
- **累积和（Cumulative Sum，CUSUM）**不断累积相对于目标的偏差。同方向的小偏差最终会形成很大的分数。

```text
偏差：      +1  -1  +1  +1  +1  +1
CUSUM 思路： 1   0   1   2   3   4  → 持续证据不断增强
```

**变点**标记两个状态之间的边界。确认变化合理后，重新训练或重置基线可能是正确选择。但每次告警后立即重置，也可能让检测器把正在持续的事故学成正常。

## 哪种工具符合故事？

| 故事 | 建议起点 |
| --- | --- |
| 数值不符合当前小时或星期 | 季节残差加 MAD |
| 观测值超出不断变化的预期范围 | 预测区间 |
| 相对于受控目标的小幅持续漂移 | EWMA 或 CUSUM |
| 需要找到长期状态的分界点 | 变点检测方法 |

## 本课检查清单

- [ ] 列出季节周期和已知日历效应。
- [ ] 只使用过去数据估计趋势、季节性、预测和区间。
- [ ] 在更晚时段验证残差行为和区间覆盖率。
- [ ] 确定要为偏差、持续性还是状态边界告警。
- [ ] 定义何时允许新状态取代旧基线。

[← 上一课]({{ '/time-series-anomaly-detection/zh-cn/lessons/03-statistical-baselines/' | relative_url }}) ·
[课程目录]({{ '/time-series-anomaly-detection/zh-cn/' | relative_url }}) ·
[下一课：传统机器学习 →]({{ '/time-series-anomaly-detection/zh-cn/lessons/05-classical-machine-learning/' | relative_url }})
