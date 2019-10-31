## 先上总结:

"value是完整单词" 类型的比较符号:  ~=  ,  |=

"拼接字符串" 类型的比较符号:  *=  ,  ^=  ,  $=

1. attribute属性中包含value:　

`[attribute~=value]` 属性中包含独立的单词为value

``` css
e.g:[title~=flower]  -->  <img src="/i/eg_tulip.jpg" title="tulip flower" />
```

`[attribute*=value]` 属性中做字符串拆分，只要能拆出来value这个词就行

``` css
e.g:[title*=flower]   -->  <img src="/i/eg_tulip.jpg" title="ffffflowerrrrrr" />
```
 
2. attribute属性以value开头:

`[attribute|=value]` 属性中必须是完整且唯一的单词，或者以-分隔开

``` css
e.g:[lang|=en]     -->  <p lang="en">  <p lang="en-us">
```

`[attribute^=value]` 属性的前几个字母是value就可以

``` css
e.g:[lang^=en]    -->  <p lang="ennn">
```
 
3. attribute属性以value结尾:

`[attribute$=value]` 属性的后几个字母是value就可以
``` css
e.g:a[src$=".pdf"]
```
