![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aa2bf51fba82481b8af5b29267b8fda3~tplv-k3u1fbpfcp-watermark.image)
> ## 重要知识点：每节博客都会重复🙉🙉🙉
> ### 口诀🤨：四基两空一对象，还有五个falsy值。
> #### 七种数据类型  
> * number string bool symbol
> * null undefined
> * object
> #### 五个 falsy 值
> * null undefined
> * 0 NaN
> * '' (空字符串)
### 今天看了方方老师的JS关于new的见解
> 大部分讲 new 的文章会从面向对象的思路讲起，但是我始终认为，在解释一个事物的时候，不应该引入另一个更复杂的事物。
> 天我从「省代码」的角度来讲 new。
## 但在我看来要从「省内存」和「省代码」这两个方向讲！
##### ------------------------------------------------------------
想象产品经要求你写正方形的程序，可以计算出正方形的周长和面积。

我们只需要这样就可以计算出一个正方形的：
```JS
      let square = {
          width: 5,
          getArea(){
              return this.width * this.width
          }
          getLength(){
              return this.width * 4
          }
      } 
```
**产品经理:这样的给我来一打**
### 这样写代码的，要么是新人，要么是傻子
```JS
              //第 一 个
      let square = {
          width: 5,
          getArea(){
              return this.width * this.width
          }
          getLength(){
              return this.width * 4
          }
      } 
              //第 二 个
      let square2 = {
          width: 6,
          getArea(){
              return this.width * this.width
          }
          getLength(){
              return this.width * 4
          }
      } 
              //第 n 个
      let square3 = { ......
      let square4 = { ......
      let square5 = { ......
```
### 简单，用for循环搞定
不就是12个对象嘛，我用for循环，代码如下:
```JS
let squareList = []
let widthList = [5,6,5,6,5,6,5,6,5,6,5,6]
for(let i = 0; i<12; i++)
{
      squareList[i] = 
    {
        width: widthList[i],
        getArea(){
        return this.width * this.width
     	 }
        getLength(){
        return this.width * 4
         }
     }
}
```
## 领导：垃圾代码，浪费内存，给我爬
### 我：**What？？？**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/213ac45df5b54e5794f08fd8f063b38f~tplv-k3u1fbpfcp-watermark.image)

### 反思
 求面积、求周长这两个动作对于每个变量其实是一样的，只需要各自引用同一个函数就可以了，没必要重复创建 100 个求面积的函数、100个求周长……
### 质疑：这有啥呀，不就是点内存么，浪费就浪费了呗，计算机还能差那点内存？
如果把情景换到我们正在制作一款类似红警的游戏，就**拿造兵来说：**
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d30303a184044782991e285fe4c794d7~tplv-k3u1fbpfcp-watermark.image)

一个大兵的在计算机里面就是一堆属性，假设兵种，攻击力，生命值是士兵这个对象的自身属性，每个兵都会一样，

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dca0e41dd49747df813aa1e7d5526bd0~tplv-k3u1fbpfcp-watermark.image)
```js
var 士兵 = {
  ID: 1, // 用于区分每个士兵
  兵种:"美国大兵",
  攻击力:5,
  生命值:42, 
  行走:function(){ /*走俩步的代码*/},
  死亡:function(){ /*嗝屁了*/    },
  攻击:function(){ /*大威天龙 大罗法咒 世尊地藏*/   },
  防御:function(){ /*护脸*/       }
}

兵营.制造(士兵)                                                          					 ———方方老师的知乎
```
但是当我们要造1000个兵的时候，用for循环1000次，就浪费了很多的内存
> * 行走、死亡、攻击、防御这四个动作对于每个士兵其实是一样的，只需要各自引用同一个函数就可以了，没必要重复创建 1000 个行走、100个死亡……
> * 这些士兵的兵种和攻击力都是一样的，没必要创建 1000 次。
> * 只有 ID 和生命值需要创建 1000 次，因为每个士兵有自己的 ID 和生命值。
### 优化
* 上节（[**原型是个啥？**](https://juejin.cn/post/6898929258245455886)）我们对原型的概念进行了一番解析，原型里面就是一大堆共有属性所在的区域。

* **把100个士兵对象的共有属性放到原型里**。那我们可以尝试把每个士兵的共同属性（行走、死亡、防御、攻击）给 **士兵**这个对象的原型，那么当我们创建士兵1，士兵2，士兵3,士兵4……的时候，每一个士兵都有一个```--prto--```,而它里面的地址#309都相同，并且都指向了共有属性，这样一来，就大大节省了空间。
![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0d086e22ed034ad2949331b3106af7a4~tplv-k3u1fbpfcp-watermark.image)
* 具体方式如下示例
```
    function 士兵(ID){
      var 临时对象 = {}

      临时对象.__proto__ = 士兵.原型

      临时对象.ID = ID
      临时对象.生命值 = 42

      return 临时对象
    }

    士兵.原型 = {
      兵种:"美国大兵",
      攻击力:5,
      行走:function(){ /*走俩步的代码*/}
      死亡:function(){ /*嗝屁了*/    }
      攻击:function(){ /*威天龙 大罗法咒 世尊地藏*/   }
      防御:function(){ /*护脸*/       }
    }

    // 保存为文件：士兵.js
```
然后就可以愉快地引用「**士**兵」来创建士兵了。
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/73ada1769e994cf7a5e81f2a927ba811~tplv-k3u1fbpfcp-watermark.image)
## JS之父的爱—— new 操作符
#### JS之父考虑到这个，就搞出来一个```new```，来看看新的程序：
```
    function 士兵(ID,生命值){
    this.ID = ID
    this.生命值 = 9999
    }
    士兵.prototype.行走 = function(){ /*走俩步的代码*/}
    士兵.prototype.死亡 = function(){ /*嗝屁了*/    }
    士兵.prototype.攻击 = function(){ /*大威天龙 大罗法咒 世尊地藏*/   }
    士兵.prototype.防御 = function(){ /*护脸*/       }

```
然后是创建士兵（加了一个```new``` ）
```
    //使用
    
    var 士兵们 = []
    for(var i=0; i<100; i++){
      士兵们.push(new 士兵(i))
    }

    兵营.批量制造(士兵们)
```
## new X()帮我们做了四件事
* 自动创建空对象
* 自动为空对象关联原型，原型地址指定为X.prototype
* 自动将空对象作为this关键字运行构造函数
* 自动return

 **new 的作用，就是省那么几行代码。（也就是所谓的语法糖）** 
### 注意 constructor 属性
方便理解，用console.dir(士兵)的属性都打出来，可以发现，行走，死亡，攻击，防御这些共同属性都放到了原型里面，

那这个constructor是啥玩意儿？

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2c70e0747b864b5297106704b74bb1c3~tplv-k3u1fbpfcp-watermark.image)

**new 操作为了记录「临时对象是由哪个函数创建的」就是告诉他爸是谁，所以预先给「士兵.prototype」加了一个 constructor 属性：**
```
士兵.prototype = {
  constructor: 士兵
}
```
## 总结
综上所述，我们从省内存，省代码的方式重新学习了一遍new,这样的根据原型举例的方式来学习——prototype这种方式，会比直接学习class深刻一些。
本文参考了知乎、CSDN、网道JavaScript 的一些知识，有描述不准确的地方还希望大多多家提醒！
> 本文参考了[方方老师的博客](https://www.zhihu.com/people/zhihusucks)
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1c2f00f4181d42479a5fa1a6bfffd356~tplv-k3u1fbpfcp-watermark.image)