### script

---

* 解析器在对script标签内部的代码解析完毕之前 页面中其他内容都不会被加载和显示

* script标签外链js文件时，如果在标签内部嵌入代码，这些代码会被忽略

* script的src属性引入链接，是可以跨域的

* noscript 标签可以包含任何body中的html元素

### 搞懂script标签的defer和async属性

#### 先看普通的脚本

* 浏览器遇到普通的script标签停止解析document

* 请求js文件

* 执行js文件脚本

* 继续解析document

#### defer 延迟脚本

`<script src="d.js" defer></script>
<script src="e.js" defer></script>`

* 浏览器遇到延迟脚本，不会停止解析document，而是继续解析document并且同时下载d.js e.js

* 下载完了连个js文件，浏览器还是继续接着解析document

* 浏览器解析完doument完毕后。就是加载并显示到</html>后，按照延迟脚本在页面中出现的顺序，在其他同步脚本执行后，DOMcontentloaded事件前依次执行延迟脚本

#### async 异步脚本

`<script src="b.js" async></script>
<script src="c.js" async></script>`

* 浏览器遇到异步脚本，不会停止解析document，而是继续解析document并且同时下载两个js文件

* 下载完毕后立即执行，两个执行顺序无法确定，谁先谁后，在DOMcontentloaded事件前后都不确定

#### 理解图

![](https://camo.githubusercontent.com/3cfc9c7f3ff4185cd5c2d9d40c03e942b98c6dfd/68747470733a2f2f692e737461636b2e696d6775722e636f6d2f77664c38322e706e67)

#### 结论

* 最理想的情况是将脚本放在head中并设置成延迟脚本，这样能节省一些时间，不然当我们把脚本放在</body>前，需要在解析显示完内容后才请求js文件并解析执行，但问题是defer有一定的兼容性问题，一些老的浏览器不支持和这个属性，所以：如果项目可以保证忽略老的浏览器，优先使用延迟脚本优化，不能忽略的话，放在</body>前依然是最稳妥的方式


### 基本概念

* js单行注释和多行注释(块级注释)，多行注释的出了开头和结尾/的* 其他的* 不是必须的 有只是为了提高可读性

* js语句代码结尾应该有分号，但不是必须的。 如果没有分号则由js解析器自己推测在哪里应该插入分号，加上分号是很好的习惯，可以提高性能(解析器不用考虑在哪里加入分号)，防止代码压缩错误等

* 使用var操作符定义的变量将成为该变量所在作用域的局部变量，而在js中只有两类作用域，函数作用域和全局作用域

#### var 声明变量提升

* var变量声明总是在任意代码执行之前处理，在代码任何位置声明的变量都等同于在代码的开头声明，所有变量的声明都是会移动到函数开头或者全局开头. 提升只提升的是声明 而不是赋值


* 在全局作用域中，不使用var声明的变量其实是一个全局的属性，因为可以用delete 删除掉，但是var声明的全局变量不行

---
*2019年2月15日*

---

### 数据类型

* 五种简单数据类型 undefined null string number boolean 一种复杂类型object

* typeof 操作符的返回值有undefined object string boolean function number

* typeof 操作符对于未经声明的变量和声明未赋值的变量都是undefined 但是使用未声明的值会报错

* 显示的赋值声明的变量为undefined是一种明智的选择。想想上面的话

* 如果声明的变量在未来要保存一个对象的话 就优先赋值一个null


### Boolean 

* Boolean()转型函数 可以把js中**任何一个类型**的值转化为对应的boolean值

* if else 流程控制语句底层调用的就是强转函数Boolean()

| 数据类型 | 转为true的值 | 转为false的值 |
| ------- | ---------- | -------------|
| Boolean |  true | false |
| String | 任何非空字符串 | 空字符串 |
| Number | 任何非零数字值（包括无穷大）| 0和NAN|
| Object | 任何对象 | null | 
| Undefined | ---- | undefined |

### Number

* 八进制以0开头 在严格模式下是无效的 超出忽略前导0 按照十进制算

* 浮点树值需要的存储空间是整数的两倍。因此js中会不失时机地将浮点数转为整数值 例如小数点后面没有数字的，是0的都会转为整数

#### NaN not a number

* 任何设计NaN的计算结果都是NaN

* NaN 与任何值都不相等 包括自己

* isNaN函数尝试会尝试将参数转化为数值

*2019年2月25日*

#### Number() 转型函数

| 类型   |  值   | 结果 |
|-------| ----- | ---- |
| Number | 数字 | 数字|
| Boolean | true/ false | true => 1 false => 0 |
| Null | null | 0 |
| Undefined | undefined | NaN |
| String | string | 只包含数字(不限于拥有正负号) => 忽略前导0转为数字 空字符串 => 0 合法十六进制转为10进制 浮点数转为浮点数(忽略前导0) 其他的全为NaN|
| Object | object | 调用valueOf() 按照前面的规则转 当结果是NaN的时候再调用对象的toString()方法|

* js中一加操作符 转化原理同Number()一样

### parseInt() 

> ***只转化字符串和数字*** parseInt()会忽略参数的前面的空格直到遇到第一个非空格字符，如果第一个字符不是数字字符或者负号直接返回NaN 如果是则继续接着解析后面的字符直到遇到非数字字符


* 这个函数具有自动识别进制的能力。也就是说参数是'0xf'这样的话他就知道这是16进制并转为10进制值

* 函数接受第二个参数表示**传入的第一个参数是几进制的**

### parseFloat()

* 只能解析10进制的数 没有第二个参数

* 始终会忽略前导0 所以16进制的数始终解析为0 

* 参数是整数或者小数点后面全是0都会返回整数

### String

* js中的字符串是不可变的 一旦创建值就不能改变。要改变就得销毁原来的 重新创建一个字符串并填充该变量 这也是某些老版本浏览器拼接字符串慢的原因 但在新浏览器中已经优化
# 面向对象的程序设计
---
### *理解对象*

	 无序属性的集合，属性值可以是基本值，对象和函数。
 
	 基于引用类型创建，引用类型可以是原生类型或自定义类型。
	

 
### *属性类型*

	ECMAscript在定义只有内部(JavaScript引擎)用的特性时，
	
	描述了对象的属性的特性，特性是为了实现JavaScript引擎使
	
	用，属于内部值，因此JavaScript不能直接访问它们，且规范将它们
	
	放在两对方框号中，eg：[Enumerable]]，以示区别。
	
	
	
### 数据属性 
 	 
 > 包含一个数据值的位置，此位置可以读取和写入值，有四个特性。
 

 * **_[[Configurable]]_** 默认值为true，表述能否通`delete`
 删除该属性并重新定义，能否修改属性的特性，能否转为访问期熟悉。
 
 * **_[[Enumerable]]_** 默认值为true，表示能否通过`for-in`循环返回该属性。

 * **_[[Writable]]_** 默认值为true，表述能否修改该属性的值。
 
 * **_[[Value]]_** 默认值为undefined，包含该属性的值，读写均在这个位置。
 
 **_注意:_**

 	1. 我们平时定义的对象属性默认为数据属性，都拥有上面四种内部特性。

   1. 要想修改属性默认的特性，**_必须_** 使用ECMAscript5 的`Object.defineProperty()`(IE8支持不完全，IE8以下不支持)，该方法接受三个参数：属性所在对象，属性名，描述符对象。
	
	1. 如果调用`Object.defineProperty()`却不指定属性的四个特性的值，那么它们此时的它们的默认值都成为了`false`(**要知道调用这个方法之前的时候它们的默认值可都是true**)。

 
### 访问器属性

> 访问器属性不包含值，但是包含一对儿`getter`和`setter`函数(两对函数非必需)，读取访问器属性时会调用`getter`函数返回有效值，写入访问器属性时会调用`setter`函数并传入新值，这个函数负责如何**_处理(不是写入)_** 传进来的数据。


* **_[[Configurable]]_** 默认值为true，表示能否通过`delete`删除属性，能否修改属性特性，能否转为数据属性。

* **_[[Enumerable]]_** 默认值为true，表述能否通过`for-in`循环返回该属性。

* **_[[Get]]_** 默认值为undefined，读取属性时调用的函数。

* **_[[Set]]_** 默认值为undefined，写入属性时调用的函数。
 
 **_注意:_**

	 1. 访问器属性不能也无法直接定义(**记得吗？我们平时给一个对象定义的属性它默认就是数据属性，是可以直接定义的**)，必须通过调用`Object.defineProperty()`这个方法来定义。

	2. 调用`Object.defineProperty()`这个方法不一定得同时`getter`和`setter`函数，**但是**如果只指定`getter`函数意味着该属性不能写，只指定`setter`函数意味着不能读。

### 方法

 1. `Object.defineProperties()`为对象定义多个属性

		
	> **_作用:_** 定义多个属性
		
	> **_参数:_** 两个对象参数，第一个对象为要添加和修改属性的对象，第二个对象是描述符对象。

 2. `Object.getOwnPropertyDescriptor()`

	> **_作用:_** 获取属性的描述符
		
	> **_参数:_** 属性所在对象和要获取描述符的属性名称
		
	> **_返回值:_** 一个对象
	

### 原型对象

   1. 我们创建的每个**_函数都有_**一个`prototype`属性，这个属
   性是一个[指针](https://baike.baidu.com/item/	%E6%8C%87%E9%92%88/2878304?fr=aladdin)，
   指向一个对象(**__原型对象__**)，而这个对象的用途就是
   包含由定类型的所有实例**_共享_**的属性和方法。
   
   
   2. 默认情况下，所有**__原型对象__**都会自动获得一个
  `constructor`构造函数属性，这个属性包含一个指向`prototype`属性所在函数的指针

  3. 创建了自定义的构造函数之后，其原型对象默认只会取得`constructor`构造函数属性，至于其他方法都是从Object继承而来。
 
 4. 当调用构造函数创建一个新实例后，该实例会包含一个内部的指针属性，`[[prototype]]`指向构造函数的原型对象。`[[prototype]]`属性是内部值，脚本没有标准的方式访问它，但Firefox，Safari和Chrome在每个对象上都支持一个属性叫`_proto_`，其他实现中对这个脚本都是不可见的。

### 原型方法

   1. `isPrototypeOf`
   
    > 确定实例和原型对象之间的关系，返回Boolean类型值，
    

   2.  `Object.getPrototypeOf`
   
    > 返回实例的原型对象。
    
### 属性查找

	属性查找过程，每当读取某个对象的属性时，都会执行一次搜索，目标
	
	是具有给定名字的属性，搜索首先从对象实例本身开时，如果在实力中
	
	找到了属性则返回，如果没有找到则继续在原型对象中查找，原型中找
	
	到了就返回，如果没有找到就会在Object构造函数的原型中找，在
	
	Object原型中还没有找到会报错。

### 继承

* **_原型链_** 

		利用原型让一个引用类型继承于另一个引用类型的属性和方法。
		
* **_本质_**

   1. 通过原型链本质上是扩展了前面介绍的原型搜索机制

   
   2. 原型链的末端是Object，所有的引用类型默认都继承了Object

* **_注意_**

 	1. 使用原型链实现继承时，不能实现对象字面量添加原型方法，这样会切断继承链。
 	
   2. 原型链的问题，如果原型中有包含引用类型值的属性，那么所有任何一个实例属性的改变都会影响到其他实例。
