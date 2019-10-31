## 触发条件

1. 如果表单里有一个type=”submit”的按钮，回车键生效。 
2. 如果表单里只有一个type=”text”的input，不管按钮是什么type，回车键生效。 
3. 如果按钮不是用input，而是用button，并且没有加type，IE下默认为type=button，FX默认为type=submit。 
4. 其他表单元素如textarea、select不影响，radio checkbox不影响触发规则，但本身在FX下会响应回车键，在IE下不响应。 
5. type=”image”的input，效果等同于type=”submit”，不知道为什么会设计这样一种type，不推荐使用，应该用CSS添加背景图合适些。 

## 解决方案

1. 表单响应回车键：保证表单里有个type=”submit”的按钮就行。
2. 不响应回车键：
    - 写一个无意义的文本框，隐藏起来。
    - 根据第3条规则，我们在用button的时候，尽量显式声明type以使浏览器表现一致。 
    - 判断按下的键是否为回车键
``` js
document.onkeypress = function(){
  if(event.keyCode == 13) {
    ··· // 处理其他内容
    return false;
  }
}
```
