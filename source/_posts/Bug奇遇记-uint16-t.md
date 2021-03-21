---
title: Bug奇遇记-uint16_t
date: 2021-03-19 20:47:11
categories:
- works
- GF
- data type
- bugs
tags:
 - cpp
 - osg
---

### uint16_t 

- 标准定义中：

```c++
typedef unsigned short  uint16_t; // max == _UI16_MAX 0xffffu 
```

![uint16_t](../uploads/uint16_t.png)

#### 问题：

- <font color=red>这是一个粗心的问题</font>

在我正在实现的功能中有一个数据遍历的部分，我使用了`uint16_t`类型作为索引的类型，但是`uint16_t`数据的最大值比较小，所以当基础数据量过大的时候，我们产生的索引会越过索引的最大范围，导致最终得到的索引不是实际需要的索引值，导致我最终绘制的图形不是正确的结果！

---

### 正确的结果展示：

![lum_2](../images/lum_arrow_2.png "result")



![right result](../images/lum_arrow_1.png "Result")

