---
title: Array的迭代
date: 2019-06-07 17:51:34
tags:
---
![](http://ww1.sinaimg.cn/mw690/e62e9d3cgy1g3sqwqbafmj22zj2a34qy.jpg)
1. every and some 

    every()是对数组的每一项运行指定函数，如果该函数对每一项都返回true，则返回true
    
    ```javascript
    var a =[1,1,1,1];
    
    
    a.every((val)=>(val===1?true:false))
    //true
    ```    
    
    some()是对数组中的每一项都运行指定函数，如果该函数对任一项返回true，则返回true。
    
    ```javascript
       a.push(2)
       //5
       a.every((val)=>(val===1?true:false))
       //false
       a.some((val)=>(val===1?true:false))
       //true
    ```

2. filter
    
    filter()是对数组中的每一项运行给定函数，返回该函数会返回true的项组成的数组。它利用指定的函数确定是否在返回的函数中包含某一项。
    
    ```javascript
     a.filter((val)=>(val===1?true:false))
     //(4) [1, 1, 1, 1]
    ```
3. map()

    map()是数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组。
    
    ```javascript
    a.map((val)=>(val===1?true:false))
    //(5) [true, true, true, true, false]
    ```
    
4. forEach()
    
    forEach()是多数组中的每一项运行给定函数，这个方法没有返回值。只是对每一项运行传入的函数.
    
    ```javascript
     a.forEach((val)=>(val===1?true:false))
     //undefined
    ```