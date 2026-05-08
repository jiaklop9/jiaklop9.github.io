---
title: 'web逆向: md5'
date: 2025-04-17 10:21:03
tags: web逆向
categories: web逆向
---

## md5加密字符串一般长度为16或者32

## 函数跳转，鼠标放上去，出现后点击

## HttpOnly含义

生成cookie时使用HttpOnly标志有助于降低客户端脚本访问受保护cookie的风险（如果浏览器支持）。
这个意思就是说，如果某一个Cookie 选项被设置成 HttpOnly = true 的话，那此Cookie 只能通过服务器端修改，Js 是操作不了的，对于 document.cookie 来说是透明的。


# acw_tc = /ktas/v0/report set_cookie


## Object(xx) 含义


在JavaScript中，Object(d.a) 这种写法是一种显式类型转换（type coercion），它的作用是将 d.a 的值转换为一个对象（Object）。具体行为如下：

### 1. 如果 d.a 是原始值（primitive）：
比如字符串、数字、布尔值、null、undefined 等，Object() 会将其包装成对应的包装对象：

```javascript
Object("hello")  // String {"hello"} （包装成字符串对象）
Object(123)     // Number {123} （包装成数字对象）
Object(true)    // Boolean {true} （包装成布尔对象）
```
例外：

null 和 undefined 会转换为一个空对象：

```javascript
Object(null)      // {} （空对象）
Object(undefined) // {} （空对象）
```

### 2. 如果 d.a 已经是对象：
Object() 会直接返回该对象本身（不做任何转换）：

```javascript
const obj = { name: "test" };
Object(obj) === obj  // true （返回原对象）
```
### 3. 为什么这样写？
确保结果是一个对象（避免原始值无法调用方法或访问属性）。

防御性编程：处理可能的 null/undefined，将其转换为空对象 {}。

举例：

```javascript
const d = { a: "hello" };
Object(d.a).toString(); // 安全调用方法（虽然字符串原始值本身也有方法）
```
### 4. 常见类似用法：
Object.assign(target, source) 中，如果 source 是原始值，会先被 Object() 转换。

与 new Object() 的区别：Object() 作为函数调用时，行为更灵活（无需 new）。

### 5. 新生建议：
优先使用更明确的类型转换（如 String()、Number() 等），除非需要处理不确定的输入。
避免过度依赖隐式转换，容易引发 bug。