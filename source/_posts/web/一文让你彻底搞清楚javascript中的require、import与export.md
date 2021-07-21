---
title: 一文让你彻底搞清楚javascript中的require、import与export
date: 2018-12-06 09:33:32
categories: 
 - javascript
tags: 
 - javascript
 - web
 - require
 - import
 - export
copyright: false
cover: https://s2.ax1x.com/2019/10/19/KnwL80.jpg
---

前言
==

> 本文主要给大家介绍了关于javascript中require、import与export的相关内容，分享出来供大家参考学习，下面话不多说了，来一起看看详细的介绍吧。

为什么有模块概念
--------

理想情况下，开发者只需要实现核心的业务逻辑，其他都可以加载别人已经写好的模块。

但是，Javascript不是一种模块化编程语言，在es6以前，它是不支持”类”（class），所以也就没有”模块”（module）了。

require时代
---------

Javascript社区做了很多努力，在现有的运行环境中，实现”模块”的效果。

*** 原始写法 ***

模块就是实现特定功能的一组方法。
只要把不同的函数（以及记录状态的变量）简单地放在一起，就算是一个模块。
```
function m1(){
　//...
}
function m2(){
　//...　　
}
```
上面的函数 *** m1() *** 和 *** m2() *** ，组成一个模块。使用的时候，直接调用就行了。

这种做法的缺点很明显：”污染”了全局变量，无法保证不与其他模块发生变量名冲突，而且模块成员之间看不出直接关系。

对象写法
----

为了解决上面的缺点，可以把模块写成一个对象，所有的模块成员都放到这个对象里面
```
var module1 = new Object({
_count : 0,
　m1 : function (){
　　//...
　},
　m2 : function (){
　　//...
　}
});
```
上面的函数 *** m1() *** 和 *** m2() ***  ，都封装在module1对象里。使用的时候，就是调用这个对象的属性
```
module1.m1();    
```
这样的写法会暴露所有模块成员，内部状态可以被外部改写。比如，外部代码可以直接改变内部计数器的值。
```
module._count = 1;
```

立即执行函数写法
--------

使用”立即执行函数”（Immediately-Invoked Function Expression，IIFE），可以达到不暴露私有成员的目的
```
var module = (function() {
var _count = 0;
var m1 = function() {
alert(_count)
}
var m2 = function() {
alert(_count + 1)
}
 
return {
m1: m1,
m2: m2
}
})()
```
使用上面的写法，外部代码无法读取内部的_count变量。
```
　　console.info(module._count); //undefined
```
*** module *** 就是Javascript模块的基本写法。

主流模块规范
------

在es6以前，还没有提出一套官方的规范,从社区和框架推广程度而言,目前通行的javascript模块规范有两种：CommonJS 和 AMD

CommonJS规范
----------

[photos des="node.js"]
![](http://img.degenerate.cn/javascript/ayitg239.bmp)
[/photos]

2009年，美国程序员Ryan Dahl创造了node.js项目，将javascript语言用于服务器端编程。

这标志”Javascript模块化编程”正式诞生。前端的复杂程度有限，没有模块也是可以的，但是在服务器端，一定要有模块，与操作系统和其他应用程序互动，否则根本没法编程。

node编程中最重要的思想之一就是模块，而正是这个思想，让JavaScript的大规模工程成为可能。模块化编程在js界流行，也是基于此，随后在浏览器端，requirejs和seajs之类的工具包也出现了，可以说在对应规范下，require统治了ES6之前的所有模块化编程，即使现在，在ES6 module被完全实现之前，还是这样。

在CommonJS中,暴露模块使用 *** module.exports *** 和 *** exports *** ，很多人不明白暴露对象为什么会有两个,后面会介绍区别

在CommonJS中，有一个全局性方法 *** require() *** ，用于加载模块。假定有一个数学模块math.js，就可以像下面这样加载。
```
var math = require('math');
```
然后，就可以调用模块提供的方法：
```
　var math = require('math');
　math.add(2,3); // 5
```
正是由于CommonJS 使用的require方式的推动，才有了后面的AMD、CMD 也采用的require方式来引用模块的风格

AMD规范
-----

[photos des="requrie.js"]
![](http://img.degenerate.cn/javascript/5lofrnpn.bmp)
[/photos]

有了服务器端模块以后，很自然地，大家就想要客户端模块。而且最好两者能够兼容，一个模块不用修改，在服务器和浏览器都可以运行。

但是，由于一个重大的局限，使得CommonJS规范不适用于浏览器环境。还是上一节的代码，如果在浏览器中运行，会有一个很大的问题
```
var math = require('math');
math.add(2, 3);
```
第二行**math.add(2, 3)** ，在第一行**require(‘math')**之后运行，因此必须等math.js加载完成。也就是说，如果加载时间很长，整个应用就会停在那里等。

这对服务器端不是一个问题，因为所有的模块都存放在本地硬盘，可以同步加载完成，等待时间就是硬盘的读取时间。但是，对于浏览器，这却是一个大问题，因为模块都放在服务器端，等待时间取决于网速的快慢，可能要等很长时间，浏览器处于”假死”状态。

因此，浏览器端的模块，不能采用”同步加载”（synchronous），只能采用”异步加载”（asynchronous）。这就是AMD规范诞生的背景。

AMD是”Asynchronous Module Definition”的缩写，意思就是”异步模块定义”。它采用异步方式加载模块，模块的加载不影响它后面语句的运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行。

模块必须采用特定的define()函数来
```
define(id?, dependencies?, factory)
```

 1. id:字符串，模块名称(可选)
 2. dependencies: 是我们要载入的依赖模块(可选)，使用相对路径。,注意是数组格式
 3. factory: 工厂方法，返回一个模块函数

如果一个模块不依赖其他模块，那么可以直接定义在define()函数之中。
```
// math.js
　　define(function (){
　　　　var add = function (x,y){
　　　　　　return x+y;
　　　　};
　　　　return {
　　　　　　add: add
　　　　};
　　});
```
如果这个模块还依赖其他模块，那么define()函数的第一个参数，必须是一个数组，指明该模块的依赖性。
```
define(['Lib'], function(Lib){
　　　　function foo(){
　　　　　　Lib.doSomething();
　　　　}
　　　　return {
　　　　　　foo : foo
　　　　};
　　});
```
当require()函数加载上面这个模块的时候，就会先加载Lib.js文件。

AMD也采用require()语句加载模块，但是不同于CommonJS，它要求两个参数：
```
require([module], callback);
```
第一个参数[module]，是一个数组，里面的成员就是要加载的模块；第二个参数callback，则是加载成功之后的回调函数。如果将前面的代码改写成AMD形式，就是下面这样：
```
require(['math'], function (math) {
　math.add(2, 3);
});
```
math.add()与math模块加载不是同步的，浏览器不会发生假死。所以很显然，AMD比较适合浏览器环境。

目前，主要有两个Javascript库实现了AMD规范：require.js和curl.js。

CMD规范
-----

[photos des="CMD"]
![](http://img.degenerate.cn/javascript/lld60uma.bmp)
[/photos]

**CMD (Common Module Definition)**, 是seajs推崇的规范，CMD则是依赖就近，用的时候再require。它写起来是这样的：
```
define(function(require, exports, module) {
var clock = require('clock');
clock.start();
});
```
CMD与AMD一样，也是采用特定的define()函数来定义,用require方式来引用模块
```
define(id?, dependencies?, factory)
```
 - id:字符串，模块名称(可选)
 - dependencies: 是我们要载入的依赖模块(可选)，使用相对路径。,注意是数组格式
 - factory: 工厂方法，返回一个模块函数
```
define('hello', ['jquery'], function(require, exports, module) {
 
// 模块代码
 
});
```
如果一个模块不依赖其他模块，那么可以直接定义在define()函数之中。
```
define(function(require, exports, module) {
// 模块代码
});
```
注意：带 id 和 dependencies 参数的 define 用法不属于 CMD 规范，而属于 Modules/Transport 规范。

CMD与AMD区别
---------

AMD和CMD最大的区别是对依赖模块的执行时机处理不同，而不是加载的时机或者方式不同，二者皆为异步加载模块。

AMD依赖前置，js可以方便知道依赖模块是谁，立即加载；

而CMD就近依赖，需要使用把模块变为字符串解析一遍才知道依赖了那些模块，这也是很多人诟病CMD的一点，牺牲性能来带来开发的便利性，实际上解析模块用的时间短到可以忽略。

现阶段的标准

[photos des="ES6"]
![](http://img.degenerate.cn/javascript/d83wlrxw.bmp)
[/photos]


ES6标准发布后，module成为标准，标准使用是以export指令导出接口，以import引入模块，但是在我们一贯的node模块中，我们依然采用的是CommonJS规范，使用require引入模块，使用module.exports导出接口。

export导出模块
----------

export语法声明用于导出函数、对象、指定文件（或模块）的原始值。

注意：在node中使用的是exports,不要混淆了

export有两种模块导出方式：命名式导出（名称导出）和默认导出（定义式导出），命名式导出每个模块可以多个，而默认导出每个模块仅一个。
```
export { name1, name2, …, nameN };
export { variable1 as name1, variable2 as name2, …, nameN };
export let name1, name2, …, nameN; // also var
export let name1 = …, name2 = …, …, nameN; // also var, const
 
export default expression;
export default function (…) { … } // also class, function*
export default function name1(…) { … } // also class, function*
export { name1 as default, … };
 
export * from …;
export { name1, name2, …, nameN } from …;
export { import1 as name1, import2 as name2, …, nameN } from …;
```


 - name1… nameN－导出的“标识符”。导出后，可以通过这个“标识符”在另一个模块中使用import引用
 - default－设置模块的默认导出。设置后import不通过“标识符”而直接引用默认导入
 - －继承模块并导出继承模块所有的方法和属性
 - as－重命名导出“标识符”
 - from－从已经存在的模块、脚本文件…导出

命名式导出
-----

模块可以通过export前缀关键词声明导出对象，导出对象可以是多个。这些导出对象用名称进行区分，称之为命名式导出。
```
export { myFunction }; // 导出一个已定义的函数
export const foo = Math.sqrt(2); // 导出一个常量
```
我们可以使用*和from关键字来实现的模块的继承：
```
export * from 'article';
```
模块导出时，可以指定模块的导出成员。导出成员可以认为是类中的公有对象，而非导出成员可以认为是类中的私有对象：
```
var name = 'IT笔录';
var domain = 'http://itbilu.com';
 
export {name, domain}; // 相当于导出
{name:name,domain:domain}
```
模块导出时，我们可以使用as关键字对导出成员进行重命名：
```
var name = 'IT笔录';
var domain = 'http://itbilu.com';
 
export {name as siteName, domain};
```
**注意**：下面的语法有严重错误的情况：
```
// 错误演示
export 1; // 绝对不可以
 
var a = 100;
export a;
```
export在导出接口的时候，必须与模块内部的变量具有一一对应的关系。直接导出1没有任何意义，也不可能在import的时候有一个变量与之对应

export a虽然看上去成立，但是a的值是一个数字，根本无法完成解构，因此必须写成export {a}的形式。即使a被赋值为一个function，也是不允许的。而且，大部分风格都建议，模块中最好在末尾用一个export导出所有的接口，例如：
```
export {fun as default,a,b,c};
```

默认导出
----

默认导出也被称做定义式导出。命名式导出可以导出多个值，但在在import引用时，也要使用相同的名称来引用相应的值。而默认导出每个导出只有一个单一值，这个输出可以是一个函数、类或其它类型的值，这样在模块import导入时也会很容易引用。
```
export default function() {}; // 可以导出一个函数
export default class(){}; // 也可以出一个类
```

命名式导出与默认导出
----------

默认导出可以理解为另一种形式的命名导出，默认导出可以认为是使用了default名称的命名导出。

下面两种导出方式是等价的：
```
const D = 123;
 
export default D;
export { D as default };
```

export使用示例
----------

使用名称导出一个模块时：
```
// "my-module.js" 模块
export function cube(x) {
return x * x * x;
}
const foo = Math.PI + Math.SQRT2;
export { foo };
```
在另一个模块（脚本文件）中，我们可以像下面这样引用：
```
import { cube, foo } from 'my-module';
console.log(cube(3)); // 27
console.log(foo); // 4.555806215962888
```
使用默认导出一个模块时：
```
// "my-module.js"模块
export default function (x) {
return x * x * x;
}
```
在另一个模块（脚本文件）中，我们可以像下面这样引用，相对名称导出来说使用更为简单：
```
// 引用 "my-module.js"模块
import cube from 'my-module';
console.log(cube(3)); // 27
```
import引入模块
----------

import语法声明用于从已导出的模块、脚本中导入函数、对象、指定文件（或模块）的原始值。

import模块导入与export模块导出功能相对应，也存在两种模块导入方式：命名式导入（名称导入）和默认导入（定义式导入）。

import的语法跟require不同，而且import必须放在文件的最开始，且前面不允许有其他逻辑代码，这和其他所有编程语言风格一致。
```
import defaultMember from "module-name";
import * as name from "module-name";
import { member } from "module-name";
import { member as alias } from "module-name";
import { member1 , member2 } from "module-name";
import { member1 , member2 as alias2 , [...] } from "module-name";
import defaultMember, { member [ , [...] ] } from "module-name";
import defaultMember, * as name from "module-name";
import "module-name";
```


 - name－从将要导入模块中收到的导出值的名称
 - member, memberN－从导出模块，导入指定名称的多个成员
 - defaultMember－从导出模块，导入默认导出成员
 - alias, aliasN－别名，对指定导入成员进行的重命名
 - module-name－要导入的模块。是一个文件名
 - as－重命名导入成员名称（“标识符”）
 - from－从已经存在的模块、脚本文件等导入

命名式导入
-----

我们可以通过指定名称，就是将这些成员插入到当作用域中。导出时，可以导入单个成员或多个成员：

**注意：**花括号里面的变量与export后面的变量一一对应
```
import {myMember} from "my-module";
import {foo, bar} from "my-module";
```
通过*符号，我们可以导入模块中的全部属性和方法。当导入模块全部导出内容时，就是将导出模块（'my-module.js'）所有的导出绑定内容，插入到当前模块（'myModule'）的作用域中：
```
import * as myModule from "my-module";
```
导入模块对象时，也可以使用as对导入成员重命名，以方便在当前模块内使用：
```
import {reallyReallyLongModuleMemberName as shortName} from "my-module";
```
导入多个成员时，同样可以使用别名：
```
import {reallyReallyLongModuleMemberName as shortName, anotherLongModuleName as short} from "my-module";
```
导入一个模块，但不进行任何绑定：
```
import "my-module";
```

默认导入
----

在模块导出时，可能会存在默认导出。同样的，在导入时可以使用import指令导出这些默认值。

直接导入默认值：
```
import myDefault from "my-module";
```
也可以在命名空间导入和名称导入中，同时使用默认导入：
```
import myDefault, * as myModule from "my-module"; // myModule 做为命名空间使用
```
或
```
import myDefault, {foo, bar} from "my-module"; // 指定成员导入
```
#### import使用示例
```
// --file.js--
function getJSON(url, callback) {
let xhr = new XMLHttpRequest();
xhr.onload = function () {
callback(this.responseText)
};
xhr.open("GET", url, true);
xhr.send();
}
 
export function getUsefulContents(url, callback) {
getJSON(url, data => callback(JSON.parse(data)));
}
 
// --main.js--
import { getUsefulContents } from "file";
getUsefulContents("http://itbilu.com", data => {
doSomethingUseful(data);
});
```
#### default关键字
```
// d.js
export default function() {}
 
// 等效于：
function a() {};
export {a as default};
```
#### 在import的时候，可以这样用：
```
import a from './d';
 
// 等效于，或者说就是下面这种写法的简写，是同一个意思
import {default as a} from './d';
```
这个语法糖的好处就是import的时候，可以省去花括号{}。

简单的说，如果import的时候，你发现某个变量没有花括号括起来（没有*号），那么你在脑海中应该把它还原成有花括号的as语法。

所以，下面这种写法你也应该理解了吧：
```
import $,{each,map} from 'jquery';
```
import后面第一个$是{defalut as $}的替代写法。

#### as关键字

as简单的说就是取一个别名,export中可以用，import中其实可以用：
```
// a.js
var a = function() {};
export {a as fun};
 
// b.js
import {fun as a} from './a';
a();
```
上面这段代码，export的时候，对外提供的接口是fun，它是a.js内部a这个函数的别名，但是在模块外面，认不到a，只能认到fun。

import中的as就很简单，就是你在使用模块里面的方法的时候，给这个方法取一个别名，好在当前的文件里面使用。之所以是这样，是因为有的时候不同的两个模块可能通过相同的接口，比如有一个c.js也通过了fun这个接口：
```
// c.js
export function fun() {};
```
如果在b.js中同时使用a和c这两个模块，就必须想办法解决接口重名的问题，as就解决了。

CommonJS中module.exports 与 exports的区别
------------------------------------

#### Module.exports

The module.exports object is created by the Module system. Sometimes this is not acceptable; many want their module to be an instance of some class. To do this, assign the desired export object to module.exports. Note that assigning the desired object to exports will simply rebind the local exports variable, which is probably not what you want to do.

> 译文：module.exports对象是由模块系统创建的。 有时这是难以接受的；许多人希望他们的模块成为某个类的实例。 为了实现这个，需要将期望导出的对象赋值给module.exports。 注意，将期望的对象赋值给exports会简单地重新绑定到本地exports变量上，这可能不是你想要的。

#### Module.exports

The exports variable is available within a module's file-level scope, and is assigned the value of module.exports before the module is evaluated. It allows a shortcut, so that module.exports.f = … can be written more succinctly as exports.f = …. However, be aware that like any variable, if a new value is assigned to exports, it is no longer bound to module.exports:

> 译文：exports变量是在模块的文件级别作用域内有效的，它在模块被执行前被赋于 module.exports 的值。它有一个快捷方式，以便 module.exports.f = … 可以被更简洁地写成exports.f = … 。 注意，就像任何变量，如果一个新的值被赋值给exports，它就不再绑定到module.exports(其实是exports.属性会自动挂载到没有命名冲突的module.exports.属性)

从[Api](http://nodejs.cn/api/modules.html#modules_module_exports)文档上面的可以看出，从require导入方式去理解，关键有两个变量(全局变量module.exports，局部变量exports)、一个返回值(module.exports)
```
function require(...) {
var module = { exports: {} };
((module, exports) => {
// 你的被引入代码 Start
// var exports = module.exports = {}; (默认都有的)
function some_func() {};
exports = some_func;
// 此时，exports不再挂载到module.exports，
// export将导出{}默认对象
module.exports = some_func;
// 此时，这个模块将导出some_func对象，覆盖exports上的some_func
// 你的被引入代码 End
})(module, module.exports);
// 不管是exports还是module.exports，最后返回的还是module.exports
return module.exports;
}
```
#### demo.js:
```
console.log(exports); // {}
console.log(module.exports); // {}
console.log(exports === module.exports); // true
console.log(exports == module.exports); // true
console.log(module);
/**
Module {
id: '.',
exports: {},
parent: null,
filename: '/Users/larben/Desktop/demo.js',
loaded: false,
children: [],
paths:
[ '/Users/larben/Desktop/node_modules',
'/Users/larben/node_modules',
'/Users/node_modules',
'/node_modules' ] }
*/
```
注意

每个js文件一创建，都有一个var exports = module.exports = {} , 使exports和module.exports都指向一个空对象。

module.exports和exports所指向的内存地址相同

本文转载自：[https://www.jb51.net/article/124442.htm](https://www.jb51.net/article/124442.htm)