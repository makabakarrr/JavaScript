<h1 style='text-Align: center;'>DOM运动</h1>

运动的实质是改变元素的样式（属性值为数值），改变位置需要定位，通过改变left/top来改变位置

## 1.匀速左右运动

使用定时器每隔一段时间给元素的left/top增加或减少固定值，实现匀速运动。

以下为点击button实现box<span style='color: red'>匀速向右运动</span>的部分代码。

```javascript
#box{
    width:  100px;
    height: 100px;
    background: red;
    position: absolute;
    top: 100px;
    left: 0px;
}
<button id="fly">向右飞</button>
<div id="box"></div>

fly.onclick = function(){
	uniform(box,500,10);
}
function uniform(ele,distance,time){
   var timer = setInterval(function(){
       ele.style.left = ele.offsetLeft + 5 + "px";
        if(ele.offsetLeft == distance){
            clearInterval(timer);
        }
    },time);
}
```

会产生以下问题：
		1.连续点击button会加速运动

​		2.第一次点击运动结束后再次点击会继续持续运动

​		3.如果distance不是5的倍数，运动无法停止

**解决问题1：**

产生问题1的原因是连续点击button造成定时器累加，所以要在每次点击button进去uniform函数后，清除上一个定时器。修改代码为：

```javascript
var timer = null;
function uniform(ele,distance,time){
    clearInterval(timer);//清除定时器，避免连续点击造成的定时器累加
   timer = setInterval(function(){
       ele.style.left = ele.offsetLeft + 5 + "px";
        if(ele.offsetLeft == distance){
            clearInterval(timer);
        }
    },time);
}
```

**解决问题2：**

产生问题2的原因是当第一次点击运动结束时，ele.offsetLeft = distance + 5；在第二次点击后，程序不会进入if语句，不会清除定时器就会一直运动下去。所以函数内部应该先判断ele.offsetLeft是否等于distance，相等则清除定时器，不相等则继续运动。修改代码为：

```javascript
var timer = null;
function uniform(ele,distance,time){
    clearInterval(timer);//清除定时器，避免连续点击造成的定时器累加
   timer = setInterval(function(){
        if(ele.offsetLeft == distance){
            clearInterval(timer);
        }else{
            ele.style.left = ele.offsetLeft + 5 + "px";
        }
    },time);
}
```

**解决问题3：**

因为distance不是5的倍数，ele.offsetLeft +5 +5+5...永远不会等于distance，无法清除定时器停止运动。在运动至最后接近于distance的时候，distance-ele.offsetLeft永远会小于5，在这个时候，可以直接将ele.style.left设置到distance。修改代码为：

```javascript
var timer = null;
function uniform(ele,distance,time){
    clearInterval(timer);//清除定时器，避免连续点击造成的定时器累加
   timer = setInterval(function(){
        if(distance - ele.offsetLeft < 5){
            ele.style.left = distance + 'px';
            clearInterval(timer);
        }else{
            ele.style.left = ele.offsetLeft + 5 + "px";
        }
    },time);
}
```

接下来，实现左右运动。如果是往左运动，那么在运动开始前distance一定是大于ele.offsetLeft，使用每次的ele.offsetLeft + 5完成运动；如果是往右运动，运动开始前distance是小于ele.offsetLeft的，使用每次的ele.offsetLeft - 5完成运动。所以在程序开头可以判断两者差的正负来决定使用+5还是-5。

```javascript
var timer = null;
function uniform(ele,distance,time){
    clearInterval(timer);
    timer = setInterval(function(){
        var speed = distance - ele.offsetLeft > 0 ? 5 : -5;
        if(Math.abs(distance - ele.offsetLeft) < 5){//①
            ele.style.left = distance + "px";
            clearInterval(timer);
        }else{
            ele.style.left = ele.offsetLeft + speed + "px";
        }	
    },time);
}
```

[^标注①]: 在distance不是5的倍数的前提下，如果向右运动，最接近distance的最后一步distance - ele.offsetLeft的值可以是[0,1,2,3,4]；如果是向左运动，最接近distance的最后一步distance - ele.offsetLeft的值可以是[0,-1，-2，-3，-4]；

## 2.缓冲运动

缓冲运动是速度由快到慢到0的运动。缓冲运动的原理： 当前速度 = （目标距离 - 当前距离）/缩放比例。代码如下。

```javascript
function buffer(ele,target,speedTime){
	clearInterval(timer);
	timer = setInterval(function(){
        var speed = (target - ele.offsetLeft)/10;
        if(ele.offsetLeft == target){
            clearInterval(timer);
        }else{
            ele.style.left = ele.offsetLeft + speed + "px";
        }
    },speedTime);
}
```

产生的问题：
		无法准确到达目标值并清除定时器。

**解决办法**

```javascript
function aimate(ele,target,speedTime){
			clearInterval(timer);
			timer = setInterval(function(){
				//缓冲运动的在于speed的变化 ，由快到慢
				//var speed = target - ele.offsetLeft > 0 ? 5 : -5;
				//因为offsetLeft会把小数直接去掉,所以永远达不到目标值
				var speed = (target - ele.offsetLeft)/10;
				//当speed为负数的小数时，会直接把小数去掉 -0
				speed = speed > 0 ? Math.ceil(speed) : Math.floor(speed);
				if(ele.offsetLeft == target){
					clearInterval(timer);
				}else{
					ele.style.left = ele.offsetLeft + speed + "px";
				}	
			},speedTime);
		}
```

## 3.改变任意属性

传入想要改变的任意属性，实现运动。首先要获取属性改变前的属性值，如果是行内样式设置的属性，可以通过元素.style.属性，但是传入的属性名是一个字符串，应该这样表示：元素.style[属性]；如果是不是行内样式，这种方式不能获取。需要通过window.getComputedStyle(元素,null)[属性]方法来获取，这个方法可以获得浏览器渲染页面后对元素属性计算后的值。

```javascript
var timer = null;
function animate(ele,attr,target,speedTime){
			clearInterval(timer);
			timer = setInterval(function(){
				var current = 0;
				if(attr == "opacity"){
					//将透明度扩大100倍来操作,传入的opacity是乘以100的值
					current = getStyle(ele,attr) * 100;
				}else{
					current = parseInt(getStyle(ele,attr))
				}
				var speed = (target - current)/10;
				speed = speed > 0 ? Math.ceil(speed) : Math.floor(speed);
				if(current == target){
					clearInterval(ele.timer);
				}else{
					if(attr == "opacity"){
						ele.style.opacity = (current + speed)/100;
					}else if(attr == "zIndex"){
						ele.style.zIndex = target;
                    }else{
						ele.style[attr] = current + speed + "px";
					}
				}	
			},speedTime);
		}
```

## 4.多物体运动

多个物体一起运动的时候，使用以上的运动代码，在一个物体运动中如果触发了另一个物体的运动，上一个物体的运动会停止。因为timer定时器是全局变量，触发了下一个定时器会把timer清除，所以会停止运动。

解决方法：把timer私有化。

```javascript
function animate(ele,attr,target,speedTime){
    //将定时器以对象属性的方法保存在当前元素对象下
    clearInterval(ele.timer);//当其它元素也来执行这个运动时，这里的清除定时器的操作就不会互相影响
	ele.timer = setInterval(function(){
        var current = 0;
        if(attr == "opacity"){
            current = getStyle(ele,attr) * 100;
        }else{
            current = parseInt(getStyle(ele,attr))
        }
        var speed = (target - current)/10;
        speed = speed > 0 ? Math.ceil(speed) : Math.floor(speed);
        if(current == target){
            clearInterval(ele.timer);
        }else{
            if(attr == "opacity"){
                ele.style.opacity = (current + speed)/100;
            }else if(attr == "zIndex"){
                ele.style.zIndex = target;
            }else{
                ele.style[attr] = current + speed + "px";
            }
        }	
    },speedTime);
    
}
function getStyle(ele,attr){
	return window.getComputedStyle ? window.getComputedStyle(ele,null)[attr] : ele.currentStyle[attr];
}
```

## 5. 同时改变多属性的运动

创建一个对象，放置多个属性和对应的目标值。在函数内部再遍历这个对象，分别使它们到达目标值。代码如下：

```javascript
function animate(ele,params,speedTime){
    //将定时器以对象属性的方法保存在当前元素对象下
    clearInterval(ele.timer);//当其它元素也来执行这个运动时，这里的清除定时器的操作就不会互相影响
	ele.timer = setInterval(function(){
        for（var attr in params）{
            var current = 0;
            if(attr == "opacity"){
                current = getStyle(ele,attr) * 100;
            }else{
                current = parseInt(getStyle(ele,attr))
            }
            var speed = (params - current)/10;
            speed = speed > 0 ? Math.ceil(speed) : Math.floor(speed);
            if(current == params){
                clearInterval(ele.timer);
            }else{
                if(attr == "opacity"){
                    ele.style.opacity = (current + speed)/100;
                }else if(attr == "zIndex"){
                    ele.style.zIndex = target;
                }else{
                    ele.style[attr] = current + speed + "px";
                }
            }	
        }
        
    },speedTime);
}
```

产生的问题：

​		在设置了多个属性和对应的目标值时，只要有属性到达，那么就会清楚定时器，停止运动。然而，不一定能使所有属性一起到达，就会产生有的属性没有 到达的问题。

**解决方法**

​		保证所有的属性都到达之后在清楚定时器。使用一个控制开关，首先在定时器内定义一个开关，并将开关置为true，在遍历对象的时候，判断当前值是否等于目标值，如果不等于，说明没有到达目标值，将flag置为false。最后判断flag是否为true，再清除定时器。代码如下：

```javascript
function animate(ele,params,speedTime){
    //将定时器以对象属性的方法保存在当前元素对象下
    clearInterval(ele.timer);//当其它元素也来执行这个运动时，这里的清除定时器的操作就不会互相影响
	ele.timer = setInterval(function(){
        var flag = true;
        for（var attr in params）{
            var current = 0;
            if(attr == "opacity"){
                current = getStyle(ele,attr) * 100;
            }else{
                current = parseInt(getStyle(ele,attr))
            }
            var speed = (params[attr] - current)/10;
			speed = speed > 0 ? Math.ceil(speed) : Math.floor(speed);
            if(current != params[attr]){
                //这里是属性没有到达目标值时执行
                //将flag设置为false
                flag = false;//表示有属性没有到达目标值
            }
            if(attr == "opacity"){
                ele.style.opacity = (current + speed)/100;
            }else{
                ele.style[attr] = current + speed + "px";
            }
        }
        if(flag){//所有的属性都到达目标值
            clearInterval(ele.timer);
        }
    },speedTime);
}
```

## 6.链式运动

链式运动是在元素的当前运动执行完毕之后再开启下一个运动。因为无法确定详细的结束时间，所以不能使用setTimeout来调用animate函数。想要在运动执行完毕之后开启下一个运动，应该在前一个运动清除定时器的时候同时开启下一个运动。可以在调用animate时传入一个回调函数，在清除定时器的时候调用。但是要注意没有传入回调函数的情况，这个时候说明不需要再开启下一个运动了。代码如下：

```javascript
function animate(ele,params,speedTime,callBack){
    clearInterval（ele.timer）;
    ele.timer = setInterval(function(){
        var flag = true;
        for(var attr in params){
			var current = 0;
            if(attr = "opacity"){
			    current = getStyle(ele,attr) * 100;
            }else{
                current = parseInt(getStyle(ele,attr))
            }
            var speed = (params[attr] - current)/10;
			speed = speed > 0 ? Math.ceil(speed) : Math.floor(speed);
            if(current != params[attr]){
                flag = false;
            }
            if(attr == "opacity"){
                ele.style.opacity = (current + speed)/100;
            }else{
                ele.style[attr] = current + speed + "px";
            }
        }
        if(flag){//所有的属性都到达目标值
            clearInterval(ele.timer);
            if(callBack){//function的布尔值为true
				callBack();//如果有回调函数就调用
            }
        }
    },speedTime)
}
```

## 7. 反弹运动

下列为小球在浏览器窗口中的反弹运动部分代码。

```javascript
setInterval(function(){
    ball.style.left = ball.offsetLeft + speedLeft + "px";
    ball.style.top = ball.offsetTop - speedTop + "px";

    var maxL = (document.documentElement.clientWidth || document.body.clientWidth) - ball.offsetWidth;
    var maxT = (document.documentElement.clientHeight || document.body.clientHeight) - ball.offsetHeight;

    if(ball.offsetTop <= 0 || ball.offsetTop >= maxT){//到达临界点，反向
        speedTop *= -1;
    }
    if(ball.offsetLeft <= 0 || ball.offsetLeft >= maxL ){//到达临界点，反向
        speedLeft *= -1;
    }
},10);
```

## 8.自由落体运动

```javascript
var timer = setInterval(function(){
    ball.style.left = ball.offsetLeft + speedLeft + "px";
    ball.style.top = ball.offsetTop - speedTop + "px";
    var maxT = (document.documentElement.clientHeight || document.body.clientHeight) - ball.offsetHeight;
    if(ball.offsetTop >= maxT){//最低点
        ball.style.top = maxT + "px";
        speedTop *= -0.8;//反向
        speedLeft--;//减少横向的步长
        if(speedLeft < 0){
            speedLeft = 0;
            setTimeout(function(){
                clearInterval(timer);
            },100);
        }
    }
    speedTop--;
},20);
```

## 9.抛物线运动

抛物线的运动函数为y = a*x*x + b*x + c，根据三个点可以确定a,b,c的值。

```javascript
//起始点
var startPoint = {
    x : addToCart.offsetLeft + addToCart.offsetWidth/2,
    y : addToCart.offsetTop
}
//终点		
var endPoint = {
    x : shopCart.offsetLeft + shopCart.offsetWidth/2,
    y : shopCart.offsetTop
}
//顶点
var topPoint = {
    x : endPoint.x - 150,
    y : endPoint.y - 150
}

var a = ((startPoint.y - endPoint.y) * (startPoint.x - topPoint.x) - (startPoint.y - topPoint.y) * (startPoint.x - endPoint.x)) / ((startPoint.x * startPoint.x - endPoint.x * endPoint.x) * (startPoint.x - topPoint.x)-(startPoint.x * startPoint.x - topPoint.x * topPoint.x) * (startPoint.x - endPoint.x));  
			
var b = ((endPoint.y - startPoint.y) - a * (endPoint.x * endPoint.x - startPoint.x * startPoint.x)) / (endPoint.x - startPoint.x);  
			
var c = startPoint.y - a * startPoint.x * startPoint.x - b * startPoint.x;
//把小球放到起始点
ball.style.left = this.offsetLeft + this.offsetWidth/2 + "px";
ball.style.top = this.offsetTop + "px";

var x = ball.offsetLeft;//原点的横坐标为小球的初始left
var y = 0;
//y = a*x*x + b*x + c;
var timer = setInterval(function(){
    x += 5;
    y = a*x*x + b*x + c;

    ball.style.left =  + x + "px";
    ball.style.top = y + "px";

    if(x >= endPoint.x){
        clearInterval(timer);
        document.body.removeChild(ball);

        shopNum.innerHTML = ++num;
    }

},10);
```







