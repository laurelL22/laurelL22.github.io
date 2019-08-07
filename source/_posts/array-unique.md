---
title: array_unique
date: 2017-01-25 15:56:46
tags: javascript
categories:
    - cate
---

### 数组去重就是将一个数组中的相同元素删除，只保留其中的一个

1. Set实现

   ```js
   Array.prototype.unique = function() {
       return Array.from(new Set(this))
   }
   ```

2. 结合{}实现

   ```js
   // （判断是否相同的时候，不要忽略对元素类型的判断）
   Array.prototype.unique = function() {
       var json = [];
       var result = [];
       this.forEach(function(value){
           var type = Object.prototype.toString.call(value).match(/\s(\w+)/)[1].toLowerCase();
           if(!((type + '-'+value) in json)){
               json[type + '-'+value] = true;
               result.push(value);
           }
       })
       return result;
   }
   ```

3. 利用Array.prototypr.filter实现

   ```js
   Array.prototype.unique = function() {
       var sortArr = this.sort();
       return sortArr.filter(function(v, i, context){
           return v !== context[i+1];
       })
   }
   // filter()方法使用指定的函数测试所有元素，并创建所有通过测试的元素的新数组
   ```

4. 利用Array.prototype.forEach实现

   ```js
   Array.prototype.unique = function() {
       var result = [];
       this.forEach(function(v){
           if(!result.includes(v)) {
               result.push(v);
           }
       })
       return result;
   }
   ```

5. 利用Array.prototype.splice()实现

   ```js
   // 关键点就是在splice一个元素之后,i要自减1。
   Array.prototype.unique = function() {
       var sortArr = this.sort(), i = 0;
       for(; i < sortArr.length; i++) {
           if(sortArr[i] === sortArr[i+1]) {
               sortArr.splice(i, 1);
               i--;
           }
       }
       return sortArr
   }
   ```

6. 利用Array.prototype.reduce()实现

   ```js
   // reduce() 方法对累加器和数组的每个值应用一个函数 (从左到右)，以将其减少为单个值。
   Array.prototype.unique = function() {
     var sortArr = this.sort(), result = [];
     sortArr.reduce((v1,v2) => {
       if(v1 !== v2){
         result.push(v1);
       }
       return v2;
     })
     result.push(sortArr[sortArr.length - 1]);
     return result;
   }
   ```