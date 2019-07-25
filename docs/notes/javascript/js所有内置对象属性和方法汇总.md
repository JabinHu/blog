

# JS三大对象

对象，是任何一个开发者都无法绕开和逃避的话题，她似乎有些深不可测，但如此伟大和巧妙的存在，一定值得你去摸索、发现、征服。

我们都知道，JavaScript有3大对象，分别是`本地对象`、`内置对象`和`宿主对象`。

在此引用ECMA-262（ECMAScript的制定标准）对于他们的定义：

- 本地对象
  - 与宿主无关，独立于宿主环境的ECMAScript实现提供的对象。
  - 简单来说，本地对象就是 ECMA-262 定义的类（引用类型）。
  - 这些引用类型在运行过程中需要通过new来创建所需的实例对象。
  - 包含：`Object`、`Array`、`Date`、`RegExp`、`Function`、`Boolean`、`Number`、`String`等。
- 内置对象
  - 与宿主无关，独立于宿主环境的ECMAScript实现提供的对象。
  - 在 ECMAScript 程序开始执行前就存在，本身就是实例化内置对象，开发者无需再去实例化。
  - 内置对象是本地对象的子集。
  - 包含：`Global`和`Math`。
  - ECMAScript5中增添了`JSON`这个存在于全局的内置对象。
- 宿主对象
  - 由 ECMAScript 实现的宿主环境提供的对象，包含两大类，一个是宿主提供，一个是自定义类对象。
  - 所有非本地对象都属于宿主对象。
  - 对于嵌入到网页中的JS来说，其宿主对象就是浏览器提供的对象，浏览器对象有很多，如`Window`和`Document`等。
  - 所有的`DOM`和`BOM`对象都属于宿主对象。

> 关于专业名词：本地对象也经常被叫做原生对象或内部对象，包含Global和Math在内的内置对象在《JavaScript高级程序设计》里也被叫做单体内置对象，很多时候，干脆也会直接把本地对象和内置对象统称为“内置对象”，也就是说除了宿主对象，剩下的都是ECMAScript的内部的“内置”对象。
>
> 声明：本文也将采取这种统称为“内置对象”的方式，比如文章标题。

# Object类型

## 属性

**constructor**
**prototype**

## 实例方法

**1、toString()**

功能：返回当前对象的字符串形式，返回值为String类型。

示例：

```js
[1,'2',true].toString(); //"1,2,true"
(new Date()).toString(); //"Sun Sep 24 2017 14:52:20 GMT+0800 (CST)"
({name:'ryan'}).toString(); //"[object Object]"
```

> 该方法属于Object对象，由于所有的对象都"继承"了Object的对象实例，因此几乎所有的实例对象都可以使用该方法。
>
> JavaScript的许多内置对象都重写了该函数，以实现更适合自身的功能需要。

**2、toLocaleString**

功能：返回当前对象的"本地化"字符串形式，以便于当前环境的用户辨识和使用，返回值为String类型。

示例：

```js
(1234567).toLocaleString(); //"1,234,567"
(6.37588).toLocaleString(); //"6.376"
(new Date()).toLocaleString(); //"2017/9/24 下午2:58:21"
```

**3、valueOf()**

功能：返回指定对象的原始值。

> JavaScript的许多内置对象都重写了该函数，以实现更适合自身的功能需要。因此，不同类型对象的valueOf()方法的返回值和返回值类型均可能不同。

## 静态方法

**1、Object.assign(target, ...sources)**

功能：把一个或多个源对象的可枚举、自有属性值复制到目标对象中，返回值为目标对象。
参数：

- 目标对象（必须）
- 至少一个源对象（可选）

示例：

```js
var target = {
    a:1
};
var source1 = {
    b:2
};
var source2 = {
    c:function(){
      console.log('c');
    }
};
Object.assign(target,source1,source2);
console.log(target); //{a: 1, b: 2, c: ƒ}
```

拓展：自定义实现一个assign方法

```js
//自定义一个assign方法
  function copy(target){
    if(target == null){
      throwError('出错：Cannot convert undefined or null to object');
    }
    var target = new Object(target);
    for(var i = 1;i < arguments.length;i ++){
      var source = arguments[i];
      for(var key in source){
        if(source.hasOwnProperty(key)){
          //若当前属性为源对象自有属性，则拷贝至目标对象
          target[key] = source[key];
        }
      }
    }
    return target;
  }
```

**2、Object.create(proto [,propertiesObject])**

功能：创建一个对象，其原型为prototype，同时可添加多个属性。 
参数：

- proto(必须)：原型对象，可以为null表示没有原型。
- descriptors(可选)：包含一个或多个属性描述符的对象。

propertiesObject参数详解：

- 数据属性
  - value：值
  - writable：是否可修改属性的值
  - configurable：是否可通过delete删除属性，重新定义
  - enumerable：是否可for-in枚举
- 访问属性
  - get()：访问
  - set()：设置

示例：

```js
function Person(name){
    this.name = name;
  }
  Person.prototype.say = function(){console.log('my name is ' + this.name +',my age is ' + this.age);}

  var person = new Person('ryan');
  var p = Object.create(person,{
    age:{
      value: 23,
      writable: true,
      configurable: true
    },
    sex:{
      configurable: true,
      get:function(){return sex + '士';},
      set:function(value){sex = value;}
    }
  });
  
  p.sex = '男';
  p.say(); //'my name is ryan,my age is 23'
  console.log(p.sex); //'男士'
  p.sex = '女';
  console.log(p.sex); //'女士'
```

> 总结：Object.create(proto [,propertiesObject]) 是E5中提出的一种新的对象创建方式，第一个参数是要继承的原型，如果不是一个子函数，可以传一个null，第二个可选参数是对象的属性描述符。

**3、Object.defineProperty(obj, prop, descriptor)**

功能：在一个对象上定义一个新属性或修改一个现有属性，并返回该对象。

参数：

- obj（必须）：被操作的目标对象
- prop（必须）：被定义或修改的目标属性
- descriptor（必须）：属性的描述符

示例：

```js
var obj = {};
Object.defineProperty(obj,'name',{
    writable: true,
    configurable: true,
    enumerable: false,
    value: '张三'
});

console.log(obj.name); //'张三'
for(var key in obj){
    console.log(obj[key]); //无结果
}
```

> 总结：在参数 descriptor中，如果不指定configurable, writable, enumerable ，则这些属性默认值为false，如果不指定value, get, set，则这些属性默认值为undefined。

**4、Object.defineProperties(obj, props)**

功能：在一个对象上定义一个或多个新属性或修改现有属性，并返回该对象。

参数：

- obj（必须）：被操作的目标对象
- props（必须）：该对象的一个或多个键值对定义了将要为对象添加或修改的属性的具体配置

示例：

```js
var obj = {};
Object.defineProperties(obj,{
    name:{
      writable: true,
      configurable: true,
      enumerable: false,
      value: '张三'
    },
    age:{
      writable: true,
      configurable: true,
      enumerable: true,
      value: 23
    }
});

console.log(obj.name); //'张三'
console.log(obj.age); //23
for(var key in obj){
    console.log(obj[key]); //23
}
```

**5、Object.seal(obj) / Object.isSealed(obj)**
功能：密封对象，阻止其修改现有属性的配置特性，即将对象的所有属性的configurable特性设置为false（也就是全部属性都无法重新配置，唯独可以把writable的值由true改为false，即冻结属性），并阻止添加新属性，返回该对象。

参数：

- obj（必须）：被密封的对象

示例：

```js
var obj = {name:'张三'};

Object.seal(obj);
console.log(Object.isSealed(obj)); //true

obj.name = '李四'; //修改值成功
console.log(obj.name); //'李四'
obj.age = 23; //无法添加新属性
console.log(obj.age); //undefined

Object.defineProperty(obj,'name',{ 
    writable: true,
    configurable: true,
    enumerable: true
}); //报错：Cannot redefine property: name
```

补充：Object.isSealed(obj)用于判断目标对象是否被密封，返回布尔值。

> 将一个对象密封后仅能保证该对象不被扩展且全部属性不可重配置，但是原属性值却是可以被修改的。

**6、Object.freeze(obj) / Object.isFrozen(obj)**

功能：完全冻结对象，在seal的基础上，属性值也不可以修改，即每个属性的wirtable也被设为false。

参数：

- obj（必须）：被冻结的对象

示例：

```js
var obj = {name:'张三'};

Object.freeze(obj);
console.log(Object.isFrozen(obj)); //true

obj.name = '李四'; //修改值失败
console.log(obj.name); //'张三'
obj.age = 23; //无法添加新属性
console.log(obj.age); //undefined

Object.defineProperty(obj,'name',{ 
    writable: true,
    configurable: true,
    enumerable: true
}); //报错：Cannot redefine property: name
```

补充：Object.isFrozen(obj)用于判断目标对象是否被冻结，返回布尔值。

**7、getOwnPropertyDescriptor(obj, prop)**

功能：获取目标对象上某自有属性的配置特性（属性描述符），返回值为配置对象。

参数：

- obj(必须)：目标对象
- prop(必须)：目标自有属性

示例：

```js
var obj = {};

Object.defineProperty(obj,'name',{
    writable: true,
    configurable: false,
    enumerable: true,
    value: '张三'
});

var prop = Object.getOwnPropertyDescriptor(obj,'name');
console.log(prop); //{value: "张三", writable: true, enumerable: true, configurable: false}
```

**8、Object.getOwnPropertyNames(obj)**

功能：获取目标对象上的全部自有属性名（包括不可枚举属性）组成的数组。

参数：

- obj(必须)：目标对象

示例：

```js
var obj = {};
obj.say = function(){};

Object.defineProperties(obj,{
    name:{
      writable: true,
      configurable: true,
      enumerable: true,
      value: '张三'
    },
    age:{
      writable: true,
      configurable: true,
      enumerable: false,
      value: 23
    }
});

var arr = Object.getOwnPropertyNames(obj);
console.log(arr); //["say", "name", "age"]
```

**9、Object.getPrototypeOf(obj)**

功能：获取指定对象的原型，即目标对象的prototype属性的值。

参数：

- obj(必须)：目标对象

示例：

```js
function Person(name){
    this.name = name;
}

var person = new Person('张三');
var p = Object.create(person); //对象p的原型为person
console.log(p); //Person {}

var __ptoto__ = Object.getPrototypeOf(p);
console.log(__ptoto__); //Person {name: "张三"}
```

**10、Object.setPrototypeOf(obj, proto)**

功能：设置目标对象的原型为另一个对象或null，返回该目标对象。

参数：

- obj(必须)：目标对象
- proto(必须)：原型对象

示例：

```js
var obj = {a:1};
var proto = {};
Object.setPrototypeOf(obj,proto); //设置obj对象的原型

proto.b = 2; //为该原型对象添加属性
proto.c = 3;

console.log(obj.a); //1
console.log(obj.b); //2
console.log(obj.c); //3
```

解析：上述代码将proto对象设为obj对象的原型，所以从obj对象上可以顺利读取到proto 对象的属性，也就是原型链上的属性。

> Object.setPrototypeOf()方法的作用与__proto__相同，用来设置当前对象的原型指向的对象(prototype)。它是 ES6 正式推荐的设置原型对象的方法。

**11、Object.keys(obj)**

功能：获取目标对象上所有可枚举属性组成的数组。

参数：

- obj(必须)：目标对象

示例：

```js
var person = {
    type:'person',
    say:function(){}
  };
  //以person对象为原型，创建obj对象
  var obj = Object.create(person,{
    sex:{
      writable: true,
      configurable: true,
      enumerable: false, //设置sex属性为不可枚举
      value: 'male'
    },
    age:{
      writable: true,
      configurable: true,
      enumerable: true, //设置age属性为可枚举
      value: 23
    }
  });

  obj.name = '张三'; //自定义属性name默认为可枚举
  console.log(obj.propertyIsEnumerable('name')); //true，成功验证name属性为可枚举

  //用for-in可获取obj上全部可枚举的属性（包括自有和原型链上的）
  var arr = [];
  for(var key in obj){
    arr.push(key);
  }
  console.log(arr); //["age", "name", "type", "say"]

  //用Object.keys()可获取obj上全部可枚举的自有属性
  console.log(Object.keys(obj)); // ["age", "name"]
```

> 总结：Object.keys(obj)方法获取的集合和for-in遍历获取的不同在于，Object.keys()只获取目标对象上可枚举的自有属性，而for-in遍历会包含原型链上可枚举属性一并获取。
>
> Object.keys()和Object.getOwnPropertyNames()的相同之处都是获取目标对象的自有属性，区别在于，后者会连同不可枚举的自有属性也一并获取组成数组并返回。

**12、Object.preventExtensions(obj) / Object.isExtensible(obj)**

功能：使某一对象不可扩展，也就是不能为其添加新属性。

参数：

- obj(必须)：目标对象

补充：Object.isExtensible(obj)方法用于判断一个对象是否可扩展，即是否可以添加新属性。

示例：

```js
var obj = {
  name: '张三'
};

Object.preventExtensions(obj); //阻止obj的可扩展性
console.log(Object.isExtensible(obj)); //false，表明obj对象为不可扩展，即阻止成功

obj.age = 23; //默认添加失败
console.log(obj.age); //undefined
```

# Array类型

## Array 对象属性

**1、length**

设置或返回数组中元素的数目。

> 设置 length 属性可改变数组的大小。如果设置的值比其当前值小，数组将被截断，其尾部的元素将丢失。如果设置的值比它的当前值大，数组将增大，新的元素被添加到数组的尾部，它们的值为 undefined。

**2、constructor**

返回对创建此对象的数组函数的引用。

**3、prototype**

使您有能力向对象添加属性和方法。

## Array 对象方法

**1、concat()**

- 用于连接两个或多个数组，该方法不会改变现有的数组，而是返回被连接数组的一个副本。
- 如果要进行 concat() 操作的参数是数组，那么添加的是数组中的元素，而不是数组。

**2、join()**

- 把数组中的所有元素放入一个字符串，元素是通过指定的分隔符进行分隔的。
- 若省略了分隔符参数，则使用逗号作为分隔符。

**3、push()**

- 向数组的末尾添加一个或多个元素，并返回新的数组长度。

**4、pop()**

- 用于删除数组的最后一个元素，把数组长度减1，并返回被删除元素。
- 如果数组已经为空，则 pop() 不改变数组，并返回 undefined。

**5、shift()**

- 用于把数组的第一个元素从其中删除，并返回被移除的这个元素。
- 如果数组是空的，那么 shift() 方法将不进行任何操作，返回 undefined。
- 该方法是直接修改原数组。

**6、unshift()**

- 向数组的开头添加一个或更多元素，并返回新的数组长度。
- 该方法是直接修改原数组。

**7、reverse()**

- 用于颠倒数组中元素的顺序。
- 该方法会直接修改原数组，而不会创建新数组。

**8、sort()**

- 用于对数组的元素进行排序。
- 该排序直接修改原数组，不生成副本。
- 该方法接受一个可选参数，若未使用参数，将按字母顺序对数组元素进行排序，说得更精确点，是按照字符编码的顺序进行排序。要实现这一点，首先应把数组的元素都转换成字符串（如有必要），以便进行比较。
- 如果想按照其他标准进行排序，就需要提供比较函数，该函数要比较两个值，然后返回一个用于说明这两个值的相对顺序的数字。比较函数应该具有两个参数 a 和 b，其返回值如下：
  - 若 a 小于 b，排序后 a 应该在 b 之前，则返回一个小于 0 的值。
  - 若 a 等于 b，则返回 0。
  - 若 a 大于 b，则返回一个大于 0 的值。

**9、slice(start [,end])**

- 截取原数组从start到end位置（不包含它）元素组成的子数组。
- 该方法返回一个新数组，不会修改原数组。
- 若未指定end参数，那么截取尾巴直到原数组最后一个元素（包含它）。

**10、splice(index,howmany [,item1,item2...])**

- 删除从 index 处开始的hownamy个元素，并且用可选参数列表中声明的一个或多个值来替换那些被删除的元素。
- 该方法返回的是含有被删除的元素组成的数组，若无被删元素，则返回空数组。
- 若参数只有index，那么原数组将从index开始删除直至结尾。
- 该方法直接修改原数组。

**map()**:返回一个新的Array，每个元素为调用func的结果

**filter()**:返回一个符合func条件的元素**新数组**

**some()**:方法用于检测数组中的元素是否有满足指定条件的，若满足返回true，否则返回false；

```js
array.some(function(item,index,array){
	// item:当前元素的值；
	// index:当前元素的索引；
	// array:当前元素的数组对象；
})
```


some()方法会依次执行数组的每个元素；如果有一个元素满足条件，则表达式返回true，剩余的元素不会再执行检测。如果没有满足条件的元素，则返回false；

**every()**:方法用于检测数组中所有元素是否都符合指定条件，若符合返回true，否则返回false；
```js
array.every(function(item,index,array){
	// item:当前元素的值；
	// index:当前元素的索引；
	// array:当前元素的数组对象；
})
```

every()方法使用指定函数检测数组中的所有元素；如果数组中检测到有一个元素不满足，则整个表达式返回false，且剩余的元素不会再进行检测。如果所有元素都满足条件，则返回true；

> 注意：some()和every()不会对空数组进行检测，且不会改变原来的数组。
> [].some(e => e) 返回false，[].every(e => e) f返回true。

**forEach()**:没有返回值，只是针对每个元素调用func

**reduce()**：reduce方法有两个参数，第一个参数是一个callback，用于针对数组项的操作；第二个参数则是传入的初始值，这个初始值用于单个数组项的操作。需要注意的是，reduce方法返回值并不是数组，而是形如初始值的经过叠加处理后的操作。

# Date类型

Date对象：封装一个时间点，提供操作时间的API。Date对象中封装的是从1970年1月1日0点至今的毫秒数。

**创建Date对象4种方式**

```js
var now = new Date(); //获取客户端的当前系统时间

var date = new Date("1994/02/04 03:23:55"); //创建自定义时间

date.toString(); // "Fri Feb 04 1994 03:23:55 GMT+0800 (中国标准时间)"
date.toDateString(); // "Fri Feb 04 1994"
date.toTimeString(); // "03:23:55 GMT+0800 (中国标准时间)"
date.toLocaleString(); // "1994/2/4 上午3:23:55"
date.toLocaleDateString(); // "1994/2/4"
date.toLocaleTimeString(); // "上午3:23:55"

var date = new Date(yyyy, MM, dd, hh, mm, ss); //创建自定义时间

var oldDate = new Date("1994/02/04");

var newDate = new Date(oldDate); //复制一个时间对象
```

**日期API**

日期分量：FullYear、Month、Date、Day、Hours、Minutes、Seconds、Milliseconds。
每一个日期分量都有一个`get`和`set`方法（除了Day没有set方法），分别用于获取和设置时间对象。

> 日期的单位及范围:
> 
> 年FullYear (无范围) 
> 
> 月Month (0~11, 0开始,没有12) 
> 
> 日Date (1~31, 和现实生活一样) 
> 
> 星期Day (0~6, 0是星期日,没有7)
> 
> 时Hours (0~23. 0开始，没有24)
> 
> 分Minutes (0~59)
> 
> 秒Seconds (0~59)
> 
> 毫秒MilliSeconds
> 

# RegExp类型

### RegExp对象属性

**1、global**

- 描述：RegExp 对象是否具有标志 g，即全局匹配。
- 值：true或false。

**2、ignoreCase**

- 描述：RegExp 对象是否具有标志 i，即忽略大小写。
- 值：一个整数，它声明的是上一次匹配文本之后的第一个字符的位置。

**3、lastIndex**

- 描述：lastIndex用于规定下次匹配的起始位置。
- 值：true或false。

> 不具有标志 g 和不表示全局模式的 RegExp 对象不能使用 lastIndex 属性。

### RegExp对象方法

**1、compile()**

- compile() 方法用于在脚本执行过程中编译正则表达式。
- compile() 方法也可用于改变和重新编译正则表达式。

**2、exec()**

- 功能：用于检索字符串中的正则表达式的匹配。
- 参数：string，必须，要检索的字符串。
- 返回值：返回一个数组，其中存放匹配的结果。如果未找到匹配，则返回值为 null。

**3、test()**

- 功能：用于检测一个字符串是否匹配某个模式。
- 参数：string，必须，要检索的字符串。
- 返回值：true或者false。

> 注意：支持正则表达式的 String 对象的方法有：search()、match()、replace()和split()。

# Function类型

## Function对象属性

**1、arguments**

- arguments.length：获取函数实参的个数
- arguments.callee：获取函数对象本身的引用
- arguments.callee.length：获取函数形参的个数

> Javascrip中每个函数都会有一个Arguments对象实例arguments，它引用着函数的实参，可以用数组下标的方式"[]"引用每个实际传入的参数。

示例：

```js
function say(a,b,c){
  console.log(arguments.length); //2
  console.log(arguments[0],arguments[1]); //hello world
}
say('hello','world');
```

## Function对象方法

**1、toString()**

- 功能：将函数体转换成对应的字符串。

# Boolean类型

常用方法：

**1、toString()**

- 功能：根据布尔值返回字符串 "true" 或 "false"。

> 注释：在 Boolean 对象被用于字符串环境中时，此方法会被自动调用。

**2、valueOf()**

- 功能：返回 Boolean 对象的原始值。

# Number类型

常用方法：

**1、toString()**

功能：将Number数值转换为字符串，该方法接受一个可选参数基数，若省略该参数，则默认基数为10，即十进制。

```js
var num = 10;
console.log(num.toString(2)); //1010
```

**2、toLocaleString()**
功能：把一个 Number 对象转换为本地格式的字符串。

**3、valueOf()**
功能：返回一个 Number 对象的基本数字值。

> valueOf() 方法通常由 JavaScript 在后台自动进行调用，而不是显式地处于代码中。

# String类型

## String对象属性

**1、length**

功能：String 对象的 length 属性声明了该字符串中的字符数。

## String对象方法

**1、charAt()**

- 功能：返回指定位置的字符。
- 参数：必须，为目标字符的下标位置。

> 若参数 index 不在 0 与 string.length 之间，该方法将返回一个空字符串。

**2、charCodeAt()**

- 功能：返回在指定的位置的字符的 Unicode 编码。
- 参数：必须，为目标字符的下标位置。

> 若参数 index 不在 0 与 string.length 之间，该方法将返回NaN。

**3、indexOf()**

- 功能：检索字符串，返回指定子字符串在字符串中首次出现的位置。
- 参数1：检索目标子字符串，必须。
- 参数2：在字符串中开始检索的位置，可选。其合法取值是 0 到 stringObject.length - 1。如省略该参数，则将从字符串的首字符开始检索。

> 注意：indexOf() 方法对大小写敏感！
> 注意：如果要检索的字符串值没有出现，则该方法返回 -1。

**4、lastIndexOf()**

- 功能：从后向前搜索字符串，返回指定子字符串在字符串中首次出现的位置。
- 参数1：检索目标子字符串，必须。
- 参数2：在字符串中开始检索的位置，可选。其合法取值是 0 到 stringObject.length - 1。如省略该参数，则将从字符串的最后一个字符开始检索。

**5、match()**

- 功能：返回指定位置的字符。
- 参数：必须，规定要检索的字符串值或待匹配的 RegExp 对象。
- 返回值：存放匹配结果的数组。该数组的内容依赖于 regexp 是否具有全局标志 g。

> 如果 regexp 没有标志 g，那么 match() 方法就只能在 stringObject 中执行一次匹配。如果没有找到任何匹配的文本， match() 将返回 null。否则，它将返回一个数组，其中存放了与它找到的匹配文本有关的信息。该数组的第 0 个元素存放的是匹配文本，而其余的元素存放的是与正则表达式的子表达式匹配的文本。除了这些常规的数组元素之外，返回的数组还含有两个对象属性。index 属性声明的是匹配文本的起始字符在 stringObject 中的位置，input 属性声明的是对 stringObject 的引用。
>
> 如果 regexp 具有标志 g，则 match() 方法将执行全局检索，找到 stringObject 中的所有匹配子字符串。若没有找到任何匹配的子串，则返回 null。如果找到了一个或多个匹配子串，则返回一个数组。不过全局匹配返回的数组的内容与前者大不相同，它的数组元素中存放的是 stringObject 中所有的匹配子串，而且也没有 index 属性或 input 属性。

示例：

```js
var s = 'hello21 world21';
console.log(s.match(/\d{2}/)); //[ '21', index: 5, input: 'hello21 world21' ]

var s = 'hello21 world21';
console.log(s.match(/\d{2}/g)); //[ '21', '21' ]
```

**6、replace()**

- 功能：在字符串中用一些字符替换另一些字符，或替换一个与正则表达式匹配的子串。
- 参数1：regexp/substr，必须，规定子字符串或要匹配的 RegExp 对象。
- 参数2：replacement，必须，用于替换的字符串值。
- 返回值：替换后的一个新字符串。

示例：

```js
var s = 'hello world hello';
console.log(s.replace('hello','hi')); //hi world hello
console.log(s.replace(/hello/,'hi')); //hi world hello
console.log(s.replace(/hello/g,'hi')); //hi world hi
```

> replace方法返回一个新字符串，并不会修改原字符串。

**7、search()**

- 功能：用于检索字符串中指定的子字符串，或检索与正则表达式相匹配的子字符串。
- 参数：regexp/substr，必须，规定子字符串或要匹配的 RegExp 对象。
- 返回值：原字符串中第一次匹配到目标字符串的起始位置。

示例：

```js
var s = 'hello world hello';
console.log(s.search('hello')); //0
console.log(s.search(/hello/g)); //0
console.log(s.search(/hello2/)); //-1
```

> search()方法不执行全局匹配，它将忽略标志 g。也就是说，它只匹配一次。若没匹配到结果，则返回-1。

**8、toLowerCase() & toUpperCase()**

- 功能：把字符串转换为小写/大写。
- 返回值：一个新的字符串。

示例：

```js
var s = 'Hello World';
console.log(s.toLowerCase()); //hello world
console.log(s.toUpperCase()); //HELLO WORLD
```

**9、concat()**

- 功能：用于连接两个或多个字符串。
- 语法：stringObject.concat(stringX,stringX,...,stringX)
- 参数：
- 返回值：衔接后的一个新字符串。

> concat方法不会修改原字符串。
> stringObject.concat() 与 Array.concat() 很相似。
> 通常使用 " + " 运算符来进行字符串的连接运算通常会更简便一些。

示例：

```js
var s1 = 'hello ';
var s2 = 'world ';
var s3 = '233';
console.log(s1.concat(s2,s3)); //hello world 233
```

**10、split()**

- 功能：用于把一个字符串分割成字符串数组，是 Array.join( ) 的逆操作。
- 参数1：separator，必须，字符串或正则表达式，从该参数指定的地方分割原字符串。
- 参数2：howmany，可选，指定返回数组的最大长度。
- 返回值：一个字符串数组。

示例：

```js
var s = 'hi baby';
console.log(s.split('')); //[ 'h', 'i', ' ', 'b', 'a', 'b', 'y' ]
console.log(s.split(' '));  //[ 'hi', 'baby' ]
console.log(s.split('b')); //[ 'hi ', 'a', 'y' ]
```

**11、slice()**

- 功能：截取字符串的某个部分，并以新的字符串返回被提取的部分。
- 参数1：截取的起始位置，必须。
- 参数2：截取的结束位置，可选。
- 返回值：截取部分，一个新的字符串。

> 注意：String.slice() 与 Array.slice() 相似。
> slice方法的两个参数接受负值，若为负数，则该参数规定的是从字符串的尾部开始算起的位置。也就是说，-1 指字符串的最后一个字符，-2 指倒数第二个字符，以此类推。
> 若未指定第二个参数，则默认截取至字符串的末尾。
> slice方法不修改原字符串。

示例：

```js
var s = 'hi baby';
console.log(s.slice(3)); //baby
console.log(s.slice(1,5)); //i ba
console.log(s.slice(-4)); //baby
console.log(s.slice(-4,-2)); //ba
```

**12、substr()**

- 功能：截取从指定下标开始的指定数目的字符。
- 参数1：start，必须，截取的起始位置，接受负值。
- 参数2：length，可选，截取字符串的长度，若未指定，则默认截取到原字符串的末尾。
- 返回值：截取部分，一个新的字符串。

> 注意：ECMAscript 没有对该方法进行标准化，因此不建议使用它。

示例：

```js
var s = 'hi baby';
console.log(s.substr(3)); //baby
console.log(s.substr(3,2)); //ba
console.log(s.substr(-3,2)); //ab
```

**13、substring()**

- 功能：截取字符串中介于两个指定下标之间的字符。
- 参数1：start，必须，截取的起始位置。
- 参数2：end，可选，截取的结束位置，若未指定，则默认截取到原字符串的末尾。
- 返回值：截取部分，一个新的字符串。

示例：

```js
var s = 'hi baby';
console.log(s.substring(3)); //baby
console.log(s.substring(3,5)); //ba
console.log(s.substring(5,3)); //ba
console.log(s.substring(3,3)); //''
```

> 注意：与 slice() 和 substr() 方法不同的是，substring() 不接受负的参数。
> 如果参数 start 与 stop 相等，那么该方法返回的一个空串。
> 如果 start 比 stop 大，那么该方法在提取子串之前会先交换这两个参数。

# Global对象（全局对象）

> 关于全局对象：全局对象只是一个对象，而不是类。既没有构造函数，也无法实例化一个新的全局对象。

## 属性

**Infinity**
代表正的无穷大的数值。

示例：

```js
console.log(6/0); //Infinity
console.log(-6/0); //-Infinity
console.log(0/0); //NaN
console.log(1.7976931348623157E+10308); //Infinity
console.log(-1.7976931348623157E+10308); //-Infinity
```

> Infinity代表了超出JavaScript处理范围的数值。也就是说JS无法处理的数值都是Infinity。实践证明，JS所能处理的最大值是1.7976931348623157e+308，而最小值是5e-324。

**NaN**
代表非数字的值。

示例：

```js
var a = Number('100');
var b = Number('hello world');

console.log(a); //100
console.log(b); //NaN
console.log(isNaN(a)); //false
console.log(isNaN(b)); //true
```

> 提示：请使用 isNaN() 方法来判断一个值是否是数字，原因是 NaN 与所有值都不相等，包括它自己。

**Undefined**
代表未定义的值。

示例：

```js
var a;
var b = '';
var c = null;

console.log(a === undefined); //true
console.log(b === undefined); //false
console.log(c == undefined); //true
```

> 提示：判断一个变量是否未定义，只能用 === undefined 运算来测试，因为 == 运算符会认为 undefined 值等价于 null，即undefined == null会返回true。
>
> 注释：null 表示无值，而 undefined 表示一个未声明的变量，或已声明但没有赋值的变量，或一个并不存在的对象属性。

## 方法

**1、encodeURI(URIString)**

功能：将字符串作为URI进行编码，返回值为URIstring 的副本。

参数：

- URIString(必须)：一个待编码的字符串。

示例：

```js
console.log(encodeURI('http://www.baidu.com')); //http://www.baidu.com
console.log(encodeURI('http://www.baidu.com/my mind')); //http://www.baidu.com/my%20mind
console.log(encodeURI(',/?:@&=+$#')); //,/?:@&=+$#
```

> 该方法不会对 ASCII 字母和数字进行编码，也不会对这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( ) 。
>
> 该方法的目的是对 URI 进行完整的编码，因此对以下在 URI 中具有特殊含义的 ASCII 标点符号，encodeURI() 函数是不会进行转义的：;/?:@&=+$,#
>
> 提示：如果 URI 组件中含有分隔符，比如 ? 和 #，则应当使用 encodeURIComponent() 方法分别对各组件进行编码。

**2、encodeURIComponent(URIString)**

功能：将字符串作为URI组件进行编码，返回值为URIstring的副本。

该方法不会对 ASCII 字母和数字进行编码，也不会对这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( ) 。

其他字符（比如 ：;/?:@&=+$,# 这些用于分隔 URI 组件的标点符号），都是由一个或多个十六进制的转义序列替换的。

参数：

- URIString(必须)：一个待编码的字符串。

示例：

> encodeURI和encodeURIComponent的区别：
>
> 它们都是编码URL，唯一区别就是编码的字符范围，其中encodeURI方法不会对下列字符编码 ASCII字母、数字、~!@#$&*()=:/,;?+'
> encodeURIComponent方法不会对下列字符编码 ASCII字母、数字、~!*()'
> 所以encodeURIComponent比encodeURI编码的范围更大。
> 实际例子来说，encodeURIComponent会把 http:// 编码成 http%3A%2F%2F 而encodeURI却不会。

使用场景：

- 当你需要编码整个URL，然后使用这个URL，则使用encodeURI。

```js
console.log(encodeURI('http://www.baidu.com/home/some other thing'));
//编码后为：http://www.baidu.com/home/some%20other%20thing; 其中，空格被编码成了%20

//但是如果你用了encodeURIComponent
console.log(encodeURIComponent('http://www.baidu.com/home/some other thing'));
//http%3A%2F%2Fwww.baidu.com%2Fhome%2Fsome%20other%20thing 连 "/" 都被编码了，整个URL已经没法用了
```

- 当你需要编码URL中的参数时，那么使用encodeURIComponent。

```js
var param = "http://www.baidu.com/home/"; //param为参数
param = encodeURIComponent(param);
var url = "http://www.baidu.com?next=" + param;
console.log(url) //'http://www.baidu.com?next=http%3A%2F%2Fwww.baidu.com%2Fhome%2F'
//显然，参数中的 "/" 被编码了，而如果用encodeURI肯定要出问题，因为后面的/是需要编码的。
```

补充：相应的，存在decodeURI()和decodeURIComponent是用来解码的，逆向操作。

**3、parseInt(string,radix)**

功能：解析一个字符串，并返回一个整数。

参数：

- string(必须)：待解析的字符串
- radix(可选)：表示要解析的数字的基数。该值介于 2 ~ 36 之间。
  如果省略该参数或其值为 0，则数字将以 10 为基础来解析。如果它以 “0x” 或 “0X” 开头，将以 16 为基数。如果该参数小于 2 或者大于 36，则 parseInt() 将返回 NaN。

示例：

```js
console.log(parseInt('10')); //10
console.log(parseInt('11',9)); //10 (9+1)
console.log(parseInt('11',2)); //3 (2+1)
console.log(parseInt('17',8)); //15 (8+7)
console.log(parseInt('1f',16)); //31 (16+15)
console.log(parseInt('010')); //10
console.log(parseInt('0x0011')); //17
```

**4、parseFloat()**

功能：解析一个字符串，并返回一个浮点数。
该函数指定字符串中的首个字符是否是数字。如果是，则对字符串进行解析，直到到达数字的末端为止。

参数：

- string(必须)：待解析的字符串

示例：

```js
console.log(parseFloat('10')); //10
console.log(parseFloat('10.00')); //10 
console.log(parseFloat('10.33')); //10.33
console.log(parseFloat(' 60 ')); //60 首尾的空格会忽略
console.log(parseFloat('23 34 45')); //23 中间的空格不会忽略，会中断
console.log(parseFloat('23 years')); //23
console.log(parseFloat('i am 23')); //NaN
```

> 提示：开头和结尾的空格是允许的。如果字符串的第一个字符不能被转换为数字，那么 parseFloat() 会返回 NaN。如果只想解析数字的整数部分，请使用 parseInt() 方法。

**5、isFinite(number)**

功能：用于检查其参数是否是无穷大。

参数：

- number(必须)：待检测数字。
  如果 number 是有限数字（或可转换为有限数字），那么返回 true。否则，如果 number 是 NaN（非数字），或者是正、负无穷大的数，则返回 false。

示例：

```js
console.log(isFinite(123)); //true
console.log(isFinite(-1.23)); //true
console.log(isFinite(5-2)); //true
console.log(isFinite(0)); //true
console.log(isFinite(0/0)); //false
console.log(isFinite('Hello')); //false
```

**6、isNaN(number)**

功能：用于检查其参数是否为非数字值。

参数：

- number(必须)：待检测数字。
  如果 number 是非数字值 NaN（或者能被转换成NaN），返回 true，否则返回 false。

示例：

```js
console.log(isNaN(123)); //false
console.log(isNaN(-1.23)); //false
console.log(isNaN(5-2)); //false
console.log(isNaN(0)); //false
console.log(isNaN(0/0)); //true
console.log(isNaN('Hello')); //true
```

> 提示：isNaN() 函数通常用于检测 parseFloat() 和 parseInt() 的结果，以判断它们表示的是否是合法的数字。当然也可以用 isNaN() 函数来检测算数错误，比如用 0 作除数的情况。

**7、Number(object)**

功能：把对象的值转换为数字。

参数：

- object(必须)：待转换的对象。 
  如果参数是 Date 对象，Number() 返回从1970年1月1日至今的毫秒数，即时间戳。如果对象的值无法转换为数字，那么 Number() 函数返回 NaN。

示例：

```js
console.log(Number(new Boolean(true))); //1
console.log(Number(new Boolean(false))); //0
console.log(Number(new Date())); //1506266494726
console.log(Number(new String('999'))); //999
console.log(Number(new String('999 888'))); //NaN
```

**8、String(object)**

功能：把对象的值转换为字符串。

参数：

- object(必须)：待转换的对象。

示例：

```js
console.log(String(new Boolean(true))); //'true'
console.log(String(new Boolean(false))); //'false'
console.log(String(new Date())); //'Sun Sep 24 2017 23:25:43 GMT+0800 (CST)'
console.log(String(new String('999'))); //'999'
console.log(String(new String('999 888'))); //'999 888'
console.log(String(12345)); //'12345'
```

# Math对象

常用方法：

```js
Math.abs(); //取绝对值
Math.ceil(); //向上取整
Math.floor(); //向下取整
Math.round(); //四舍五入取整
Math.random(); //生成0~1间的随机数(>0)
Math.max(x,y); //取x、y中较大的那个
Math.min(x,y); //取x、y中较小的那个
```

# JSON对象

我们常说的对象字面量其实不是JSON对象，但是有真正的JSON对象。

两者完全不一样概念，在新版的浏览器里JSON对象已经被原生的内置对象了，目前有2个静态方法：JSON.parse用来将JSON字符串反序列化成对象，JSON.stringify用来将对象序列化成JSON字符串。

老版本的浏览器不支持这个对象，但你可以通过json2.js来实现同样的功能。

## JSON对象方法

**1、JSON.parse()**

- 功能：将字符串反序列化成对象
- 参数：JSON字符串
- 返回值：对象

示例：

```js
var jsonString = '{"name":"ryan"}'; //JSON字符串（比如从AJAX获取字符串信息）
var obj = JSON.parse(jsonString); //将字符串反序列化成对象
console.log(obj); //{ name: 'ryan' }
console.log(obj.name == 'ryan'); //true
```

**2、JSON.stringify()**

- 功能：将一个对象解析为JSON字符串
- 参数：对象
- 返回值：JSON字符串

示例：



```js
var obj = {name:'ryan',age:23};
var jsonString = JSON.stringify(obj);
console.log(jsonString); //'{"name":"ryan","age":23}'
```