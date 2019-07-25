# banner-淡入淡出式效果

🌟效果页面：[https://jabinhu.github.io/banner/](https://jabinhu.github.io/banner/)

🎉代码：[https://github.com/JabinHu/banner/blob/master/index.html](https://github.com/JabinHu/banner/blob/master/index.html)

🎈如有问题，请提issue。

浏览器（本人电脑上装的版本比较新🤪）：
- chrome 版本 75.0.3770.100
- safari 版本 12.0 (14606.1.36.1.9)

## 代码分析

### HTML部分

准备5张图，放在页面的同级目录下。

``` html
<div id="wrap">
  <div class="banner">
    <img src="images/1.jpg" width="1226" height="460" alt="轮播图1">
  </div>

  <div class="banner">
    <img src="images/2.jpg" width="1226" height="460" alt="轮播图2">
  </div>

  <div class="banner">
    <img src="images/3.jpg" width="1226" height="460" alt="轮播图3">
  </div>

  <div class="banner">
    <img src="images/4.jpg" width="1226" height="460" alt="轮播图4">
  </div>

  <div class="banner">
    <img src="images/5.jpg" width="1226" height="460" alt="轮播图5">
  </div>

  <div class="tab">
    <span></span>
    <span></span>
    <span></span>
    <span></span>
    <span></span>
  </div>

  <div class="prev"></div>
  <div class="next"></div>
</div>
```

### css部分

css部分，仔细过下代码，就能明白意思。

``` css
<style>
  * {
    margin: 0;
    padding: 0;
  }

  #wrap {
    position: relative;
    margin: 20px auto;
    width: 1226px;
    height: 460px;
    user-select: none;
  }

  #wrap .banner {
    position: absolute;
    top: 0;
    width: 100%;
    height: 100%;
    opacity: 0;
    transition: opacity 2s;
  }

  #wrap .tab {
    position: absolute;
    bottom: 10px;
    right: 10px;
  }

  #wrap .tab span {
    display: inline-block;
    width: 6px;
    height: 6px;
    margin: 3px;
    background: rgba(105, 105, 105, 0.5);
    border-radius: 50%;
    cursor: pointer;
    border: 2px solid #887B6E;
  }

  #wrap .tab span.on {
    background: #E2CEB7;
  }

  #wrap .tab span:hover {
    background: #E2CEB7;
  }

  #wrap .prev {
    position: absolute;
    left: 20px;
    top: 195px;
    width: 40px;
    height: 70px;
    background: url("images/icon-slides.png") 80px;
    cursor: pointer;
  }

  #wrap .prev:hover {
    background: url("images/icon-slides.png");
  }

  #wrap .next {
    position: absolute;
    right: 20px;
    top: 195px;
    width: 40px;
    height: 70px;
    background: url("images/icon-slides.png") 40px;
    cursor: pointer;
  }

  #wrap .next:hover {
    background: url("images/icon-slides.png") 120px;
  }
</style>
```

### js部分

首先，通过类名、标签名方法获取各个节点：

``` js
// 获取各个节点
var body = document.getElementsByTagName("body")[0];
var banner = document.getElementsByClassName("banner");
var span = document.getElementsByClassName("tab")[0].getElementsByTagName("span");
var next = document.getElementsByClassName("next")[0];
var prev = document.getElementsByClassName("prev")[0];
```

初始化第一个图，显示第一个小圆点颜色：

``` js
// 初始化,num表示当前第几张图（数组以0开始）
banner[0].style.opacity = "1";
span[0].className = "on";
var num = 0;
```

对小圆点绑定点击事件：

``` js
// 小圆点绑定点击事件，先循环一遍，给每个圆点的下标值赋值，使得每个圆点对应一张图片，
// 再将所有小圆点样式置空，再设置当前被点击小圆点的样式（this.index代表当前点击圆点下标）
for (let i = 0; i < span.length; i++) {
  span[i].index = i;
  span[i].onclick = function () {
    for (let j = 0; j < span.length; j++) {
      num = this.index;
      span[j].className = "";
      banner[j].style.opacity = "0";
    }
    span[num].className = "on";
    banner[num].style.opacity = "1";
  }
}
```

对下一张图标绑定点击事件：

``` js
// 下一张图标绑定点击事件，临界值控制（最后的小圆点值为4，大于4时，需要回到第一张）
next.onclick = function () {
  for (let j = 0; j < span.length; j++) {
    if (span[j].className == "on") {
      span[j].className = "";
      banner[j].style.opacity = "0";
      j++;
      num++;
      if (j > 4) {
        j = 0;
        num = 0;
      }
      span[j].className = "on";
      banner[j].style.opacity = "1";
    }
  }
}
```

对上一张图标绑定点击事件：

``` js
// 上一张图标绑定点击事件
prev.onclick = function () {
  for (let j = 0; j < span.length; j++) {
    if (span[j].className == "on") {
      span[j].className = "";
      banner[j].style.opacity = "0";
      j--;
      num--;
      if (j < 0) {
        j = 4;
        num = 4;
      }
      span[j].className = "on";
      banner[j].style.opacity = "1";
    }
  }
}
```

最后设置一个定时器的函数，实现图片轮播：

``` js
function Time() {
  num++;
  if (num < 5) {
    for (let j = 0; j < span.length; j++) {
      span[j].className = "";
      banner[j].style.opacity = "0";
    }
    span[num].className = "on";
    banner[num].style.opacity = "1";
    if (num == 4) {
      num = -1;
    }
  } else {
    num = -1;
  }
}

clearInterval(timer);
var timer = setInterval(Time, 3000);

// 鼠标引入，清除定时器，轮播图停止
body.onmouseover = function () {
  clearInterval(timer);
}

//鼠标移出，重新调用定时器，轮播图开始
body.onmouseout = function () {
  clearInterval(timer);
  timer = setInterval(Time, 3000);
}
```

在调用定时器的时候，用的使setInterval，如果要使用setTimerout也是可以的；

在调用定时器要先清除定时器，（防止定时器一直叠加，使得轮播速度越来越快）
当鼠标移入时，轮播图停止，即定时器被清除，当鼠标移出时，定时器重新被调用。

## 总结

- 提前设置配置项:
  - 图片
  - 定时器的时间
  - 临界值设置为小圆点的个数（span.length），也可以自己预先定义。
> 有了配置之后，通过js来添加dom节点，不用去每次修改了图片去修改html代码。只需要修改配置就行。