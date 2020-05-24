---
title: js变量
date: 2020-03-21 15:41:03
tags: javascript
---
# javascript变量

## 变量类型

### 6种原始值：

    1. boolean
    2. nunber
    3. string
    4. undefined
    5. symbol
    6. null
    
### 引用类型

    1. 对象
    2. 数组
    3. 函数
    
### typeof 所能得到的结果

    1. boolean
    2. number
    3. string
    4. undefined
    5. symbol
    6. object
    7. function
    8. bigint
    
### instanceof 判断原理

判断一个对象与构造函数是否存在一个原型链上
    
  ```ecmascript 6
        const Person = function() {}
        const p1 = new Person()
        p1 instanceof Person // true
        
        var str = 'hello world'
        str instanceof String // false
        
        var str1 = new String('hello world')
        str1 instanceof String // true
```
### 实现一个类型判断函数

1. 判断null
2. 判断基础类型
3. 使用Object.prototype.toString.call(target)来判断引用类型

```ecmascript 6
function getType(target) {
  // null
  if (target === null) {
    return 'null';
  }
  // 基础类型
  cont typeOfT = typeof target;
  if (typeOfT !== 'object') {
    return typeOfT; 
  }
  const typeStr = Object.prototype.toString.call(target);
  return typeStr;
}
```

### 转Boolean

以下都为假值，其他所有值都转为true

* false
* undefined
* null
* ''
* NaN
* 0
* -0

### 对象转基本类型

对象在转换基本类型时，会调用`valueOf`,需要转换成字符类型时调用`toString`

```ecmascript 6
const a = {
  valueOf () {
    return 0;
  },
  toString() {
    return '1';
  }
}

1 + a; //1
'1'.concat(a); // "11"

```
也可以重写 Symbol.toPrimitive ，该方法在转基本类型时调用优先级最高。 Symbol.toPrimitive 指将被调用的指定函数值的属性转换为相对应的原始值

### 类型转换

运算中其中一方为字符串，那么就会把另一方也转换成字符串 如果一方不是字符串或者数字， 那么将它转换为数字或者字符串

```ecmascript 6
    1 + '1' //'11'
    true + true //2
    4 + [1,2,3] //"41,2,3"
```
还需要注意这个表达式`'a' + + 'b'`

```ecmascript 6
  'a' + + 'b' // -> "aNaN"
```
因为 + 'b' 等于NaN, 等于NaN,所以结果为"aNaN"

[JS类型转换规则总结](https://blog.csdn.net/qq_37746973/article/details/82491282)

### `100 + `问题

```ecmascript 6

'100' + 100 // "100100"
100 + '100' // "100100"
100 + true // 101
100 + false //100
100 + undefined // NaN
100 + null // 100

```
### "a common string" 为什么会有length属性

通过字面量的方式创建：var a = 'string';.这时是基本类型值；通过构造函数创建: var a = new String('string');这时它是对象类型。

基本类型是没有属性和方法的，但仍然可以使用对象才有的属性方法。这时因为在对基本类型使用属性方法的时候，后台会隐式的创建这个基本类型的对象，之后再销毁。

### == 操作符

对于 == 来说，如果双方类型不一致，就会进行强制转换

判断流程：

1. 首先判断类型是否相同。相同就比大小
2. 类型不同，进行类型转换
3. 先会判断是否在比较null和undefined，是的话返回true
4. 判断两者类型是否为string和number，是的话会将字符串转换为number
    
   ```ecmascript 6
    1 == '1'
          ↓
    1 ==  1
   ```
   
5. 判断其中一方是否是boolean,是的话就会把boolean转为number在进行判断

    ```ecmascript 6
    '1' == true
            ↓
    '1' ==  1
            ↓
     1  ==  1
   ```
6. 判断其中一方是否为object且另一方为string、number或symbol，把object转为原始类型在进行判断
    
    ```ecmascript 6
       '1' == { a: 'b' }
               ↓
       '1' == '[object Object]'
   ```
7. 两边都是对象的话，那么只要不是同一对象的不同饮用，都为false

ps.只要是NaN，就一定false，因为连NaN != NaN

### ===操作符

不转类型，直接判断类型和值是否相同。但是NaN === NaN还是false

