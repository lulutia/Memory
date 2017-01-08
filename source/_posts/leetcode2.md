title: leetcode-JavaScript实现part2
categories: Algorithm
date: 2015-10-12 18:09
comments: true
tags: [前端,计算机,算法]
---
其他见part1
Merge Two Sorted Lists
* https://leetcode.com/problems/merge-two-sorted-lists/
* Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.
    
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
var mergeTwoLists = function(l1, l2) {
    var resultNode = new ListNode(0);
    var result = resultNode;
    if(l1===null&&l2===null){
        return null;
    }
    while(l1!==null||l2!==null){
        if(l1===null){
            result.val = l2.val;
            l2 = l2.next;
            if(l2!==null){
            result.next = new ListNode(0);
            result = result.next;}
        }
        else if(l2===null){
            result.val = l1.val;
            l1 = l1.next;
            if(l1!==null){
            result.next = new ListNode(0);
            result = result.next;}
            
        }
        else{
            if(l1.val<=l2.val){
                result.val = l1.val;
                l1 = l1.next;
                if(l1!==null||l2!==null){
                result.next = new ListNode(0);
                result = result.next;}
                
            }
            else{
                result.val = l2.val;
                l2 = l2.next;
                if(l1!==null||l2!==null){
                result.next = new ListNode(0);
                result = result.next;}
                
            }
        }
        
    }
    return resultNode;
};

~~~

Remove Duplicates from Sorted Array
* https://leetcode.com/problems/remove-duplicates-from-sorted-array/
* Given a sorted array, remove the duplicates in place such that each element appear only once and return the new length.
    * Do not allocate extra space for another array, you must do this in place with constant memory.
    * For example,
    * Given input array nums = [1,1,2],
    * Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively. It doesn't matter what you leave beyond the new length.
* 注意返回的是length

~~~
/**
 * @param {number[]} nums
 * @return {number}
 */
var removeDuplicates = function(nums) {
    if(nums.length===0){
        return 0;
    }
    if(nums.length===1){
        return nums.length;
    }
    for(var i=1 ;i<nums.length;i++){
       if(nums[i]===nums[i-1]){
           nums.splice(i,1);
           i = i-1;
       }
    }
    return nums.length;
};
~~~

Remove Element
* https://leetcode.com/problems/remove-element/
* Given an array and a value, remove all instances of that value in place and return the new length.The order of elements can be changed. It doesn't matter what you leave beyond the new length.

~~~
/**
 * @param {number[]} nums
 * @param {number} val
 * @return {number}
 */
var removeElement = function(nums, val) {
    var count = nums.length;
    if(nums.length===0){
        return null;
    }
    for(var i=0; i<nums.length;i++){
        if(nums[i]===val){
            nums.splice(i,1);
            i = i-1;
            count = count-1;
        }
    }
    return count;
};
~~~

Implement strStr()
* https://leetcode.com/problems/implement-strstr/
* mplement strStr().Returns the index of the first occurrence of needle in haystack, or -1 if needle is not part of haystack.

~~~
/**
 * @param {string} haystack
 * @param {string} needle
 * @return {number}
 */
var strStr = function(haystack, needle) {
    if(haystack.length<needle.length){
        return -1;
    }
    if(haystack.length===0&&needle.length>0){
        return -1;
    }
    return haystack.indexOf(needle);
    
};
~~~

Valid Sudoku
* https://leetcode.com/problems/valid-sudoku/
* Determine if a Sudoku is valid, according to: [Sudoku Puzzles - The Rules](http://sudoku.com.au/TheRules.aspx).The Sudoku board could be partially filled, where empty cells are filled with the character '.'.
* Note:A valid Sudoku board (partially filled) is not necessarily solvable. Only the filled cells need to be validated.
* 简单的说确认是否是有效的数独。即满足每一行、每一列、每一个粗线宫内的数字均含1-9，不重复。

~~~
/**
 * @param {character[][]} board
 * @return {boolean}
 */
var isValidSudoku = function(board) {
    var array3 = [];
    for(var q=0;q<3;q++){
            array3[q] = [];
            for(var f=0 ; f<3;f++){
            array3[q][f] = [];}
        }
    for(var i=0;i<9;i++){
        var array = [];
        var array2 = [];
        for(var j=0;j<9;j++){
            var fir = parseInt(i/3);
            var sec = parseInt(j/3);
            if(array3[fir][sec].indexOf(board[i][j])!==-1&&board[i][j]!=="."){
                return false;
            }
            if(array.indexOf(board[i][j])!==-1&&board[i][j]!=="."){
                return false;
            }
            if(array2.indexOf(board[j][i])!==-1&&board[j][i]!=="."){
                return false;
            }
            else {
                array3[fir][sec].push(board[i][j]);
                array.push(board[i][j]);
                array2.push(board[j][i]);
            }
        }
    }
    return true;
};
~~~

Length of Last Word
* https://leetcode.com/problems/length-of-last-word/
* Given a string s consists of upper/lower-case alphabets and empty space characters ' ', return the length of last word in the string.If the last word does not exist, return 0.
    * Note: A word is defined as a character sequence consists of non-space characters only.
    * For example, Given s = "Hello World",return 5.

~~~
/**
 * @param {string} s
 * @return {number}
 */
// var lengthOfLastWord = function(s) {
//     s = s.trim();
//     if(s[s.length-1]===" "){return 0;}
//     var arr = s.split(" ");
//     return arr[arr.length-1].length;
// };
// 上面这种效率太低了
var lengthOfLastWord = function(s) {
    s = s.trim();
    if(s[s.length-1]===" "){return 0;}
    for(var i=s.length-1;i>=0;i--){
        if(s[i]===" "){
            return s.length-1-i;
        }
    }
    return s.length;
};
~~~

Plus One
* https://leetcode.com/problems/plus-one/
* Given a non-negative number represented as an array of digits, plus one to the number.The digits are stored such that the most significant digit is at the head of the list.

~~~
/**
 * @param {number[]} digits
 * @return {number[]}
 */
 //之前用字符串parseInt来做会越界
var plusOne = function(digits) {
    if(digits.length===0){
        return [1];
    }
   for(var i=0;i<digits.length;i++){
       if(digits[digits.length-i-1]+1>=10){
           digits[digits.length-i-1] = 0;
           if(digits.length-i-1-1<0){
               digits.unshift(1);
               return digits;
           }
           flag = true;
       }
       else {
           digits[digits.length-i-1]=digits[digits.length-i-1]+1;
           return digits;
       }
   }
};
~~~

Climbing Stairs
* https://leetcode.com/problems/climbing-stairs/
* You are climbing a stair case. It takes n steps to reach to the top.Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?
* 测试可知，其实是一个斐波拉契数列n为1，2，3，4，5时，结果为1，2，3，5，8.

~~~
/**
 * @param {number} n
 * @return {number}
 */
var climbStairs = function(n) {
    function step(n){
        var a=1,b=2;
        if(n===2){return b;}
        if(n===1){return a;}
        var sum;
        n -= 2;
        while(n--){
            sum = a+b;
            a = b;
            b = sum;
        }
        return sum;
    }
    return step(n);
};
~~~

Remove Duplicates from Sorted List
* https://leetcode.com/problems/remove-duplicates-from-sorted-list/
* Given a sorted linked list, delete all duplicates such that each element appear only once.
    * For example,
    * Given 1->1->2, return 1->2.
    * Given 1->1->2->3->3, return 1->2->3.
* 注意的点：处理最后一位如果为重复的情况,所以放了一个暂时变量tmp，因为链表是有序的，所以只需要对比head的下一个与result的最后一个是否一样。

~~~
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} head
 * @return {ListNode}
 */
var deleteDuplicates = function(head) {
    if(head===null){return [];}
    var outNode = new ListNode(0);
    var result = outNode;
    result.val = head.val;
    while(head.next!==null){
        var tmp = head.next;
        if(tmp.val!==result.val){
            result.next = new ListNode(0);
            result = result.next;
            result.val = tmp.val;
            head = head.next
        }
        else{
            head = head.next;
        }
    }

    return outNode;
};
~~~

Merge Sorted Array
* https://leetcode.com/problems/merge-sorted-array/
* Given two sorted integer arrays nums1 and nums2, merge nums2 into nums1 as one sorted array.
    * Note:You may assume that nums1 has enough space (size that is greater or equal to m + n) to hold additional elements from nums2. The number of elements initialized in nums1 and nums2 are m and n respectively.
* 注意的点：
    * 可能数组中有0，但是0是不计入数量的，所以最好一开始就将不符合长度的值去掉。
    * 另外需要处理nums1已经遍历完了，但是nums2还有剩的情况，直接将nums2剩下的加入nums1中，因为本来他们两个就是有序的。
    * 注意不要return。
    * splice的用法好好体会，记得是在第一个值之前插入。

~~~
/**
 * @param {number[]} nums1
 * @param {number} m
 * @param {number[]} nums2
 * @param {number} n
 * @return {void} Do not return anything, modify nums1 in-place instead.
 */
var merge = function(nums1, m, nums2, n) {
    nums1.splice(m);
    nums2.splice(n);
    var pp=0;
    for(var i=0;i<n;i++){
        for(var j=pp;j<m;j++){
            if(nums2[i]<=nums1[j]){
                if(j===0){
                    nums1.unshift(nums2[i]);
                    pp = j + 1;
                    m = m + 1;
                    break;
                }
                else{
                    nums1.splice(j,0,nums2[i]);
                    pp = j+1;
                    m = m + 1;
                    break;
                }
            }
        }
        if(j>=m){nums1.push(nums2[i])}
    }
};
~~~



    
