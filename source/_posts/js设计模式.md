---
title: js设计模式
date: 2020-01-23 23:46:17
---
# es6下的设计模式

Quickly create a rich document.

# 单例模式

---

1. 什么是单例模式？

    单例模式是一种常见的设计模式，一个类只能有一个实例，即使多次实例化后也只会返回第一次实例化后的实例对象。减少内存开销，和全局变量的数量。

    1. 简单的单例模式

        简单的单例模式即使你对与单例模式的概念比较模糊，在日常的使用中你也会用到。

            let timeTool = {
            	name: '处理时间工具库',
            	getISODate: function() {},
            	getUTCDate: function() {}
            }

        以对象字面量创建对象的方式在JS开发中很常见。这里的timeTool就已经是一个实例。且在es6中let和const不允许重复声明，确保timeTool不会被覆盖。

    2. 惰性单例

        对象字面量创建单例只能适用于简单的场景，一旦场景复杂，创建对象本身就会复杂需要一些私有变量和私有方法。此时就需要采用构造函数的方法来实例化对象。下面就是适用立即执行函数和构造函数来改造timeTool工具库。

            let timeTool = (function() {
            		let _instance = null;
            		
            		function init() {
            		//private
            		let now = new Date();
            		//public
            		this.name = '处理时间工具库'；
            		this.getISODate = function() {
            			return now.toISOString();
            		}
            		this.getUTCDate = function() {
            			return now,toUTCString();
            		}
            	}
            	
            	return function() {
            		if(_instance) {
            			_instance = new init();
            		}
            		return _instance;
            	}
            })();

        上面的timeTool实际是一个函数，_instance作为实例对象最开始赋值为null，init函数是其构造函数，立即执行函数返回的是匿名函数用于判断实例是否创建。不在加载的时候就进行实例化而是在需要的时候进行创建。如果再次调用返回的还是第一次实例化后的对象。

            let instance1 = timeTool();
            let instance2 = timeTool();
            console.log(instance1 === instance2); //true

        es6下的export default其实也是惰性单例模式的体现

            let a = 1;
            a = a + 1;
            export default a;

2. 单例模式的应用场景
    1. 命名空间

        适用命名空间便于开发维护。减少暴露在项目中的全局变量和方法。

            //开发者A写了一大段js代码
            let devA = {
              addNumber() { }
            }
            
            //开发者B开始写js代码
            let devB = {
              add: ''
            }
            
            //A重新维护该js代码
            devA.addNumber();

    2. 管理模块

        单例模式管理代码库中的各个模块

            var devA = (function(){
              //ajax模块
              var ajax = {
                get: function(api, obj) {console.log('ajax get调用')},
                post: function(api, obj) {}
              }
            
              //dom模块
              var dom = {
                get: function() {},
                create: function() {}
              }
              
              //event模块
              var event = {
                add: function() {},
                remove: function() {}
              }
            
              return {
                ajax: ajax,
                dom: dom,
                event: event
              }
            })()

    3. es6下单例模式

            class SingletonApple {
              constructor(name, creator, products) {
                  this.name = name;
                  this.creator = creator;
                  this.products = products;
              }
              //静态方法
              static getInstance(name, creator, products) {
                if(!this.instance) {
                  this.instance = new SingletonApple(name, creator, products);
                }
                return this.instance;
              }
            }
            
            let appleCompany = SingletonApple.getInstance('苹果公司', '乔布斯', ['iPhone', 'iMac', 'iPad', 'iPod']);
            let copyApple = SingletonApple.getInstance('苹果公司', '阿辉', ['iPhone', 'iMac', 'iPad', 'iPod'])
            
            console.log(appleCompany === copyApple); //true

---