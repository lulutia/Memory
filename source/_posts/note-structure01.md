title: (数据结构与算法JavaScript描述)读书笔记
categories: technology
date: 2015-10-13 16:06
comments: true
tags: [前端,计算机,算法]
---
数组
* 判断是否数组：Array.isArray(sth);
* []创建数组的效率比Array构造函数高;
* 浅复制 VS 深复制；
* 字符串生成数组:split();数组的字符串表示:join()/toString()
* sort()是按照字典顺序对元素进行排序的，因此它假定元素都是字符串类型的，按照数字大小进行排序的一般如下：

~~~
function compare(num1,num2){
    return num1 - num2;
}
var nums = [3,2,5,7,88];
nums.sort(compare);
~~~

* 迭代器方法: forEach();every();some();reduce()--累加；reduceRight();map();filter()--返回一个新数组，该数组包含应用该函数后结果为true的**元素**;
* 创建多维数组的一般办法

~~~
function matrix(numrows,numcols,initial){
    var arr = [];
    for(var i = 0; i < numrows; i++){
        var columns = [];
        for(var j = 0; j < numcols; j++){
            columns[j] = initial;
        }
        arr[i] = columns;
    }
    return arr;
}
~~~

列表
* 如果数据结构非常复杂。列表的作用就没有那么大了。
* 如果数据储存的顺序不重要，也不必对数据进行查找，那么列表就很好。

~~~
function List(){
    this.listSize = 0;//列表长度
    this.pos = 0;//列表的当前位置
    this.dataStore = [];//初始化一个空数组来保存列表元素
    this.clear = clear;
    this.find = find;
    this.toString = toString;
    this.insert = insert;
    this.append = append;
    this.remove = remove;
    this.front = front;
    this.end = end;
    this.prev = prev;
    this.next = next;
    this.length = length;
    this.currPos = currPos;
    this.moveTo = moveTo;
    this.getElement = getElement;
    this.contains = contains;
}
function append(element){
    this.dataStore[this.listSize++] = element;
}
function find(element){
    for(var i=0;i < this.dataStore.lenhgth;i++){
        if(this.dataStore[i] == element){
            return i;
        }
    }
    return -1;
}
function remove(element){
    var foundAt = this.find(element);
    if(foundAt > -1){
        this.dataStore.splice(found,1);
        --this.listSize;
        return true;
    }
    return false;
}
function length(){
    return this.listSize;
}
function toString(){
    return this.dataStore;
}
function insert(element, after){
    var insertPos = this.find(after);
    if(insertPos > -1){
        this.dataStore.splice(insertPos+1, 0, element);
        ++this.listSize;
        return true;
    }
    return false;
}
function clear(){
    delete this.dataStore;//关于delete的用法重启一章描述
    this.dataStore = [];
    this.listSize = this.pos = 0;
}
function contains(element){
    for(var i=0; i<this.dataStore.length;i++){
        if(this.dataStore[i] == element){
            return true;
        }
    }
    return false;
}
function front(){
    this.pos = 0;
}
function end(){
    this.pos = this.listSize - 1;
}
function prev(){
    if(this.pos > 0){
        --this.pos;
    }
}
function next(){
    if(this.pos<this.listSize-1){
        ++this.pos;
    }
}
function currPos(){
    return this.pos;
}
function moveTo(position){
    this.pos = position;
}
function getElement(){
    return this.dataStore[this.pos];
}
迭代器：
var names = [2,4,5,5];
for(names.front();names.currPos()<names.length();names.next()){
    console.log(names.getElement());
}
~~~

栈
~~~
function Stack(){
    this.dataStore = [];
    this.top = 0;
    this.push = push;
    this.pop = pop;
    this.peek = peek;
}
function push(element){
    this.dataStore[this.top++] = element;
}
function pop(){
    return this.dataStore[--this.top];
}
function peek(){
    return this.dataStore[this.top-1];
}
function length(){
    return this.top;
}
function clear(){
    delete this.dataStore;//关于delete的用法重启一章描述
    this.dataStore = [];
    this.top = 0;
}
~~~

* 将数字转化为二至九进制的数字

~~~
function mulBase(num, base){
    var s = new Stack();
    do{
        s.push(num%base);
        num = Math.floor(num/=base);
    }while(num>0);
    var converted = "";
    while(s.length()>0){
        converter += s.pop();
    }
    return converted;
}
~~~

* 回文判断

~~~
function isPalindrome(word){
    var s = new Stack();
    for(var i=0; i<word.length; i++){
        s.push(word[i]);
    }
    var rword = "";
    while(s.lenght()>0){
        rword += s.pop();
    }
    if(word == rword){
        return true;
    }
    else{
        return false;
    }
}
~~~
