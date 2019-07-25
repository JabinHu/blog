# banner-carousel 跑马式效果

🌟效果页面：[https://jabinhu.github.io/banner-carousel/](https://jabinhu.github.io/banner-carousel/)

🎉代码：[https://github.com/JabinHu/banner-carousel/blob/master/index.html](https://github.com/JabinHu/banner-carousel/blob/master/index.html)

🎈如有问题，请提issue。

浏览器（本人电脑上装的版本比较新🤪）：
- chrome 版本 75.0.3770.100
- safari 版本 12.0 (14606.1.36.1.9)

## 代码分析

### HTML部分

准备5张图，放在页面的同级目录下。

``` html
<div id="container">
  <div id="list" style="left:-1226px">
    <img src="images/5.jpg" alt=""/>
    <img src="images/1.jpg" alt=""/>
    <img src="images/2.jpg" alt=""/>
    <img src="images/3.jpg" alt=""/>
    <img src="images/4.jpg" alt=""/>
    <img src="images/5.jpg" alt=""/>
    <img src="images/1.jpg" alt=""/>
  </div>

  <div id="buttons">
    <span></span>
    <span></span>
    <span></span>
    <span></span>
    <span></span>
  </div>

  <div id="prev"></div>
  <div id="next"></div>
</div>
```

**疑问：为什么要在第一张图片前加上最后一张，最后一张图片前加上第一张？**

**答：让banner看起来有无线滚动的感觉。如果不加首尾图片，当停留在第一张图片点击上一张图片（或者停留在最后一张图片点击下一张图片），会立马移动到最后一张图片（或者立马移动到第一张图片），视觉效果不友好。**

### css部分

css部分，仔细过下代码，就能明白意思。

``` css
<style>
  * {
      margin: 0;
      padding: 0;
    }

  #container {
    margin: 50px auto;
    position: relative;
    width: 1226px;
    height: 460px;
    overflow: hidden;
  }

  #list {
    position: absolute;
    width: 8582px;  /*7张图片总宽度*/
  }

  #list img {
    float: left;
    width: 1226px;  /*每张图片的宽度*/
    height: 460px;  /*每张图片的高度*/
  }

  #buttons {
    height: 10px;
    width: 100px;
    position: absolute;
    left: 0;  /*设置水平居中*/
    right: 0;  /*设置水平居中*/
    margin: 0 auto;  /*设置水平居中*/
    bottom: 20px;
  }

  #buttons span {
    float: left;
    margin-right: 5px;
    width: 10px;
    height: 10px;
    border: 1px solid #ccc;
    border-radius: 50%;
    cursor: pointer;
  }

  #buttons .on {
    background: orangered;
  }

  #container #prev {
    position: absolute;
    left: 20px;
    top: 195px;
    width: 40px;
    height: 70px;
    background: url("images/icon-slides.png") 80px;
    cursor: pointer;
  }

  #container #prev:hover {
    background: url("images/icon-slides.png");
  }

  #container #next {
    position: absolute;
    right: 20px;
    top: 195px;
    width: 40px;
    height: 70px;
    background: url("images/icon-slides.png") 40px;
    cursor: pointer;
  }

  #container #next:hover {
    background: url("images/icon-slides.png") 120px;
  }
</style>
```

### js部分

首先，通过Id、标签名方法获取各个节点：

``` js
// 获取各个节点
var container = document.getElementById("container");
var list = document.getElementById("list");
var prev = document.getElementById("prev");
var next = document.getElementById("next");
var buttons = document.getElementById("buttons").getElementsByTagName("span");
```

初始化“控制变量”，以及显示第一个小圆点颜色：

``` js
/*
* timer：一张图片移动到下一张图片的定时器id
* timer2：所有图片移动的定时器id
* flag：防止多次点击上/下图片按钮
* index：当前图片的下标
*/
buttons[0].className = "on";
var timer = null;
var timer2 = null;
var flag = true;
var index = 0;
```

定义一个用来处理图片移动的函数moveImg：

**前提：目前一共7张图【5，1，2，3，4，5，1】，对应的style.left值为【0，-1226，-2452，-3678，-4904，-6130，-7356】。**

**思路：一张图片让其在400ms内移动到下一张，每次花10ms移动一次，变换图片的style.left值，（在肉眼观察下，感觉是连续移动的图像）。if里加边界值控制，注意正负边界，当移动完成时，需判断newPosition值的边界，如果值为0（即7张图中的第一张）时，需要将图片的style.left值变为第六张图的值（-6130），如果值为-7356（即7张图中的最后一张）时，需要将图片的style.left值变为第二张图的值（-1226）。目的是防止越出边界，在第二张跟第六张图时，可以左右滑动，起到无线滚动的感觉。**

``` js
/*
* distance：移动距离
* time：移动所需总时间
* eachTime：每次移动所需时间
* newPosition：移动后的新位置
*/
function moveImg(distance) {
  var time = 400;
  var eachTime = 10;
  var eachDistance = distance/(time/eachTime);
  var newPosition = parseInt(list.style.left) + distance;
  flag = false;

  function eachMove() {
    if (distance > 0 && parseInt(list.style.left) < newPosition || distance  < 0 && parseInt(list.style.left) > newPosition) {
      list.style.left = parseInt(list.style.left) + eachDistance + 'px';
    } else {
      flag = true;
      clearInterval(timer);
      list.style.left = newPosition + 'px';
      if (newPosition == 0) {
        list.style.left = -6130 + 'px';
      }
      if (newPosition == -7356) {
        list.style.left = -1226 + 'px';
      }
    }
  }
  timer = setInterval(eachMove, 10);
}
```

对下一张图标绑定点击事件：

``` js
// 下一张图标绑定点击事件，临界值控制（最后的小圆点值为4，大于4时，需要回到第一张）
// 如果当前处于图片滑动中（即flag为false）则跳出函数，不执行。
next.onclick = function() {
  if (!flag) return;
  moveImg(-1226);
  if (index == 4) {
    index = 0;
  } else {
    index++;
  }
  buttonsShow();
}
```

对上一张图标绑定点击事件：

``` js
// 上一张图标绑定点击事件，临界值控制（最前的小圆点值为0，小于0时，需要回到最后一张）
// 如果当前处于图片滑动中（即flag为false）则跳出函数，不执行。
prev.onclick = function() {
  if (!flag) return;
  moveImg(1226);
  if(index == 0) {
    index = 4;
  } else {
    index--;
  }
  buttonsShow();
}
```

控制小圆点样式显示函数：

``` js
function buttonsShow() {
  for (let i = 0; i < buttons.length; i++) {
    if (buttons[i].className == "on") {
      buttons[i].className = "";
      break;
    }
  }
  buttons[index].className = "on";
}
```

对小圆点绑定点击事件：

``` js
// 小圆点绑定点击事件，给每个圆点的下标值赋值，使得每个圆点对应一张图片，
// this.index代表当前点击圆点下标，点击某个小圆点时，比较与当前圆点下标index，
// 差值*图片的宽度（-1226）即为需要移动的距离。
for (let i = 0; i < buttons.length; i++) {
  buttons[i].vaule = i;
  buttons[i].onclick = function() {
    if(this.vaule == index) return;
    var offset = (this.vaule - index) * -1226;
    moveImg(offset);
    index = this.vaule;
    buttonsShow();
  }
}
```

最后设置一个定时器的函数，实现图片轮播：

``` js
clearInterval(timer2);
timer2 = setInterval(next.onclick, 3000);

// 鼠标引入，清除定时器，轮播图停止
container.onmouseover = function() {
  clearInterval(timer2);
}

//鼠标移出，重新调用定时器，轮播图开始
container.onmouseout = function() {
  clearInterval(timer2);
  timer2 = setInterval(next.onclick,3000);
}
```

在调用定时器的时候，用的使setInterval，如果要使用setTimerout也是可以的；

在调用定时器要先清除定时器，（防止定时器一直叠加，使得轮播速度越来越快）
当鼠标移入时，轮播图停止，即定时器被清除，当鼠标移出时，定时器重新被调用。

## 总结

- 提前设置配置项:
  - 图片（长宽，路径等）
  - 定时器的时间（timer，timer2）
  - 临界值设置为小圆点的个数（buttons.length）
  - 每次移动图片的时间和距离（time，eachTime，eachDistance）
> 有了配置之后，通过js来添加dom节点，不用去每次修改了图片去修改html代码。只需要修改配置就行。