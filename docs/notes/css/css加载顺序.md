## css样式的加载顺序及覆盖顺序

**css样式层叠优先级是:** 
浏览器缺省 < 外部样式表(引入的css文件) < 内部样式表(`<style>`标签内的样式) < 内联样式(style=”)

**其中样式表又有:** 
类选择器(.class) < 类派生选择器(.class h1) < ID选择器(#id) < ID派生选择器(#id h1)

派生选择器以前叫上下文选择器，所以完整的层叠优先级是: 
浏览器缺省 < 外部样式表(css文件) < 外部样式表类选择器 < 外部样式表类派生选择器 < 外部样式表ID选择器 < 外部样式表ID派生选择器 < 内部样式表(`<style>`标签内的样式) < 内部样式表类选择器 < 内部样式表类派生选择器 < 内部样式表ID选择器 < 内部样式表ID派生选择器 < 内联样式(style=”) 
共12个优先级

------

查找一些教材中(w3schools等)，只说css的顺序是“元素上的style” > “文件头上的style元素” >“外部样式文件”，但对于样式文件中的多个相同样式的优先级怎样排列，没有详细说明。经过测试和继续搜索，得知优先级如下排列：

- 样式表的元素选择器选择越精确，则其中的样式优先级越高。 
  id选择器指定的样式 > 类选择器指定的样式 > 元素类型选择器指定的样式

通过下面的实例可以得出此结论

```html
<style type="text/css">
    #p {color:red}
    .blue{color:blue}
    p{color:green}
</style>
<p> 我是绿色，p{color:green}</p>
<p class="blue"> 我是蓝色，class="blue"</p>
<p id="p" class="blue">我是红色，class="blue"不起作用</p>12345678
```

- 对于相同类型选择器指定的样式，在样式表文件中，越靠后的优先级越高。 
  注意：这里是样式表文件中越靠后的优先级越高，而不是在元素class出现的顺序。

比如以下实例，.class2在样式表中出现在.class3和.class1之后，所以.class2生效：

```html
<style type="text/css">
    .class3 {color: blue}
    .class1 {color: green;}  
    .class2 {color: red;}
</style>
<p class="class1 class2 class3"> 我是红色，class2生效</p>
<p>注意：这里是样式表文件中越靠后的优先级越高，而不是在元素class出现的顺序。</p>1234567
```

- 如果要让某个样式的优先级变高，可以使用!important来指定。

```html
<style type="text/css">
    .class3 {color: blue}
    .class1 {color: green !important;}  
    .class2 {color: red;}
</style>
<p class="class3 class2"> 我是红色，class2生效</p>
<p class="class1 class2 class3"> 我是绿色，class1生效</p>1234567
```

上述代码得知：如果同一个元素在没有其他样式的作用影响下，其Class定义了多个并以空格分开，其优先级顺序为：一个元素同时应用多个class，后定义的优先(即近者优先)，加上!important者最优先!