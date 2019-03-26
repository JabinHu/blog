## 命名导出(named exports)

可以直接在任何变量或者函数前面加上一个 export 关键字，就可以将它导出。 

这种写法非常简洁，和平时几乎没有区别，唯一的区别就是在需要导出的地方加上一个 export 关键字。 

比如：

``` javascript
export const sqrt = Math.sqrt; 
export function square(x) { 
  return x * x;
} 
export function diag(x, y) {   
  return sqrt(square(x) + square(y)); 
} 
```

然后在另一个文件中这样引用：

```javascript
import { square, diag } from 'lib';
console.log(square(11)); // 121 
console.log(diag(4, 3)); 
```


你可能会注意到这个奇怪的语法` { square, diag } `不就是前面讲过的 destructing吗。所以你会以为还可以这样写：
``` javascript
import lib from 'lib'; 
square = lib.square; 
```


但是其实这样是错的，因为` import { square, diag } from 'lib’;` 是`import`的特有语法，并不是 destructing 语法，所以其实import的时候并不是直接把整个模块以对象的形式引入的。

如果你希望能通过 lib.square 的形式来写，你应该这样导入：
```javascript
 import * as lib from 'lib';  
 square = lib.square;
```


不过值得注意的一点是，如果你直接用babel编译，执行是会报错的。因为 babel 并不会完全编译 modules，他只是把 ES6 的modules语法编译成了 CMD 的语法，所以还需要用 browserify 之类的工具再次编译一遍。 

如果你发现 browserify 找不到 lib，可以改成` from ‘./lib’ `试试。

## 默认导出

大家会发现上面的写法比较麻烦，因为必须要指定一个名字。其实很多时候一个模块只导出了一个变量，根本没必要指定一个名字。 

还有一种用法叫默认导出，就是指定一个变量作为默认值导出：
``` javascript
//------ myFunc.js ------ 
export default function () { ... }; 
//------ main1.js ------
import myFunc from 'myFunc'; myFunc();
```


默认导出的时候不需要指定一个变量名，它默认就是文件名。 

这里的区别不仅仅是不用写名字，而是 导出的默认值就是模块本身，而不是模块下面的一个属性，即是` import myFunc from 'myFunc’; `而不是` import {myFunc} from 'myFunc’;`

**命名导出结合默认导出**

默认导出同样可以结合命名导出来使用：
``` javascript
export default function (obj) { 
  ...
};
export function each(obj, iterator, context) {   
  ...
}
export { each as forEach }
```


上面的代码导出了一个默认的函数，然后由导出了两个命名函数，我们可以这样导入：
``` javascript
 import _, { each } from 'underscore'; 
```


注意这个逗号语法，分割了默认导出和命名导出。

其实这个默认导出只是一个特殊的名字叫 default，你也可以就直接用他的名字，把它当做命名导出来用，下面两种写法是等价的：
```javascript
import { default as foo } from 'lib';
import foo from 'lib'; 
```


同样的，你也可以通过显示指定 default 名字来做默认导出, 下面两种写法是一样的：
``` javascript
 //------ module1.js ------ 
 export default 123;  
 //------ module2.js ------ 
 const D = 123; 
 export { D as default }; 
```


## 仅支持静态导入导出

ES6规范只支持静态的导入和导出，也就是必须要在编译时就能确定，在运行时才能确定的是不行的，比如下面的代码就是不对的：
```javascript
//动态导入
var mylib;
if (Math.random()) { 
  mylib = require('foo');
} else {
  mylib = require('bar'); 
} 
//动态导出 
if (Math.random()) {  
  exports.baz = ...; 
} 
```


为什么要这么做，主要是两点：

1. 性能，在编译阶段即完成所有模块导入，如果在运行时进行会降低速度
2. 更好的检查错误，比如对变量类型进行检查

## 各种导入和导出方式总结
```javascript
// Default exports and named exports 默认导出的可以随意取名
import theDefault, { named1, named2 } from 'src/mylib';
import theDefault from 'src/mylib';
import { named1, named2 } from 'src/mylib'; 
// Renaming: import named1 as myNamed1 
import { named1 as myNamed1, named2 } from 'src/mylib';  
// Importing the module as an object 
// (with one property per named export) 
import * as mylib from 'src/mylib'; 
// Only load the module, don’t import anything
import 'src/mylib'; 
```


如下几种导出方式：
```javascript
//命名导出
export var myVar1 = ...;
export let myVar2 = ...;
export const MY_CONST = ...; 
export function myFunc() {  
  ...
}
export function* myGeneratorFunc() {   
  ...
}
export class MyClass {  
   ... 
}
// default 导出 
export default 123; 
export default function (x) {
  return x 
}; 
export default x => x; 
export default class {    
  constructor(x, y) {  
    this.x = x;      
    this.y = y;    
  } 
}; 
//也可以自己列出所有导出内容 
const MY_CONST = ...;
function myFunc() {    
  ...
}  
export { MY_CONST, myFunc }; 
//或者在导出的时候给他们改个名字 
export { MY_CONST as THE_CONST, myFunc as theFunc };  
//还可以导出从其他地方导入的模块 
export * from 'src/other_module';
export { foo, bar } from 'src/other_module'; 
export { foo as myFoo, bar } from 'src/other_module'; 
```