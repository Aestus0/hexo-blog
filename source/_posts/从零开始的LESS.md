---
title: 从零开始的LESS
date: 2019-06-07 17:27:24
tags: css
---
![](http://pssf2j165.bkt.clouddn.com/dBaz0xhCkPY.jpg)

# 前言

## css短板

css作为一门标记性语言，对于初学者的第一印象就是简单易懂，缺少逻辑性。所以每当语法更新时，浏览器的兼容性又成为新的问题。

问题的诞生导致了新技术的兴起，产生了一批css预处理语言。使得css变为一种可以使用变量、循环、继承、自定义方法等多种特性的标记语言，逻辑性得到增强。

## 三种预处理语言

1. Sass是一款经过其核心团队超过8年精心打造的一款成熟的css预处理语言。
2. Stylus是一款富于表现力、健壮、功能丰富的css预处理器。
3. Less受Sass影响的一个开源项目，它扩充了css语言，增加了诸如变量、混合(mixin)、混合函数等功能，让CSS更易于维护、方便制作主题、扩充。

## 预处理语言的选择

就网上的讨论而言，Sass与Stylus相比Less功能更为丰富，逻辑性更强，但是对于学习成本和适应时间而言，Less更胜一筹。

Less并没有去除CSS已有的功能，而只是在现有的语法上添加了一些额外的功能。

## 引入Less

* 在页面引入 Less.js

在官网下载，或者使用CDN
`<script src="//cdnjs.cloudflare.com/ajax/libs/less.js/2.7.2/less.min.js"></script>`

**需要注意的是**，link标签一定要在Less.js之前，并且link标签的rel属性要设置为stylesheet/less。
```
 <link rel="stylesheet/less" href="style.less">
 <script src="less.min.js"></script>
```

* 使用命令行npm安装
`npm install -g less`

具体使用命令
`lessc styles.less > styles.css`

如果项目使用了webpack请参考webpack的使用手册。

# 正文

    以下为less的功能特性。
    
## 变量

### 值变量

Less中的变量是常量只能定义一次，不能重复使用。
```
/* less */
@color: #999;
@bgColor: skyblue;
@width: 50%;
#wrap {
    color: @color;
    width: @width;
}

/* 生成后的css */
#wrap {
    color: #999;
    width: 50%;
}
```

以`@`开头 定义变量，并且在使用时直接键入@名称。

平时工作中，我们就可以把常用的变量封装到一个文件中，这样利于代码文件组织维护。
```
@lightPrimaryColor: #c5cae9;
@textPrimaryColor: #fff;
@accentColor: rgb(99, 137, 185);
@primaryTextColor: #646464;
@secondaryTextColor: #000;
@dividerColor: #b6b6b6;
@borderColor: #dadada;
```

### 选择器变量

让选择器变为动态

```
/* Less */
@mySelector: #wrap;
@Wrap: wrap;
@{mySelector}{
//变量名 必须使用大括号包裹
    color: #999;
    width: 50%;
}
.@{Wrap}{
    color: #ccc;
}
#{wrap}{
    color: #666;
}

/* 生成的css */
#wrap{
    color: #999;
    width:50%
}
.wrap{
    color:#ccc;
}
#wrap{
    color:#666;
}
```

### 属性变量

可减少代码书写量

```
/* Less */
@borderStyle: border-style;
@Solid: solid;
#wrap{
    @{borderStyle}: @Solid;
}

/* 生成的CSS */
#wrap{
    border-style:solid;
}
```

### url变量

项目结构改变时，修改其变量即可。

```
/* Less */
@images:"../img";//需要加引号
body {
    background: url("@{images}/dog.png");//变量名必须使用大括号包裹
}

/* 生成的css */
body{
    background: url("../img/dog.png");
}
```

### 声明变量

有点类似于 下面的混合方法

```
- 结构：@name: {属性: 值;};
- 使用：@name();
```

```
/* Less */
@background: {background:red;};
#main{
    @background();
}
@Rules{
    width: 200px;
    height: 200px;
    border: solid 1px red;
}
#con{
    @Rules();
}

/* 生成的css */
#main{
    background:red;
}
#con{
    width:200px;
    height:200px;
    border: solid 1px red;
}
```

### 变量运算

```
- 加减法时 以第一个数据的单位为基准
- 乘除法时 注意单位一定要统一
```

```
/* Less */
@width: 300px;
@color: #222;
#wrap{
    width: @width-20；
    height:@width-20*5;
    margin:(@width-20)*5;
    color:@color*2;
    background-color:@color + #111;
}

/* 生成的css */
#wrap{
    width: 280px;
    height: 200px;
    margin: 1400px;
    color: #444;
    background-color: #333;
}
```

### 变量作用域

就近原则，先去查看自己的作用域内有没有对应变量，逐层往外查找

```
/* Less */
@var: @a;
@a: 100%;
#wrap {
    width: @var;
}

/* 生成的css */
#wrap {
    width: 9%;
}
```

### 用变量去定义变量

```
/* Less */
@fnord: "I am fnord.";
@var: "fnord";
#wrap::after {
    content: @@var;
}

/* 生成的css */
#wrap::after {
    content: "I am fnord"
}
```

### 嵌套

**&的妙用**

&:代表的上一层选择器的名字，此例便是header。
```
/* Less */
#header {
    &:after{
        content: "Less is more!";
    }
    .title {
        font-weight: bold;
    }
}

/* 生成的css */
#header::after {
    content: "Less is more!";
}
#header .title {
    font-weight: bold;
}
#header_content { //没有嵌套！
    margin:20px;
}

```

### 媒体查询

在以往的工作中，我们使用媒体查询，都要把一个元素分开写
```
#wrap {
    width: 500px;
}
@media screen and (max-width:768px) {
    #wrap {
        width:100px;
    }
}
```

Less提供了一个十分便捷的方式

```
/* Less */
#main {
    //something 
    
    @media screen {
        @media (max-width:768px){
        width:100px;    
    }
    @media tv {
        width: 2000px;
    }
}

 /* 生成的 CSS */
@media screen and (maxwidth:768px){
    #main{
        width:100px; 
    }
}
@media tv{
    #main{
        width:2000px;
    }
}
```

每个元素都会编译出自己的@media声明，并不会合并。

### 混合方法

#### 无参数方法

方法犹如 声明的集合，使用时 直接键入名称即可。

```
/* Less */
.card() {
    background: #f6f6f6;
    -webkit-box-shadow: 0 1px 2px rgba(151, 151, 151, .58);
    box-shadow: 0 1px 2px rgba(151, 151, 151, .58);
}

#wrap {
    .card();
}

/*生成的css*/
#wrap {
   background: #f6f6f6;
    -webkit-box-shadow: 0 1px 2px rgba(151, 151, 151, .58); 
    box-shadow: 0 1px 2px rgba(151, 151, 151, .58);
}
```

.card与.card()是等价的。
为避免代码混淆，应写成card()。

**要点：
  `.` 与 `#` 皆可作为 方法前缀。
  方法后写不写 `()` 看个人习惯。**

#### 默认参数方法

Less可以使用默认参数，如果没有传参，那么将使用默认参数。
@arguments犹如JS中的arguments指代全部参数。
传参必须带单位。

```
/* Less */
.border(@a:10px,@b:50px,@c:30px,@color:#000){
    border:solid 1px @color;
    box-shadow: @arguments;//指代的是 全部参数
}

#main {
    .border(0px,5px,30px,red);//必须带单位
}

#wrap {
    .border(0px);
}

#content {
    .border;
}

/* 生成的 css */
#main {
    border: solid 1px red;
    box-shadow: 0px 5px 30px red;
}

#wrap {
    border: solid 1px #000;
    box-shadow: 0px 50px 30px #000;
}

#content {
    border: solid 1px #000;
    box-shadow: 10px 50px 30px #000;
}
```

#### 方法的匹配模式

与面向对象中的多态 很相似

```
/* Less */
.trangle(top,@width:20px,@color:#000) {
    border-color: transparent transparent @color transparent;
}

.trangle(right,@width:20px,@color:#000) {
    border-color: transparent transparent @color transparent;
}

.trangle(bottom,@width:20px,@color:#000) {
    border-color: transparent transparent @color transparent;
}

.trangle(left,@width:20px,@color:#000) {
    border-color: transparent transparent @color transparent;
}

.trangle(@_,@width:20px,@color:#000) {
    border-style: solid;
    border-width: @width;
}

#main {
    .triangle(left, 50px, #999)
}

/* 生成的css */
#main {
    border-color: transparent transparent transparent #999;
    border-style: solid;
    border-width: 50px;
}
```

要点

```
- 第一参数 left 要会找到方法中匹配成都
```
