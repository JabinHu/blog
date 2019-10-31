## &&与||

``` js
return a&&b如果a为true，则返回b，否则返回a；
return a||b 如果a为true，则返回a，否则返回b；
return a,b,c 返回c，就是返回最后一个；
```

## js ==与===区别（两个等号与三个等号）

1. 对于string,number等基础类型，==和===是有区别的

    - 不同类型间比较，==之比较“转化成同一类型后的值”看“值”是否相等，===如果类型不同，其结果就是不等
    - 同类型比较，直接进行“值”比较，两者结果一样
 
2. 对于Array,Object等高级类型，==和===是没有区别的
进行“指针地址”比较
 
3. 基础类型与高级类型，==和===是有区别的
    - 对于==，将高级转化为基础类型，进行“值”比较
    - 因为类型不同，===结果为false

js阻止事件冒泡
 `e.stopPropagation();`

js阻止链接默认行为，没有停止冒泡
`e.preventDefault(); `


## url的三个js编码函数escape(),encodeURI(),encodeURIComponent()简介

- `escape()`不能直接用于URL编码，它的真正作用是返回一个字符的Unicode编码值。比如"春节"的返回结果是`%u6625%u8282，escape()`不对"+"编码 主要用于汉字编码，现在已经不提倡使用。 

- `encodeURI()`是Javascript中真正用来对URL编码的函数。 编码整个url地址，但对特殊含义的符号`"; / ? : @ & = + $ , #"，`也不进行编码。对应的解码函数是：`decodeURI()`。 

- `encodeURIComponent()` 能编码`"; / ? : @ & = + $ , #"`这些特殊字符。对应的解码函数是decodeURIComponent()。
假如要传递带`&`符号的网址，所以用`encodeURIComponent()`