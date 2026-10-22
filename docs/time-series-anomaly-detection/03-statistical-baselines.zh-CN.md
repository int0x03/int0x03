---
title: "第 3 课：建立第一个统计基线"
permalink: /time-series-anomaly-detection/zh-cn/lessons/03-statistical-baselines/
layout: math-page
lang: zh-CN
---

# 第 3 课：建立第一个统计基线

[English]({{ '/time-series-anomaly-detection/lessons/03-statistical-baselines/' | relative_url }})

## 例子：35 算异常吗？

最近几个夜间的请求量是 `19, 20, 20, 21, 20, 19, 35`。统计基线要回答两个问题：

1. 正常历史的中心在哪里？
2. 正常历史的离散程度有多大？

异常分数用“正常离散程度”作为尺子，衡量观测值离中心有多远。分数负责排序；另一个独立的阈值负责把分数变成告警。

## Z-score：容易理解，也容易被污染

对于观测值 $$x_t$$、历史均值 $$\mu$$ 和标准差 $$\sigma$$：

$$
z_t = \frac{x_t - \mu}{\sigma}
$$

分数为 3，表示“比均值高三个历史标准差”。对于正态分布，大约 99.7% 的观测值落在 -3 到 3 之间；这是对覆盖范围的描述，并不表示某个点有 99.7% 的概率属于异常。常见规则 `abs(z) > 3` 只是起点，不是通用阈值。

一次极端历史事故就能拉高均值并放大标准差。结果是后续观测的 Z-score 变小，新的事故反而更难被发现。

## 中位数绝对偏差：一把稳健的尺子

即使存在少量极端值，中位数仍会留在数据中部。中位数绝对偏差（Median Absolute Deviation，MAD）衡量每个值到中位数的典型绝对距离：

$$
\operatorname{MAD} = \operatorname{median}(|x_i - \operatorname{median}(x)|)
$$

常用的缩放修正 Z-score 为：

$$
m_t = \frac{0.6745(x_t - \operatorname{median}(x))}{\operatorname{MAD}}
$$

当数据服从正态分布时，系数 `0.6745` 让这个尺度可与标准差比较。稳健并不代表无懈可击：如果大部分训练数据都有问题，中位数描述的同样是错误过程。

## 四分位距：围住中间一半数据

令 $$Q_1$$ 和 $$Q_3$$ 分别表示第 25 和第 75 百分位数。四分位距（Interquartile Range，IQR）为：

$$
\operatorname{IQR} = Q_3 - Q_1
$$

箱线图常用的围栏是 $$Q_1 - 1.5\operatorname{IQR}$$ 和 $$Q_3 + 1.5\operatorname{IQR}$$。

```text
下围栏          Q1         中位数         Q3          上围栏
---|-------------[==========|============]-------------|---
                    中间 50% 的观测值
```

倍数 `1.5` 同样只是需要验证的惯例，并非定律。

## 全局历史、滚动历史还是季节历史？

| 基线 | 当前点与谁比较 | 适用情况 |
| --- | --- | --- |
| 全局基线 | 所有认可的历史 | 过程稳定。 |
| 滚动基线 | 一段尾随窗口 | 水平缓慢变化。 |
| 季节基线 | 过去相同小时或日期 | 每日或每周模式占主导。 |

滚动窗口必须在被评分点之前结束。窗口太短，适应很快，却可能把事故吸收到基线中；窗口太长，更稳定，却难以及时跟上合理变化。

## 边界情况也是方法的一部分

- 当 $$\sigma = 0$$ 或 $$\operatorname{MAD} = 0$$ 时，不要除以零。可以直接标记发生变化的值，或改用其他离散程度估计。
- 确定要检测向上、向下还是双向偏差。
- 在历史数据足够之前不要评分，并把预热期标记为不可用。
- 只能按照事先记录的规则排除已知维护，不能看完结果再删除。

## 本课检查清单

- [ ] 根据异常污染程度和分布形状选择中心与离散程度。
- [ ] 选择全局、尾随或季节参考历史。
- [ ] 把数值分数与告警阈值分开。
- [ ] 定义零离散、预热、缺失数据和单向检测行为。
- [ ] 保存这条简单基线，供后续所有模型比较。

[← 上一课]({{ '/time-series-anomaly-detection/zh-cn/lessons/02-prepare-time-series-data/' | relative_url }}) ·
[课程目录]({{ '/time-series-anomaly-detection/zh-cn/' | relative_url }}) ·
[下一课：季节性与变化 →]({{ '/time-series-anomaly-detection/zh-cn/lessons/04-seasonality-and-change/' | relative_url }})
