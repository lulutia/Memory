title: JavaScript中的delete一探
categories: technology
tags: [前端,计算机]
---
##JavaScript中的delete
* 核心内容借鉴自下面的[网址](http://perfectionkills.com/understanding-delete/)
* delete一般用法：
    * 删除对象的属性 
        
    ```JavaScript:
    var a={p:21};
    console.log(a.p);//21
    delete a.p;//true
    console.log(a.p);//undefined
    ```
    * 无法删除一般的变量
   
    ```JavaScript:
    var x=13;
    console.log(x);//13
    delete x;//false
    console.log(x);//13
    ```
    * 无法删除函数 
     
    ```JavaScript:
    var p=function(){};
    delete p;//false
    typeof(p);//function
    ```
    
* 造成上述表现的原因
    * 在ECMAScript中，有三种可执行代码类型：全局代码、函数代码、eval代码。对于以上几种代码都有相应的执行上下文。每个执行上下文又对应有变量对象，**在源代码中声明的变量和方法实际上都是作为属性被加入到与当前上下文相关联的这个对象当中**。
        * 全局代码时：变量对象是全局对象。
      
        ```JavaScript:
        var global=33;
        window.global;//33
        global===window.global;//true
        function uu(){};
        typeof window.uu;//"function"
        window.uu===uu;//true
        ``` 
        * 函数代码：此时有活动对象的概念。**在函数段中，并不是只有显式声明的变量和函数会成为活动对象的属性，对于每个函数中隐式存在的arguments对象（函数的参数列表）也是一样的**。
        * eval函数：进入eval代码时并不会新建新的变量对象，而是沿用当前的环境。
    * 每一个变量属性都可以有以下任意多个属性: ReadOnly, DontEnum, DontDelete, Internal。在声明变量或者函数时，他们都变成了当前上下文对象的属性，这些属性在创建时 都带有DontDelete标记；但是显式或者隐式的赋值语句所产生的属性并不会带有这个标记。
  
    ```JavaScript:
    var test=1;
    delete test;//false
    test2 = 2;
    delete test2;//true
    ```   
    * 一些内建的对象是自动持有DontDelete这个标记的，从而不能被删除，比如函数内的arguments，函数的传入参数以及函数的length属性。
    * 属性标记诸如DontDelete是在这个属性被创建的时候 产生的，之后对该属性的任何赋值都不会改变此属性的标记。
    
    ```JavaScript:
    function y(){};
    delete y;//false
    y=1;
    delete y;//false
    typeof y;//"number"
    ```
    * 在eval中声明的变量创建时都不会带有DontDelete标记,在函数内部也一样。
    
    ```JavaScript:
    eval('var ii=34;');
    delete ii;//true
    ```
* 在Firebug控制台中的代码最终将通过eval执行，而不是作为全局代码或函数代码。
* IE6-8中的问题：
     * 通过全局this变量去删除属性(delete this.x)总会出错
     * 直接删除该属性(delete x)时：
        * 如果x是通过全局this赋值产生会(this.x=1)导致错误；
        * 如果x通过显式声明创建(var x=1)则delete会像我们预料的那样无法删除并返回false；
        * 如果x通过非声明式赋值创建(x=1)则delete可以正常删除。
     * 原因： "IE的全局变量对象是通过JScript实现，而一般的全局变量是由host实现的。"
* 对于宿主对象而言，delete操作的结果是不可预料的。因为宿主对象根据不同浏览器的实现允许有不同的行为，这其中包括了delete。
* strict mode采用了更主动并且描述性的方法，而不是简单的忽略无效的删除操作。(比如平常删除一个不存在的变量会return true;但使用严格模式后会报错。)
