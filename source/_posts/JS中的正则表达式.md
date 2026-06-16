---
title: JS中的正则表达式
date: 2025-05-12 08:47:11
tags: JavaScript
categories: 前端
series: js
---

# JavaScript中的正则表达式常见形式

## 1. 基本形式

```js
/hello/
```

## 2. 常见参数

```js
// 忽略大小写
/hello/i

// 全局匹配
/hello/g

// 匹配任意字符
/[+-\s]/
```




## 2. 匹配数字: 指数表达式匹配

```js
/[0-9]e[0-9]/

// 匹配1次到多次
/[0-9]+e[0-9]+/

// 数字匹配简写形式, shorthands
/\d+e\d+/
```

## 3. 其他: TODO 
