---
title: css box-sizing
date: 2025-03-03 14:20:47
tags: 
- CSS
categories:
- 前端
---

**前言**

    其实一直没仔细研究过CSS3新增的这个属性box-sizing，只是经常会看到其它网页和公司项目里面有用到这个属性。

要想清楚这个属性的作用，首先要理解盒子模型，盒子模型是指：外边距（margin）+ border（边框） + 内边距（padding）+ content（内容），可以把每一个容器，比如div，都看做是一个盒子模型

比如你给一个div设置宽高为500px，但实际你设置的只是content，之后你又设置了padding:10px;border:1px solid red；

这时div的宽高就会变为544px（content 500px + padding 40px + border 4px）
相当于一个元素的实际宽高是由：　padding + border + content 组成

**定义和用法**
    box-sizing 属性允许您以特定的方式定义匹配某个区域的特定元素。

例如，假如您需要并排放置两个带边框的框，可通过将 box-sizing 设置为 "border-box"。这可令浏览器呈现出带有指定宽度和高度的框，并把边框和内边距放入框中。


盒子模型会自动根据padding和border的值来调整content的值，就不需要手动调整



**一句话总结**：
border-box: 元素的总高度和宽度包含内边距和边框(padding 与 border) :
default: 元素的总高度和宽度应该加上内边距和边框(padding 与 border) :