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

#### 转为字符串
1. 调用**几乎每个值(null和undefined)没有**都有的toString()方法
2. toSting()方法可以传入一个参数表示输出是数值字符串的时候的基础(进制) 默认值10
3. String() 如果要转化的值有toString()方法调用这个方法 没有null => "null" undefined => "undefined"
4. 使用一加操作符把要转化的值和一个空字符串相加

#### 一元操作符

* 前置递增递减是在包含语句求值之前计算的 后置递增递减是在包含语句求值之后计算的

* ***记住*** 一元 ++ -- 不仅使用于数字。其实适用于js中的任何数据类型 当不是数字类型的时候先通过调用转型函数Number()转成数字。再操作

* 一元 + 放在数字前面没有影响。放在非数字类型前相当于调用Number() 一元 - 同一元 + 一样放在非数字前面


#### 位运算符

* 有符号的整数，从右到左，前31位表示数值，最后一位表述符号，0代表正数，1代表负数，没有用到的位用0填充

* 负数以补码形式表示

* 默认所有的整数都是有符号整数，但也有无符号整数，无符号整数更大。因为多出一位表述数值

* 应用位操作时 NaN和Infinity被当作0 其余非0值会先被调用Number()转型函数

* ～按位非 返回反码

* &按位与 两个对应位都是1时返回1

* | 按位或 两个对应位都是0时返回0

* ^ 按位异或 对应位只有一个1时返回1

* << 向左移动指定的位数 移动后右侧空出来的位以0填补 不会影响符号位

* '>>' 有符号的右移 保留符号位，向右移动指定位数 移动后左侧空的位置以**符号位的值**填充

* '>>>' 无符号右移 移动32位 移动后以0填充空位 对于正数 无符号右移结果与有符号右移动结果一样，但是对于负数，因为负数是以二进制补码形式表述，并且移动时，会将负数的二进制码当成正数的二进制码，负数在无符号右移后变得非常大


### 布尔操作值

#### 逻辑非 ！

> 逻辑非的工作原理是： 调用Boolean()转型函数 再取反 也就是说同时使用两个逻辑非的作用就是相当于Boolean()


#### 逻辑与 &&

> 逻辑与只有在两个操作数都是true的时候才会返回true 逻辑与可以操作任何类型的值。不仅仅是Boolean值  ***在有一个操作值不是Boolean值的情况下返回的就不一定是Boolean值了***

* ***总结起来：逻辑与遇到非Boolean值先调用Boolean转型函数转成Boolean值，然后如果第一个值是true则永远返回第二个值，否则就返回第一个值***


#### 逻辑或 ||

> 只有两个值都是false才会返回false 

* ***总结起来：逻辑与遇到非Boolean值先调用Boolean转型函数转成Boolean值，然后如果第一个值是false则永远返回第二个值，否则就返回第一个值***

逻辑与和逻辑或都属于短路操作

#### 乘性操作符

* Infinity * 0 = Infinity

* Infinity / Infinity = NaN     0 / 0 = NaN

* ‘+’ 如果有一个值是字符串则结果是字符串  +0 + -0 = +0


#### 关系操作符

`> < >= <=`

* 如果都是数值则执行数值比较

* 如果都是字符串 比较两个字符串对应位置的每个字符的编码值(大写编码值全小于小写编码值) ***是按照第一个字母比较的***

* 如果有一个是数值。则将另一个转化为数值

* 如果是boolean值转为数值

* 如果是对象 调用valueOf. toString

* 任何操作数与NaN比较结果都是false `'a' < 3 => false  NaN < 3 NaN >= 3 都是false`


#### 相等操作符

##### == != 相等和不相等

* 先转化类型 再比较类型

* 如果有个值是Boolean值。则将其转为0 || 1

* 一个数值。一个字符串。将字符串转为数值

* 如果一个对象 一个不是对象 则调用对象的valueOf方法

* null 和 undefined 相等

* 如果有一个NaN 则返回false

* 如果两个值都是对象。则他们指向同一个对象。相等。 

##### === !== 全等和不全等

* 类型不同直接返回false


#### 条件操作符

* var variable = isTrue ? true-result : false-result

#### 逗号操作符

* 可以在一条语句中执行多个操作

* 逗号操作符可以用于**赋值** 此时总是返回操作符的最后一项 var name = (1,3,4,5,6)   name => 6


### 语句

#### if else

* if(condition) condition可以是任何值任何表达式 如果表达式的值不是Boolean值的话 js会自动调用Boolean()转型函数将其转为一个boolean值

* do while 语句至少执行一次循环体 

* for循环和while循环的能力一样，但是区别是for循环***具有执行循环前定义初始化变量和定义之行循环后要之行的代码的能力*** **记住定义初始化变量只有开始的一次  亲自试的**

* for in 循环遍历对象可枚举的属性

* switch 语句 case可以连续判断两种几种情况 比如这样： `switch(condition) case 1: case 2: case 3: expression` ***case的情况不一定是常量 可以是变量甚至表达式 ***

### 函数

* 函数的参数不在乎个数，类型

* 函数的参数实际上在内部是一个arguments这个类数组来定义的

* 函数没有重载的概念 因为其参数是由包含0个或多个值的数组来表示的而不是函数签名（接受的参数的类型，和数量）

## 第四章

### 变量

* 只能给引用类型的值动态的添加属性

* 复制基本类型值的时候 会在当前的变量对象上创建一个新位置，然后把放到这个新的位置上 此后这两个值的任何操作互不影响

* 当从一个变量向另一个变量赋值引用类型的值时，同样也会将存储在变量对象中的值复制一份放到为新变量分配的空间中，**不同的是这个值的副本实际上是一个指针，而这个指针指向存储在堆中的一个对象** 复制结束后，两个变量保存的指针指向同一个对象

* js中的参数传递是按值传递的，意思就是说：把函数外部的值传递给函数内部（函数的一个布局变量，arguments类数组的一个元素）就相当于把一个值复制到另一个变量中，基本类型的传递相当于基本类型的复制，引用类型就相当于引用类型的复制一样，在传递基本类型时就是在传递值，传递引用类型时传递的是指针(就是这个值在内存中保存的地址)

* 参数实际上是函数的布局变量


### 类型检测

* typeof 操作符是检测string，number，boolean，undefined的最佳工具

* typeof操作符检测对象和null时都返回‘object’

* typeof 函数返回值是‘function’ 实际上typeof操作符会对所有内部实现了[[Call]] 方法的对象都返回‘function’

* instanceof操作符 如果变量是给定引用类型的实例 那么就会返回true intanceof操作符的原理是根据原型链实现的


### 执行环境及作用域

* 执行环境定义了变量和函数有权访问的其他数据，决定了他们各自的行为

* 每一个执行环境都有一个与之关联的变量对象，环境中定义的变量和函数都保存在这个对象中

* 根据我的理解 ***执行环境其实就是一个对象 他就是用一个对象来表示的， 例如在web中最外围的全局执行环境就是用window对象表示的***

* 每一个执行环境其实就是就是一个对象，而且这个执行环境与之关联的变量对象也是一个对象

* 每一个执行环境中的代码执行完了之后，该环境就会被销毁，定义在环境中的变量和函数也会同时销毁

* 每一个函数都会有一个执行环境，当执行流进入一个函数中，这个函数的执行环境就会被推入一个环境栈中，函数执行完后执行环境就会被弹出

* ***当代码在一个函数中执行时，会创建变量对象的一个作用链，作用域链的作用就是保证对执行环境有权访问的变量和函数的有序访问 作用域链的前端是当前执行环境的变量对象，最外层是全局执行环境的变量对象***


* 每一个函数都会有一个执行环境，当执行流进入一个函数中，这个函数的执行环境就会被推入一个环境栈中，函数执行完后执行环境就会被弹出

* ***当代码在一个函数中执行时，会创建当前变量对象的一个作用域链，他的用途就是保证执行环境所有有权访问的变量和函数的有序访问***

* 访问一个变量的时候，最先在自己的变量对象中查找，如果没有找到，就开始沿着作用域链向上查找，一直查到全局执行环境的变量对象上(浏览器中就是window), 如果最终没有找到就是undefined

* 函数的参数也是一个变量，就是函数的当前的局部变量，

### 延长作用域链

* with 语句可以将指定的对象添加到作用域链的前端

* try catch 语句中catch块则是创建一个新的变量对象 其中包含的是错误对象的声明添加到作用域链中

### 声明变量

* 使用var关键字声明的变量会被自动添加到最近的执行环境中的变量对象中，如果没有使用var关键字，则一律添加到全局执行环境的变量对象中(全局执行环境在浏览器中就是window)

* **建议使用变量之前一定要先声明，这是良好的习惯同时在严格模式下这种行为会报错**

### 查询标示符

* 如果在某个环境中读取或者写入一个变量的时候，就会通过搜索的方式来确定这个标识符到底代表着什么， 过程从作用域链的前端开始，向上逐级查询，如果在某一级找到则停止搜索，如果知道全局执行环境的变量对象上还没有找到的话就会是undefined

* 变量查询的过程并不是没有代价，所以尽量应该把变量声明在最近的执行环境中，这样查询更快，不过这个过程已经被引擎优化了，其中的性能损耗可以忽略不计，

### 垃圾回收

* JavaScript中执行环境会负责管理代码执行过程中使用到的内存，包括内存的分配和收回

* 局部变量的生命周期： 局部变量只在函数执行的过程中存在，在这个过程中开始为变量分配内存空间，然后在函数中使用，函数执行结束时 就要收回内存空间了，在收回的过程中，会判断这个变量还有存在的必要吗， 如果没有则会打上标记，等待下一周垃圾回收周期到来的时候收回，

* 而对于打标记，根据浏览器的实现不同，打标记的方式也有不同，主流的有两种

1. 标记清除
2. 引用计数

#### 标记清除

* 垃圾收集器会在运行的时候给存储在内存中的所有变量都加上标记，然后它会去掉环境中的变量以及被环境中引用的变量，而在此之后在此被加上标记的变量将被认为是准备删除的变量， 

* 大多数浏览器都用的是标记清除方式的垃圾收集策略，只不过收集的时间间隔不一样

#### 引用计数

* 引用计数的含义就是跟踪记录每个值被引用的次数，如果引用的次数变成0则说明不能在访问，已经可以准备收回了

* 引用计数会导致一个循环引用的问题，这将会导致变量值占用的内存永远不能释放

```
function problem() {
   var objA = new Object()
   var objB = new Object()
   
   objA.someObj = objA
   objB.anotherObj = objB
}
```

#### 性能问题

* 垃圾收集器是周期运行的 

* 确定垃圾收集器运行的时间间隔是非常重要的，IE浏览器是根据内存分配量运行的，具体就是256个变量，4096个对象或者数组，或者64kb的字符串，达到上述任何一个阀值就是触发垃圾收集器运行，这样有性能问题，如果一个脚本一直含有超过阀值的内存使用情况，则会频繁的触发垃圾收集器运行，造成巨大的性能损耗

* 从IE7开始，javaScript引擎改变了工作方式，阀值变成了动态修改的，改善了性能损耗

* 部分浏览器可以触发垃圾收集，IE： window.CollectGarbage() Opera: window.opera.collect()

### 管理内存

* 分配给web浏览器的可用内存要比分配给桌面程序的少，这样防止web程序耗尽系统内存导致系统奔溃

* 确保更少的内存使用将会带来更好的性能体验

* 手动解除引用，尤其是全局变量

## 引用类型

* 构造函数就是一个普通的函数，只不过它是为了创造新对象而定义的，构造函数就是给将要创造的新对象定义了默认的属性和方法

* 创造Object实例的方法有两种 new 原生构造函数 对象字面量

* 对象的属性可以是字符串，如果是数字可以自动转化为字符串

* 必需参数可以是命名参数，可选参数可以选择对象字面量封装

* 点表示法和方括号语法都可以访问对象的属性，方括号的优势在于属性值可以是变量，或者某些会导致语法错误的属性 eg： “first name”

* 优先选择点表示法


### Array

* 创建方式： 使用Array构造函数， 对象字面量语法

* Javascript中的数组特点是： 可以保存任何数据类型，而且数组的长度是随着数据的添加自动增加的

* 使用构造函数创建数组的时候可以给函数传入数值代表数组的长度 像这样： 

```
var arr = new Array(20)
```
也可以直接传入数组中保存的值 像这样： 

```
var arr = new Array('asdfas', 'asdfasdf', 'afsdfa')
```

同时在创建的时候还可以省略new关键字 直接使用构造函数（Object() 也是如此）

```
var arr = Array()
```

***不建议使用这种构造函数的方式定义数组， 因为当你传入一个数值的时候会被认为是数组的长度，如果是多个就是数值或者别的类型就是数组元素***

* 数组的length属性非常灵活，它不仅是可读的，还可以写，通过设置length属性可以增加数组的长度，也可以删除数组元素 增加长度设置的数组访问的值都是undefined

#### 数组检测

* instanceof 

```
value instanceof Array
```
instanceof 虽然能检测出数组但是他有缺点： **instanceof 假设当前存在单一的全局环境 如果网页中还有多个框架，则每个框架下的数组的构造函数其实是不一样的，也就是说如果在框架1中检测框架2中的数组用instanceof  那么就会检测失败**

* Array.isArray() 这个方法就是为了解决上面的问题，判断到底是不是数组，

* 数组的toString()方法返回数组每一个元素的toString()方法，toLocaleString()则是返回每一个数组的元素的toLocaleString()方法，然后中间用逗号隔开，valueOf()方法则是返回原数组

* join()方法不传递参数或者传入undefined则是默认以逗号为分隔符

* 如果数组中有元素是undefined或者null，则在调用上面提到的几个方法后全部变为空字符串

* 数组的栈方法，push()可以接受任意数量的参数，并逐个将它们添加到数组的末尾，**同时返回数组的长度** pop()方法则是在数组的末尾删除一个元素，并返回删除的元素

* 队列方法，shift() 方法在数组的前端删除一个元素，并返回这个删除的元素，数组长度减少一，unshift() 则是在数组的前端添加若干个元素并返回数组的长度，

* sort()***比较的是数组元素的字符串格式*** 可以接受一个自定义函数

* concat() 方法合并数组，不会改变原来的数组，会创建并返回一个新数组，如果传入的是空，则简单复制原数组，如果是数组或者元素则是添加并返回新数组，不会改变原数组

* slice() 接受开始下标值和结束下标值(不包括) 创建并返回一个新数组，不会改变原数组，如果传入的负数则会用负数加上数组的长度来确定位置， 如果结束位置小于开始位置，则会返回空数组

* splice(index, length, element element) 可以删除元素，添加元素，替换元素  返回一个包含删除元素的新数组，

* 位置： indexOf lastIndexOf 这两个方法都会返回两个参数，第一个是查找的元素，第二个是起始查找的位置，如果没有查找到就会返回 -1 

* 迭代方法： every，some map filter forEach 每个方法都会接受两个参数，第一个是运行在每一个元素的函数，第二个是该函数的作用域对象，影响this的值， 函数接受三个参数，分别是元素本身，元素位置，数组本身，这些方法都不会改变数组本身

* 缩小范围： reduce 和 reduceRight arr.reduce((pre, next, index, curArr) => {})

### Date

* Date.parse()接受一个指定格式的字符串然后返回一个时间戳

* Date.now()获取当前时间时间戳

* new Date().valueOf() 方法返回时间戳

### RegExp 正则

`/pattern/` `flag` 正则表达式就是pattern和flag的组合体

* g 表示全局匹配并不是在发现匹配到第一个后就立即停止，i是忽略大小写，m是多行匹配

* 正则表达式中的原字符是： ( [ { \ ^ $ | ) ? * + . ] } 在使用这些元字符的时候需要转义

* 正则表达式字面量定义模式和RegExp()构造函数定义的模式有区别，ES3字面量的模式始终会共享一个实例，而构造函数每回都创建的是一个新的实例

***ES5开始明确规定，使用字面量定义的模式和构造函数定义的模式必须一摸一样，每次都会创建新的实例***

| 字符 | 含义 |
| --- | ---  |
| \   | 反斜杠表示转义，如果放在一个非特殊字符前则表示下一个字符是特殊的，如果放在一个特殊字符前则表示下一个字符不是特殊的了 例如： /a*/ 表示匹配0或多个a /a\*/ 表示匹配 a\*   |
| ^   | 匹配以什么开头  |
| $   | 匹配以什么结尾  | 
| *   | 匹配前一个一个表达式0次或者多次 等价于 {0, }| 
| +   | 匹配前一个表达式1次或者多次 等价于 {1, } |
| ?   | 匹配前一个表达式0次或者1次 |
| .   | 匹配任何除换行符之外的任何单个字符 |
| (x) | 匹配x并记住匹配项 这时候如果调用exec方法会返回捕获组|
| (?:x)| 匹配x但不记住捕获组，这种叫做非捕获括号|
| x(?=y) | 匹配x当且仅当后面跟着y |
| (?<=y)x| 匹配x当且仅当前面跟着y |
|x(?!y)| 匹配x当且仅当后面不跟着y |
| x\|y | 匹配x或者y |
| {n, m} | 匹配前面的字符出现至少n次 最多m次 如果n m 都等于0 则忽略这个值|
| [xyz] | 一个字符集合，匹配方括号中的任意字符 可以使用破折号指定字符范围 |
| [^xyz] | 匹配除括号中的字符 |
| \d | 匹配一个数字 等价于 [0-9] |
| \D | 匹配一个非数字字符 |
| \s | 匹配一个空白字符 |
| \S | 匹配一个非空白字符 | 
| \w | 匹配一个单字字符 |

* exec test

### Function

***函数是对象，是Function构造函数的实例，因此它具有跟其他引用类型一样的属性和方法，而函数名实际上是一个指向函数对象的指针***

* 理解可以通过函数表达式定义函数实际上就能提现出js中的函数是一个对象

* ***由于函数名仅仅是指向函数的指针，因为函数名与包含对象指针的其他变量没有什么不同，也就是说我们可以让函数跟对象一样拥有多个函数名***

函数声明的三种方式：

1. 函数声明语法方式

```
function func() {
  console.log(1)
}
```

2. 函数表达式方式

```
var func = function() {
   console.log(2)  
}
```

3. Function构造函数方式（这种方式可以传递若干个参数，但是最后一个参数永远被视为函数体）

```
var func = new Function(1,2,3,4,...., 'console.log(3)')
```



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
