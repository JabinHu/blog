## 如何理解模块化

### 面临的问题

技术的诞生是为了解决某个问题，模块化也是。在js模块化诞生之前，开发者面临很多问题：随着前端的发展，web技术日趋成熟，js功能越来越多，代码量也越来越大。之前一个项目通常各个页面公用一个js，但是js逐渐拆分，项目中引入的js越来越多：

```
<script src="zepto.js"></script>
<script src="jhash.js"></script>
<script src="fastClick.js"></script>
<script src="iScroll.js"></script>
<script src="underscore.js"></script>
<script src="handlebar.js"></script>
<script src="datacenter.js"></script>
<script src="util/wxbridge.js"></script>
<script src="util/login.js"></script>
<script src="util/base.js"></script>
```

当年我刚刚实习的时候，项目中的js就是类似这样，这样的js引入造成了问题：

1. 全局变量污染：各个文件的变量都是挂载到window对象上，污染全局变量。
2. 变量重名：不同文件中的变量如果重名，后面的会覆盖前面的，造成程序运行错误。
3. 文件依赖顺序：多个文件之间存在依赖关系，需要保证一定加载顺序问题严重。

这些问题严重干扰开发，也是日常开发中经常遇到的问题。

### 什么是模块化

![clipboard.png](https://segmentfault.com/img/bVbcmTm?w=300&h=199)

我觉得用乐高积木来比喻模块化再好不过了。每个积木都是固定的颜色形状，想要组合积木必须使用积木凸起和凹陷的部分进行连接，最后多个积木累积成你想要的形状。

模块化其实是一种规范，一种约束，这种约束会大大提升开发效率。将每个js文件看作是一个模块，每个模块通过固定的方式引入，并且通过固定的方式向外暴露指定的内容。

按照js模块化的设想，一个个模块按照其依赖关系组合，最终插入到主程序中。

## 模块化解决方案

模块化这种规范提出之后，得到社区和广大开发者的响应，不同时间点有多种实现方式。我们举个例子：a.js

```
// a.js
var aStr = 'aa';
var aNum = cNum + 1;
```

b.js

```
// b.js
var bStr = aStr + ' bb';
```

c.js

```
// c.js
var cNum = 0;
```

index.js

```
// index.js
console.log(aNum, bStr);
```

四份文件，不同的依赖关系（a依赖c，b依赖a，index依赖a b）在没有模块化的时候我们需要页面中这样：

```
<script src="./c.js"></script>    
<script src="./a.js"></script>
<script src="./b.js"></script>
<script src="./index.js"></script>
```

严格保证加载顺序，否则报错。

### 1. 闭包与命名空间

这是最容易想到的也是最简便的解决方式，早在模块化概念提出之前很多人就已经使用闭包的方式来解决变量重名和污染问题。

这样每个js文件都是使用**IIFE**包裹的，各个js文件分别在不同的词法作用域中，相互隔离，最后通过**闭包**的方式暴露变量。每个闭包都是单独一个文件，每个文件仍然通过script标签的方式下载，标签的顺序就是模块的依赖关系。

上面的例子我们用该方法修改下写法：

a.js

```
// a.js
var a = (function(cNum){
   var aStr = 'aa';
   var aNum = cNum + 1; 
    
    return {
       aStr: aStr,
       aNum: aNum
    };
})(cNum);
```

b.js

```
// b.js
var bStr = (function(a){
   var bStr = a.aStr + ' bb';
    
   return bStr;
})(a);
```

c.js

```
// c.js
var cNum = (function(){
   var cNum = 0;
    
   return cNum;
})();
```

index.js

```
;(function(a, bStr){
    console.log(a.aNum, bStr);
})(a, bStr)
```

这种方法下仍然需要在入口处严格保证加载顺序：

```
<script src="./c.js"></script>    
<script src="./a.js"></script>
<script src="./b.js"></script>
<script src="./index.js"></script>
```

这种方式最简单有效，也是**后续其他解决方案的基础**。这样做的意义：

1. 各个js文件之间避免了变量重名干扰，并且最少的暴露变量，避免全局污染。
2. 模块外部不能轻易的修改闭包内部的变量，程序的稳定性增加。
3. 模块与外部的连接通过IIFE传参，语义化更好，清晰地知道有哪些依赖。

不过各个模块的依赖关系仍然要通过加装script的顺序来保证。

### 2. 面向对象开发

一开始一些人在闭包的解决方案上做出了规范约束：每个js文件始终返回一个object，将内容作为object的属性。

比如上面的例子中b.js

```
// b.js
var b = (function(a){
   var bStr = a.aStr + ' bb';
    
   return {
       bStr: bStr
   };
})(a);
```

及时返回的是个值，也要用object包裹。后来很多人开始使用面向对象的方式开发插件：

```
;(function($){
    var LightBox = function(){
        // ...
    };
    
    LightBox.prototype = {
        // ....
    };
    
    window['LightBox'] = LightBox;
})($);
```

使用的时候：

```
var lightbox = new LightBox();
```

当年很多人都喜欢这样开发插件，并且认为能写出这种插件的水平至少不低。这种方法只是闭包方式的小改进，约束js文件返回必须是对象，对象其实就是一些个方法和属性的集合。这样的优点：

1. 规范化输出，更加统一的便于相互依赖和引用。
2. 使用‘类’的方式开发，便于后面的依赖进行扩展。

本质上这种方法只是对闭包方法的规范约束，并没有做什么根本改动。

### 3. YUI

早期雅虎出品的一个工具，模块化管理只是一部分，其还具有JS压缩、混淆、请求合并（合并资源需要server端配合）等性能优化的工具，说其是现有JS模块化的鼻祖一点都不过分。

```
// YUI - 编写模块
YUI.add('dom', function(Y) {
  Y.DOM = { ... }
})

// YUI - 使用模块
YUI().use('dom', function(Y) {
  Y.DOM.doSomeThing();
  // use some methods DOM attach to Y
})

// hello.js
YUI.add('hello', function(Y){
    Y.sayHello = function(msg){
        Y.DOM.set(el, 'innerHTML', 'Hello!');
    }
},'3.0.0',{
    requires:['dom']
})

// main.js
YUI().use('hello', function(Y){
    Y.sayHello("hey yui loader");
})
```

YUI的出现令人眼前一新，他提供了一种模块管理方式：通过YUI全局对象去管理不同模块，所有模块都只是对象上的不同属性，相当于是不同程序运行在操作系统上。YUI的核心实现就是闭包，不过好景不长，具有里程碑式意义的模块化工具诞生了。

### 4. CommonJs

2009年Nodejs发布，其中Commonjs是作为Node中模块化规范以及原生模块面世的。Node中提出的Commonjs规范具有以下特点：

1. 原生Module对象，每个文件都是一个Module实例
2. 文件内通过require对象引入指定模块
3. 所有文件加载均是同步完成
4. 通过module关键字暴露内容
5. 每个模块加载一次之后就会被缓存
6. 模块编译本质上是沙箱编译
7. 由于使用了Node的api，只能在服务端环境上运行

基本上Commonjs发布之后，就成了Node里面标准的模块化管理工具。同时Node还推出了npm包管理工具，npm平台上的包均满足Commonjs规范，随着Node与npm的发展，Commonjs影响力也越来越大，并且促进了后面模块化工具的发展，具有里程碑意义的模块化工具。之前的例子我们这样改写：

a.js

```
// a.js
var c = require('./c');

module.exports = {
    aStr: 'aa',
    aNum: c.cNum + 1
};
```

b.js

```
// b.js
var a = require('./a');

exports.bStr = a.aStr + ' bb';
```

c.js

```
// c.js
exports.cNum = 0;
```

入口文件就是 index.js

```
var a = require('./a');
var b = require('./b');

console.log(a.aNum, b.bStr);
```

可以直观的看到，使用Commonjs管理模块，十分方便。Commonjs优点在于：

1. 强大的查找模块功能，开发十分方便
2. 标准化的输入输出，非常统一
3. 每个文件引入自己的依赖，最终形成文件依赖树
4. 模块缓存机制，提高编译效率
5. 利用node实现文件同步读取
6. 依靠注入变量的沙箱编译实现模块化

这里补充一点沙箱编译：require进来的js模块会被Module模块注入一些变量，使用立即执行函数编译，看起来就好像：

```
(function (exports, require, module, __filename, __dirname) {
    //原始文件内容
})();
```

看起来require和module好像是全局对象，其实只是闭包中的入参，并不是真正的全局对象。之前专门整理探究过[ Node中的Module源码分析](https://segmentfault.com/a/1190000015139548)，也可以看看阮一峰老师的[require()源码解读](http://www.ruanyifeng.com/blog/2015/05/require.html)，或者廖雪峰老师的[CommonJS规范](http://javascript.ruanyifeng.com/nodejs/module.html)。

### 5. AMD和RequireJS

Commonjs的诞生给js模块化发展有了重要的启发，Commonjs非常受欢迎，但是局限性很明显：Commonjs基于Node原生api在服务端可以实现模块同步加载，但是仅仅局限于服务端，客户端如果同步加载依赖的话时间消耗非常大，所以需要一个在客户端上基于Commonjs但是对于加载模块做改进的方案，于是AMD规范诞生了。

[AMD](https://github.com/amdjs/amdjs-api/wiki/AMD)是"Asynchronous Module Definition"的缩写，意思就是"异步模块定义"。它采用异步方式加载模块，模块的加载不影响它后面语句的运行。所有依赖这个模块的语句，都定义在一个回调函数中，等到**所有依赖加载完成之后**（前置依赖），这个回调函数才会运行。

#### AMD规范

AMD与Commonjs一样都是js模块化规范，是一套抽象的约束，与2009年诞生。文档[这里](https://github.com/amdjs/amdjs-api/wiki/AMD)。该约束规定采用require语句加载模块，但是不同于CommonJS，它要求两个参数：

```
require([module], callback);
```

第一个参数[module]，是一个数组，里面的成员就是要加载的模块；第二个参数callback，则是加载成功之后的回调函数。如果将前面的代码改写成AMD形式，就是下面这样：

```
require(['math'], function (math) {

    math.add(2, 3);

});
```

定义了一个文件，该文件依赖math模块，当math模块加载完毕之后执行回调函数，这里并没有暴露任何变量。不同于Commonjs，在定义模块的时候需要使用define函数定义：

```
define(id?, dependencies?, factory);
```

define方法与require类似，id是定义模块的名字，仍然会在所有依赖加载完毕之后执行factory。

#### RequireJs

RequireJs是js模块化的工具框架，是AMD规范的具体实现。但是有意思的是，RequireJs诞生之后，推广过程中产生的AMD规范。文档[这里](http://requirejs.org/docs/api.html)。

RequireJs有两个最鲜明的特点：

1. 依赖前置：动态创建`<script>`引入依赖，在`<script>`标签的onload事件监听文件加载完毕；一个模块的回调函数必须得等到所有依赖都加载完毕之后，才可执行，类似Promise.all。
2. 配置文件：有一个main文件，配置不同模块的路径，以及shim不满足AMD规范的js文件。

还是上面那个例子：

配置文件main.js

```
requirejs.config({
    shim: {
        // ...
    },
    paths: {
        a: '/a.js',
        b: '/b.js',
        c: '/c.js',
        index: '/index.js'
    }
});

require(['index'], function(index){
    index();
});
```

a.js

```
define('a', ['c'], function(c){
    return {
        aStr: 'aa',
        aNum: c.cNum + 1
    }
});
```

b.js

```
define('b', ['a'], function(a){
    return {
        bStr = a.aStr + ' bb';
    }
});
```

c.js

```
define('c', function(){
    return {
        cNum: 0
    }
});
```

index.js

```
define('index', ['a', 'b'], function(a, b){
    return function(){
        console.log(a.aNum, b.bStr);
    }
});
```

页面中嵌入

```
<script src="/require.js" data-main="/main" async="async" defer></script>
```

RequireJs当年在国内非常受欢迎，主要是以下优点：

1. 动态并行加载js，依赖前置，无需再考虑js加载顺序问题。
2. 核心还是注入变量的沙箱编译，解决模块化问题。
3. 规范化输入输出，使用起来方便。
4. 对于不满足AMD规范的文件可以很好地兼容。

不过个人觉得RequireJs配置还是挺麻烦的，但是当年已经非常方便了。

### 6. CMD和SeaJs

#### CMD规范

同样是受到Commonjs的启发，国内（阿里）诞生了一个CMD（[Common Module Definition](https://github.com/cmdjs/specification/blob/master/draft/module.md)）规范。该规范借鉴了Commonjs的规范与AMD规范，在两者基础上做了改进。

```
define(id?, dependencies?, factory);
```

与AMD相比非常类似，CMD规范（2011）具有以下特点：

1. define定义模块，require加载模块，exports暴露变量。
2. 不同于AMD的依赖前置，CMD推崇依赖就近（需要的时候再加载）
3. 推崇api功能单一，一个模块干一件事。

#### SeaJs

SeaJs是CMD规范的实现，跟RequireJs类似，CMD也是SeaJs推广过程中诞生的规范。CMD借鉴了很多AMD和Commonjs优点，同样SeaJs也对AMD和Commonjs做出了很多兼容。

SeaJs核心特点：

1. 需要配置模块对应的url。
2. 入口文件执行之后，根据文件内的依赖关系整理出依赖树，然后通过插入`<script>`标签加载依赖。
3. 依赖加载完毕之后，执行根factory。
4. 在factory中遇到require，则去执行对应模块的factory，实现就近依赖。
5. 类似Commonjs，对所有模块进行缓存（模块的url就是id）。
6. 类似Commonjs，可以使用相对路径加载模块。
7. 可以向RequireJs一样前置依赖，但是推崇就近依赖。
8. exports和return都可以暴露变量。

修改下上面那个例子：

a.js

```
console.log('a1');
define(function(require,exports,module){
    console.log('inner a1');
    require('./c.js')
});
console.log('a2')
```

b.js

```
console.log('b1');
define(function(require,exports,module){
    console.log('inner b1');
});
console.log('b2')
```

c.js

```
console.log('c1');
define(function(require,exports,module){
    console.log('inner c1');
});
console.log('c2')
```

页面引入

```
<body>
    <script src="/sea.js"></script>
    <script>
    seajs.use(['./a.js','./b.js'],function(a,b){
        console.log('index1');
    })    
    </script>
</body>
```

对于seaJs中的就近依赖，有必要单独说一下。来看一下上面例子中的log顺序：

1. seaJs执行入口文件，入口文件依赖a和b，a内部则依赖c。

2. 依赖关系梳理完毕，开始动态script标签下载依赖，控制台输出：

   ```
   a1
   a2
   b1
   b2
   c1
   c2
   ```

3. 依赖加载之后，按照依赖顺序开始解析模块内部的define：`inner a1`

4. 在a模块中遇到了require('./c')，就近依赖这时候才去执行c模块的factory：`inner c1`

5. 然后解析b模块：`inner b1`

6. 全部依赖加载完毕，执行最后的factory：`index`

完整的顺序就是：

```
a1
a2
b1
b2
c1
c2
inner a1
inner c1 
inner b1
index
```

这是一个可以很好理解SeaJs的例子。

### 7. ES6中的模块化

之前的各种方法和框架，都出自于各个大公司或者社区，都是民间出台的结局方法。到了2015年，ES6规范中，终于将模块化纳入JavaScript标准，从此js模块化被官方扶正，也是未来js的标准。

之前那个例子再用ES6的方式实现一次：

a.js

```
import {cNum} from './c';

export default {
    aStr: 'aa',
    aNum: cNum + 1
};
```

b.js

```
import {aStr} from './a';

export const bStr = aStr + ' bb';
```

c.js

```
export const bNum = 0;
```

index.js

```
import {aNum} from './a';
import {bStr} from './b';

console.log(aNum, bStr);
```

可以看到，ES6中的模块化在Commonjs的基础上有所不同，增加了关键字import，export，default，as，from，而不是全局对象。另外深入理解的话，有两点主要的区别：

1. CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。
2. CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。

一个经典的例子：

```
// counter.js
exports.count = 0
setTimeout(function () {
  console.log('increase count to', ++exports.count, 'in counter.js after 500ms')
}, 500)

// commonjs.js
const {count} = require('./counter')
setTimeout(function () {
  console.log('read count after 1000ms in commonjs is', count)
}, 1000)

//es6.js
import {count} from './counter'
setTimeout(function () {
  console.log('read count after 1000ms in es6 is', count)
}, 1000)
```

分别运行 commonjs.js 和 es6.js：

```
➜  test node commonjs.js
increase count to 1 in counter.js after 500ms
read count after 1000ms in commonjs is 0
➜  test babel-node es6.js
increase count to 1 in counter.js after 500ms
read count after 1000ms in es6 is 1
```

这个例子解释了CommonJS 模块输出的是值的拷贝，也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。ES6 模块的运行机制与 CommonJS 不一样。JS 引擎对脚本静态分析的时候，遇到模块加载命令`import`，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值。换句话说，ES6 的`import`有点像 Unix 系统的“符号连接”，原始值变了，`import`加载的值也会跟着变。因此，ES6 模块是动态引用，并且不会缓存值，模块里面的变量绑定其所在的模块。

更多ES6模块化特点，参照阮一峰老师的[ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/module-loader)。

## 总结思考

写了这么多，其实都是蜻蜓点水地从使用方式和运行原理分析了不同方法的实现。现在重新看一下当时模块化的痛点：

1. 全局变量污染：各个文件的变量都是挂载到window对象上，污染全局变量。
2. 变量重名：不同文件中的变量如果重名，后面的会覆盖前面的，造成程序运行错误。
3. 文件依赖顺序：多个文件之间存在依赖关系，需要保证一定加载顺序问题严重。

不同的模块化手段都在致力于解决这些问题。前两个问题其实很好解决，使用闭包配合立即执行函数，高级一点使用沙箱编译，缓存输出等等。

我觉得真正的难点在于依赖关系梳理以及加载。Commonjs在服务端使用fs可以接近同步的读取文件，但是在浏览器中，不管是RequireJs还是SeaJs，都是使用动态创建script标签方式加载，依赖全部加载完毕之后执行，省去了开发手动书写加载顺序这一烦恼。

到了ES6，官方出台设定标准，不在需要出框架或者hack的方式解决该问题，该项已经作为标准要求各浏览器实现，虽然现在浏览器全部实现该标准尚无时日，但是肯定是未来趋势。

