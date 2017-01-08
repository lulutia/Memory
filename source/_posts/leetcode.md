title: leetcode-JavaScript实现
categories: Algorithm 
tags: [前端,计算机,算法]
---
找工作找的心伤，碰上今年这个动荡的互联网就业年份，刷题安抚下情绪＝＝,然后发现n久之前刷过的几道都过期了，看不到当时的解决了……算了，这次还是存个档吧。
##Add Two Numbers
* https://leetcode.com/problems/add-two-numbers/
* You are given two linked lists representing two non-negative numbers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.
    * Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
    * Output: 7 -> 0 -> 8
    
~~~
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */
var addTwoNumbers = function(l1, l2) {
    var resultNode = new ListNode(0);
    var result = resultNode;
    var sum = 0;
    if(l1===null&&l2===null){
        return null;
    }
    while(l1!==null||l2!==null){
        if(l1!==null){
            sum = sum+l1.val;
            l1 = l1.next;
        }
        if(l2!==null){
            sum = sum+l2.val;
            l2 = l2.next;
        }
        if(sum>9){
            result.val = sum-10;
            result.next = new ListNode(1);
            result = result.next;
            sum = 1;
        }
        else{
            result.val = sum;
            if(l1!==null||l2!==null){
            result.next = new ListNode(0);
            result = result.next;
            sum = 0;
            }
        }
    }
    return resultNode;
};

~~~

##Longest Substring Without Repeating Characters：

* https://leetcode.com/problems/longest-substring-without-repeating-characters/
* Given a string, find the length of the longest substring without repeating characters. For example, the longest substring without repeating letters for "abcabcbb" is "abc", which the length is 3. For "bbbbb" the longest substring is "b", with the length of 1.

~~~
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function(s) {
    var item = "";
    var num=0;
    for(var i=0;i<s.length;i++){
        if(item.indexOf(s[i])===-1){
        item = item + s[i];
        var len = item.length;
            if (len>num){
                num = len;
            }
        }
        else{
            var index = item.indexOf(s[i]);
            item = item.substr(index+1);
            item = item+s[i];
        }
    }
    return num;
};
~~~

##ZigZag Conversion
* https://leetcode.com/problems/zigzag-conversion/
* 简单的说就是从左边开始不停画Z字型路线，然后从上部读取字母拼成输出。
* 后来网上找了下，[这个孩子](http://fisherlei.blogspot.com/2013/01/leetcode-zigzag-conversion.html)的分析思路和我的基本一样。

~~~
/**
 * @param {string} s
 * @param {number} numRows
 * @return {string}
 */
var convert = function(s, numRows) {
    if(s===""||numRows<1||s===null){return ""}
    if(numRows===1){return s}
    if(s.length<=numRows){return s}
    var step = 2*numRows - 2;
    var output="";
    for(var i=0; i<numRows; i++ ){
        for(var j=i; j<s.length; j=j+step){
            output += s[j];
            if(i!==0&&i!==(numRows-1)){
                var minStep = step - i*2;
                if((j+minStep)<s.length){
                output += s[j+minStep];}
            }
        }
    }
    return output;
};
~~~

##Reverse Integer
* https://leetcode.com/problems/reverse-integer/
* Reverse digits of an integer.
    * Example1: x = 123, return 321
    * Example2: x = -123, return -321

~~~
/**
 * @param {number} x
 * @return {number}
 */
var reverse = function(x) {
    var flag = false
    if(x<0){
        flag = true;
    }
    x = Math.abs(x);
    var str = x+ "";
    var out = ""
    for(var i=0;i<str.length;i++){
        out += str[str.length-i-1];
    }
    out = parseInt(out);
    if(out > Number.parseInt("1111111111111111111111111111111", 2)){return 0}
    if(flag){
        out = -out;
    }
    return out;
};
~~~

##String to Integer (atoi)
* https://leetcode.com/problems/string-to-integer-atoi/
* Implement atoi to convert a string to an integer.
    * Hint: Carefully consider all possible input cases. If you want a challenge, please do not see below and ask yourself what are the possible input cases.
    * Notes: It is intended for this problem to be specified vaguely (ie, no given input specs). You are responsible to gather all the input requirements up front.

~~~
/**
 * @param {string} str
 * @return {number}
 */
var myAtoi = function(str) {
    if(str.length === 0){return 0;}
    var count = 0;
    var flag = false;
    str = str.trim();
    if(str[0]==="+"||str[0]==="-"){
        flag = true;
        if(isNaN(parseInt(str[1]))){
            return 0;
        }
    }
    if(flag){
        for(var i=1 ; i<str.length;i++){
             if(isNaN(parseInt(str[i]))){
                str = str.substr(0,i);
        }
        }
    }
    else{
         for(var j=0 ; j<str.length;j++){
             if(isNaN(parseInt(str[j]))&&j!==0){
                str = str.substr(0,j);
        }
        else if(isNaN(parseInt(str[j]))&&j===0){
            return 0;
        }
        }
    }
    if(parseInt(str)<-2147483648) {return -2147483648;}
    if(parseInt(str)> 2147483647) {return 2147483647;}
    return parseInt(str);
};
~~~

##Palindrome Number
* https://leetcode.com/problems/palindrome-number/
* Determine whether an integer is a palindrome. Do this without extra space.

~~~
/**
 * @param {number} x
 * @return {boolean}
 */
var isPalindrome = function(x) {
    if(x<0){
        return false;
    }
    var str = x + "";
    if(str.length===1){
        return true;
    }
    if(str.length%2===0){
        for(var i=0; i<(str.length/2); i++){
            if(str[i]!==str[str.length-1-i]){
                return false;
            }
        }
    }
    else{
        for(var j=0; j<((str.length+1)/2)-1;j++){
            if(str[j]!==str[str.length-j-1]){
                return false;
            }
        }
    }
    return true;
};
~~~

##Roman to Integer
* https://leetcode.com/problems/roman-to-integer/
* Given a roman numeral, convert it to an integer.Input is guaranteed to be within the range from 1 to 3999.
* 题目没写具体的罗马数规律，查了一下，如下：[罗马数规律](http://blog.csdn.net/wzy_1988/article/details/17057929)

~~~
/**
 * @param {string} s
 * @return {number}
 */
var romanToInt = function(s) {
    var num = [];
    for(var i=0; i<s.length; i++){
        switch(s[i]){
            case "I":
                num.push(1);
                break;
            case "V":
                num.push(5);
                break;
            case "X":
               num.push(10);
                break;
            case "L":
                num.push(50);
                break;
            case "C":
               num.push(100);
                break;
            case "D":
                num.push(500);
                break;
            case "M":
                num.push(1000);
                break;
        }
        
    }
    if(num.length===0){return 0;}
    if(num.length===1){return num[0];}
    var sum = num[0];
    for(var j=1 ;j<num.length;j++){
        if(num[j]<=num[j-1]){
            sum += num[j];
        }
        else{
            sum = sum - 2*num[j-1] + num[j];
        }
    }
    return sum;
};
~~~

##Longest Common Prefix
* https://leetcode.com/problems/longest-common-prefix/
* Write a function to find the longest common prefix string amongst an array of strings.

~~~
/**
 * @param {string[]} strs
 * @return {string}
 */
var longestCommonPrefix = function(strs) {
    if(strs.length===0){return "";}
    var short=strs[0].length;
    for(var i=0;i<strs.length;i++){
        if(strs[i].length<short){
            short = strs[i].length;
        }
    }
    var pre = "";
    for(var j=0;j<short;j++){
        for(var k=1;k<strs.length;k++){
            if(strs[k][j]!==strs[k-1][j]){
                return pre;
            }
        }
        pre = pre + strs[0][j];
    }
    return pre;
};
~~~




    
