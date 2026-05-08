---
title: js中的属性描述符及hook应用
date: 2025-11-12 11:41:53
tags: JavaScript
categories: 前端
---


# JavaScript属性描述符详解

JavaScript的属性描述符特性是ES5引入的一种能够精确控制对象属性行为的机制。<br>
通过属性描述符，我们可以定义属性的值（value）、可写性（writable）、可枚举性（enumerable）和可配置性（configurable）<br>同时也可以使用访问器属性（get和set）来拦截对属性的读取和赋值操作。

## 属性描述符有两种主要类型：
#### 数据描述符：具有值的属性，可以选择是否可写。

* value：属性的值。
* writable：如果为true，则属性可以被修改，默认为false。
* enumerable：如果为true，则属性可以在枚举对象属性时（如for...in循环）被枚举，默认为false。
* configurable：如果为true，则属性可以被删除，也可以被修改，默认为false。

#### 访问器描述符：由getter和setter函数描述的属性。

* get：在读取属性时调用的函数，默认为undefined。
* set：在设置属性时调用的函数，默认为undefined。
* enumerable：同上。
* configurable：同上。

#### 为什么能够拦截对特定属性的所有访问和修改操作？
**当我们使用Object.defineProperty来定义一个属性的访问器描述符（即提供了get和set方法）时，每当代码中读取该属性就会调用get方法，而每当给该属性赋值时就会调用set方法。这就使得我们有机会在get和set函数中插入自定义的逻辑，比如记录日志、触发debugger、验证值等，从而实现对属性访问和修改的拦截。**


```js
// 保存原始的cookie值或行为
let originalCookieValue = '';

// 拦截document.cookie
Object.defineProperty(document, 'cookie', {
    // 当设置cookie时触发
    set: function(value) {
        console.log('拦截到cookie设置:', value);
        
        // 可以添加条件断点
        if (value.includes('token')) {
            debugger; // 当设置包含'token'的cookie时暂停
        }
        
        // 保存值或执行原始操作
        originalCookieValue = value;
        // 如果需要继续原始行为，可能需要更复杂的实现
        return value;
    },
    
    // 当读取cookie时触发
    get: function() {
        console.log('拦截到cookie读取');
        
        // 可以修改返回值
        return originalCookieValue || '拦截后的值';
    }
});
```