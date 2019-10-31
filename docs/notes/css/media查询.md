## 设置Meta标签

首先我们在使用Media的时候需要先设置下面这段代码，来兼容移动设备的展示效果：
``` html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
```
这段代码的几个参数解释：
- width = device-width：宽度等于当前设备的宽度
- initial-scale：初始的缩放比例（默认设置为1.0）  
- minimum-scale：允许用户缩放到的最小比例（默认设置为1.0）    
- maximum-scale：允许用户缩放到的最大比例（默认设置为1.0）   
- user-scalable：用户是否可以手动缩放（默认设置为no，因为我们不希望用户放大缩小页面） 


## CSS3 Media

上面我们大概讲了下CSS2的媒体查询用法，现在我们重新回到CSS3的媒体查询，在第一段代码上面我用的是小于960px的尺寸的写法，那现在我们来实现等于960px尺寸的代码：
``` css
@media screen and (max-device-width:960px){
  body{
        background:red;
    }
}
```

然后就是当浏览器尺寸大于960px时候的代码了：
``` css
@media screen and (min-width:960px){
  body{
        background:orange;
    }
}
```

我们还可以混合使用上面的用法：
``` css
@media screen and (min-width:960px) and (max-width:1200px){
    body{
        background:yellow;
    }
}
```

上面的这段代码的意思是当页面宽度大于960px小于1200px的时候执行下面的CSS。

## Media所有参数汇总

以上就是我们最常需要用到的媒体查询器的三个特性，大于，等于，小于的写法。媒体查询器的全部功能肯定不止这三个功能，下面是我总结的它的一些参数用法解释：
- width:浏览器可视宽度。
- height:浏览器可视高度。
- device-width:设备屏幕的宽度。
- device-height:设备屏幕的高度。
- orientation:检测设备目前处于横向还是纵向状态。
- aspect-ratio:检测浏览器可视宽度和高度的比例。(例如：aspect-ratio:16/9)
- device-aspect-ratio:检测设备的宽度和高度的比例。
- color:检测颜色的位数。（例如：min-color:32就会检测设备是否拥有32位颜色）
- color-index:检查设备颜色索引表中的颜色，他的值不能是负数。
- monochrome:检测单色楨缓冲区域中的每个像素的位数。（这个太高级，估计咱很少会用的到）
- resolution:检测屏幕或打印机的分辨率。(例如：min-resolution:300dpi或min-resolution:118dpcm)。
- grid：检测输出的设备是网格的还是位图设备。