title: ES6 learning
categories: technology
date: 2015-10-11 22:38
comments: true
tags: [前端,计算机]
---
Environment Prepare
* Bable
    * node --harmony
    * node --v8-options | grep harmony
    * npm install --global babel
    * babel-node
    * babel-node es6.js
    * babel es6.js -o es5.js
    * babel -d build-dir source-dir
    * babel -d build-dir source-dir -s
    * use Bable in browser:
    
    ~~~
        <script src="node_modules/babel-core/browser.js"></script>
        <script type="text/babel">
        // Your ES6 code
        </script>
    ~~~
 
<!-- more -->    
     
* Traceur
    
    ~~~
    <!-- 加载Traceur编译器 -->
<script src="http://google.github.io/traceur-compiler/bin/traceur.js"
        type="text/javascript"></script>
<!-- 将Traceur编译器用于网页 -->
<script src="http://google.github.io/traceur-compiler/src/bootstrap.js"
        type="text/javascript"></script>
<!-- 打开实验选项，否则有些特性可能编译不成功 -->
<script>
        traceur.options.experimental = true;
</script>
<script type="module">
  //Your ES6 code
</script>
    ~~~
    * npm install -g traceur
    * traceur es6.js
    * traceur --script es6.js --out es5.js  --experimental


Some commands
* let 
    * 块级作用域
    * 不存在变量提升
    * 暂时性死区[在代码块内，使用let命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称TDZ）]
    * 不允许重复声明
    * 块级作用域外部，无法调用块级作用域内部定义的函数
* const
    * 声明后不可变
    * 同样是块级作用域，不提升，有暂时性死区，不可重复声明
    * const命令只是指向变量所在的地址，所以将一个对象声明为常量必须非常小心，因为可以改变其属性
    * 对对象冻结：Object.freeze
* ES6规定，var命令和function命令声明的全局变量，属于全局对象的属性；let命令、const命令、class命令声明的全局变量，不属于全局对象的属性。

   
跨模块常量：
~~~
    export XXX
    -----
    import * as XXX from XXX
    -----
    import {XXX} from XXX
~~~
    
    
变量的解构赋值
* 如果解构不成功，变量的值就等于undefined
* 解构赋值允许指定默认值
* 如果等号的右边不是数组（或者严格地说，不是可遍历的结构，参见《Iterator》一章），那么将会报错,解构不仅可以用于数组，还可以用于对象
* 类似数组的对象都有一个length属性，因此还可以对这个属性解构赋值
* 不能使用圆括号的情况
    * 变量声明语句中，模式不能带有圆括号
    * 函数参数中，模式不能带有圆括号
    * 不能将整个模式，或嵌套模式中的一层，放在圆括号之中
* 可以使用圆括号的情况只有一种：赋值语句的非模式部分，可以使用圆括号

Iterator
* Waiting
