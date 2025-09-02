---
title: 微数据和Json-LD
date: 2025-09-01 10:00:00
updated: 2025-09-02 11:00:00
---

# 微数据和Json-LD

## 用途

让搜索引擎理解网页内容的“含义”

## 怎么用

https://schema.org 词汇表

1. JSON-LD描述网页的语义
```html
<script type="application/ld+json">
{
  "@context": "https://schema.org", // 这是“词典”的地址
  "@type": "Person",                // "Person" 这个词的定义在 schema.org 上
  "name": "张三",
  "jobTitle": "软件工程师"
}
</script>
```


2. 微数据

- itemtype 描述术语
- itemprop 描述具体属性

```html
<div itemscope itemtype="https://schema.org/Person"> <!-- "Person" 类型的定义来源 -->
  <span itemprop="name">张三</span> <!-- "name" 属性的定义也在 schema.org 上 -->
  <span itemprop="jobTitle">软件工程师</span>
</div>

```
