---
title: JS中的原型及原型链
date: 2026-06-16 08:01:32
tags: JavaScript
categories: 前端
series: js
---


# 一、原型链
提起 JavaScript 的“原型对象”（Prototype），很多刚入门的小伙伴都会觉得脑子嗡的一下。别慌，我们继续用大白话和生活中的例子来攻克它！

如果说 **Property（属性）** 是贴在某个具体对象上的标签，那么 **Prototype（原型对象）** 就是这些对象的“共享模板”或“共享仓库”。

## 🏭 一个大白话比喻：共享充电宝 / 乐高积木
假设你和你的朋友们每人买了一台一模一样的“掌上游戏机”（对象）。

* 自有属性（Property）： 你的游戏机贴了张贴纸，你朋友的游戏机外壳有划痕。这些属于你们各自机器的专属特征。

* 原型对象（Prototype）： 厂家在出厂时，并没有给每台游戏机都内置1000款游戏（因为太占内存空间了）。厂家在云端放了一个“共享游戏库”，所有买这款游戏机的人，都可以通过联网直接玩里面的游戏。

这个“云端共享游戏库”，在 JavaScript 里就叫做 **原型对象（Prototype）**。

## 💻 为什么要用原型对象？（解决痛点）
我们来看一个实际的代码场景。假设我们要批量制造“小兵”对象：
```JavaScript
function Soldier(name) {
    this.name = name;
    this.hp = 100; // 每个小兵都有自己的生命值
    
    // 如果我们把方法写在里面：
    this.attack = function() {
        console.log(this.name + " 发起攻击！");
    };
}

let s1 = new Soldier("小兵A");
let s2 = new Soldier("小兵B");
```
问题来了： 如果我创建了 10 万个小兵，内存里就会有 10 万个一模一样的 attack 函数。这太浪费内存空间了！

## 💡 解决方案：把方法放进“原型对象”
我们把 attack 这个动作放进他们的“共享仓库”里，所有小兵共享这一个函数：

```JavaScript
function Soldier(name) {
    this.name = name;
    this.hp = 100;
}

// 把攻击方法放到“原型对象（prototype）”上
Soldier.prototype.attack = function() {
    console.log(this.name + " 发起攻击！");
};

let s1 = new Soldier("小兵A");
let s2 = new Soldier("小兵B");

s1.attack(); // 输出: 小兵A 发起攻击！
s2.attack(); // 输出: 小兵B 发起攻击！
```
这时候，内存里只有 1 个 attack 函数。s1 和 s2 都可以跑去这个共享仓库里调用它。

## 🔍 JS 是怎么在原型里找东西的？（原型链）
当你在代码里写 s1.attack() 时，JavaScript 引擎会像侦探一样去破案，寻找这个属性：

1. 第一步（看自己）： 问 s1 自己：“你身上有 attack 这个属性吗？”

答：没有，我身上只有 name 和 hp。

2. 第二步（看仓库）： 顺着一根隐形的线，找到 s1 的原型对象（共享仓库），问：“你这里有 attack 吗？”

答：有的！ 于是 JS 成功执行了该方法。

3. 第三步（如果还没有）： 如果原型对象里也没有，它会继续往更高级的“大原型”去找，直到找到头为止（最顶层是 Object.prototype）。如果一圈找下来都没有，就会报错 undefined。

这根由隐形线串起来的“寻找路线”，就叫做 **原型链（Prototype Chain）**。

## 🛠️ 现实中天天在用的例子
其实你天天都在用原型，只是你之前没发现。

还记得上一节的字符串吗？

```JavaScript
let str = "hello";
console.log(str.toUpperCase()); // 输出: HELLO
```
你并没有给 str 定义过 toUpperCase（转大写）这个方法对吧？那是谁给它的？
答案：它存在于字符串的“共享仓库”——String.prototype 里面。

## 📝 一分钟核心总结
* **什么是原型对象？** 它是一个普通的对象，作为“共享仓库”存在，用来存放让同类对象共享的属性和方法。

* **为什么要用它？** 节省内存。不需要给每个实例都复制一份一模一样的方法。

* **怎么运作的？** 找属性时，自己有就用自己的；自己没有，就顺着“原型链”去共享仓库里找。


# 二、原型

在上面的例子中，Soldier 的原型对象，准确来说是 Soldier.prototype 指向的那个对象。

我们用一句话来定义它：

Soldier **的原型对象，就是 s1 和 s2（所有被制造出来的小兵）的“共享仓库”。**

为了彻底理清，我们需要分清两个长得很像、但完全不同的“线索”。这也是所有 JS 初学者最容易绕晕的地方：

### 2.1. 它是构造函数的“属性”：Soldier.prototype
当你写下 function Soldier() {} 的那一刻，JavaScript 就会在幕后自动帮你做两件事：

    1. 创建了这个名为 Soldier 的函数。

    2. 自动给这个函数分配了一个附带的小本子（属性），叫做 prototype。这个小本子指向的位置，就是我们所说的原型对象（共享仓库）。

此时，这个仓库是空的。所以我们在代码里手动往里塞了一个攻击技能：

```JavaScript
Soldier.prototype.attack = function() { ... };
```
这时候，这个“原型对象”里就有一个叫做 attack 的方法了。

### 2.2 它是实例对象的“靠山”：__proto__
当你用 new Soldier("小兵A") 制造出 s1 时，JS 会在 s1 内部连接一根隐形的线，指向 Soldier 的那个共享仓库。

在代码里，我们可以通过 __proto__（前后各两个下划线，读作 "dunder proto"）来查看这根线连到了哪里：

```JavaScript
// 验证一下：s1 的“靠山/仓库” 是不是就是 Soldier 的那个小本子指向的地方？
console.log(s1.__proto__ === Soldier.prototype); // 输出: true
```
## 📊 一张图看清三者关系
我们可以把它们的关系画成一个铁三角：

```Plaintext
   【Soldier 构造函数】
       |
       |  .prototype (手里牵着的仓库钥匙)
       v
   【Soldier.prototype 原型对象 (共享仓库)】 <--- 里面躺着 attack()
       ^
       |  .__proto__ (小兵内部认祖归宗的隐形线)
       |
   【s1 实例对象 (小兵A)】
```
对 Soldier 来说： “这是我出厂配置的共享仓库，写在我的 prototype 属性里。”

对 s1（小兵A）来说： “这是我的原型对象，我可以通过 __proto__ 找到它。当我不会 attack 的时候，我就去这里偷学。”

## 💡 终极一问：那这个“原型对象”本身长什么样？
如果你在浏览器控制台打印出 Soldier.prototype，你会发现它本质上就是一个普通的 JavaScript 对象，里面长这样：

```JavaScript
{
    attack: function() { ... },  // 你放进去的攻击方法
    constructor: Soldier         // 仓库自带的标签，记录了这个仓库是谁的
}
```
所以，Soldier 的原型对象，就是那个专门用来给所有小兵实例提供共享方法（如 attack）的、实实在在存在于内存中的对象。