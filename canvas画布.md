# canvas画布

## 1.基本用法

> 使用canvas元素，必须设置其width和height属性，指定画布的大小

如果不给 `<canvas>` 设置 `widht、height` 属性时，则默认 `width`为300、`height` 为 150，单位都是 `px`。

```html
//方法一：
<canvas id = 'canvas' width = '200' height = '200'></canvas>
```

```
//方法二：使用canvasAPI
canvas.width = 200;
canvas.height = 200;
```

如果是通过样式来设置canvas的宽高，实际上是在canvas的默认宽高上进行拉伸，会产生变形（相当于把一张300*150的图片进行拉伸）

> 要在这块画布上绘图，需要取得绘图上下文的对象

```html
<canvas id = 'canvas' width = '200' height = '200'>
	中间的内容为后备信息，如果浏览器不支持canvas，就会显示这些信息。
</canvas>
<script>
	var canvas = document.getElementById('canvas');
    var ctx = canvas.getContext('2d');//调用getContext方法并传入上下文的名字，传入‘2d’取得2D上下文
</script>
```

## 2. 2D

使用2d绘图上下文提供的方法，可以绘制简单的2D图形（矩形、弧形、路径、字符、添加图像）。2d上下文的原点点坐标是（0,0），位于canvas元素的左上角，x值越大表示越靠右，y越大表示越靠下。

### a. 填充和描边

填充和描边分别取决于fillStyle和strokeStyle属性，这两个属性的值可以是字符串、渐变对象、模式对象，默认值都为“#000000”。

fillStyle属性：设置填充样式

strokeStyle属性：描边样式

​		线条样式：

| lineWidth  | 线条的宽度 （属性值为数值）                                 |
| ---------- | ----------------------------------------------------------- |
| linecap    | 线条的结束端点样式（属性值：butt(默认)    round    square） |
| lineJoin   | 两个线条相交拐角的类型（属性值：miter(默认) round  bevel）  |
| miterLimit | 最大斜接长度（属性值为正数）                                |

### b.矩形

填充矩形：fillRect( 矩形左上角的x坐标，矩形左上角的y坐标，矩形的宽度，矩形的高度 )，以fillStyle的样式填充矩形

绘制矩形：strokeRect( 矩形左上角的x坐标，矩形左上角的y坐标，矩形的宽度，矩形的高度 )，以strokeStyle的样式对矩形描边，无填充

清除矩形区域： clearRect( 矩形左上角的x坐标，矩形左上角的y坐标，矩形的宽度，矩形的高度 )，清除画布上的矩形区域

创建矩形： rect( 矩形左上角的x坐标，矩形左上角的y坐标，矩形的宽度，矩形的高度 )

### c.路径

| fill()                                    | 填充当前绘图（路径）。                                       |
| ----------------------------------------- | ------------------------------------------------------------ |
| stroke()                                  | 绘制已定义的路径。                                           |
| beginPath()                               | 起始一条路径，或重置当前路径。                               |
| moveTo()                                  | 把路径移动到画布中的指定点，不创建线条。                     |
| closePath()                               | 创建从当前点回到起始点的路径。                               |
| lineTo()                                  | 添加一个新点，然后在画布中创建从该点到最后指定点的线条。     |
| clip()                                    | 从原始画布剪切任意形状和尺寸的区域。                         |
| quadraticCurveTo()                        | 创建二次贝塞尔曲线。                                         |
| bezierCurveTo()                           | 创建三次贝塞尔曲线。                                         |
| arc(x,y,r,起始角度，终止角度，是否逆时针) | 创建弧/曲线（用于创建圆形或部分圆）。                        |
| arcTo(x1,y1,x2,y2,r)                      | 创建两切线之间的弧/曲线。x1,x2为切线交点的坐标，x2,y2为第二条切线上的点坐标 |
| isPointInPath()                           | 如果指定的点位于当前路径中，则返回 true，否则返回 false。    |



### d.文本

绘制文本主要有两个方法：fillText()、strokeText()

这两个方法都可以接受4个参数：文本字符串、x坐标、y坐标和可选的最大像素宽度

这两个方法都已下列3个属性为基础：

font：表示文本样式、大小以及字体

textAlign：表示文本对齐方式 “start/end/center/left/right”

textBaseline：表示文本的基线  值"top/hanging/middle/alphabetic/ideographic/bottom"



### e.图像

drawImage(img,x,y,w,h)：img使用的图片，x放置图片的位置的横坐标，y放置图片位置的纵坐标，w规定图像的宽度，h规定图像的高度

