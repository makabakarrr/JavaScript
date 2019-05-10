<h1 style='text-Align: center;'>关于this</h1>





##  1.this 的指向



> a.普通函数里的this，函数调用后指向window
> b.对象方法里面的this，指向调用的对象
> c.事件处理程序 中的this指向注册这个事件的元素对象。
```javascript
function box(){
    console.log(this);
}
box();//window，其实是window.box(),window省略
```

```javascript
document.onclick = function(){
	console.log(this);
	box();//这里的box中是一个调用，运行时还是在window环境下运行 输出window
};
function box(){
	console.log(this);
}
```
```javascript
var obj = {
    run : function(){
        console.log(this);
    }
}
obj.run();//obj
```

```javascript
var name="TT";
var person={
    name:"tt",
    showName:function(){
    console.log(this.name);
    }
}
person.showName();  //输出  tt
//这里是person对象调用showName方法，很显然this关键字是指向person对象的，所以会输出name

var showNameA=person.showName;
showNameA();    //输出TT 是window对象在调用showNameA()方法，window的name属性值为TT
```

```javascript
var personA={
    name:"tom",
    showName:function(){
    	console.log(this.name);
    }
}
var personB={
    name:"TOM",
    sayName:personA.showName
}
    
personB.sayName();  //输出 TOM
    //虽然showName方法是在personA这个对象中定义，但是调用的时候却是在personB这个对象中调用，因此this对象指向
```

```javascript
var a=11
function test1(){
  this.a=22;
  var b=function(){
    console.log(this.a);
  };
  b();
}
var x=new test1();//11 第一个this指向x 第二个this指向window
```

```javascript
var a=11
function test1(){
  this.a=22;
  var b=function(){
    console.log(this.a);
  };
  b();
}
test1()//22 此时this指向window，this.a=22是对window的a属性重新赋值为22
```

```javascript
function  Person(name){
    this.name=name;
}
var personA=Person("xl");   
console.log(personA.name); // 输出undefined 因为函数没有返回值 this指向window
console.log(window.name);//输出xl 没有使用new，等于 var personA=window.Person('xl');this指向window
```



## 2.this指向的改变

**a.call()/apply()**

​	使用方法：	Obj.method.apply(AnotherObj,arguments);//参数是一个数组

​							Obj.method.call(AnotherObj,argument,argument,argument...);//参数单个罗列

​							AnotherObj调用Obj的method方法，传入的参数为。。。

```javascript
var name="XL";
var Person={
    name:"xl",
    showName:function(){
        console.log(this.name);
    }
}
Person.showName();//输出xl this指向Person对象
Person.showName.call(); //输出 "XL" call()里第一个参数为空，默认指向window this指向window
```
> call()/apply()里第一个参数为空，默认指向window
>
> 如果把call和apply放在匿名函数后面改变this指向，匿名函数会立即执行

**b.bind()**

```javascript
var name="XL";
var Person={
    name:"xl",
    showName:function(){
        console.log(this.name);
    }
}
var getName = Person.showName;
getName();//XL
var getName = Person.showName.bind(Person);
getName();//xl
```

> 所有的匿名函数都可以使用bind()来改变this的指向







