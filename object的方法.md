# object的方法

## 1.Object.create(一定要有参数)

​		创建一个空对象，并把该对象的[[Prototype]]链接到指定的参数。ES5的方法

```JavaScript
var obj1 = Object.create(null);//创建一个空对象，没有原型链接
var obj2 = {};//创建一个空对象，有原型链接
```



## 2.Object.setPrototypeOf(x,y) ES6

用来设置一个对象的原型。es6在PC端兼容性不好

## 3.Object.getPrototypeOf(x) ES6

用来读取一个对象的原型对象

```javascript
var obj1 = {a:1,b:2};
var obj2 = {c:3};

Object.setPrototypeOf(obj1,obj2);//obj1的原型设为obj2
obj2.c = 5;
console.log(obj1.c);//5
```

## 4.Object.defineProperty()

该方法会直接在一个对象上定义一个新属性或者修改一个对象的现有属性，并返回这个对象。

```javascript
var obj = {
    name: 'xiaocuo',
    age: 18,
    say: function () {
        console.log('嗨，大家好');
    }
};
console.log(obj.age); //18
Object.defineProperty(obj,"age",{
    // 是否可配置(枚举、可写、删除)为false不会影响write和 enumerable
    configurable: true,
    // 是否可枚举属性（遍历属性）
    enumerable: true,
    // 是否可写，不可写是在外面不可修改
    writable: true,
    value: 19
});
console.log(obj.age); // 19
for(var str in obj){
    console.log(str); // name age say
}
delete obj.name; // 删除对象属性
console.log(obj); // {age: 19, say...}
```

## 5.访问器方法

set/get不仅可以使用defineProperty方法来设置<span style='color:red'>使用defineProperty设置get和set方法使，不允许设置writabe和value</span>，还可以直接通过对象来设置。仅有set没有get说明该属性只可写不可读，仅有get没有set说明只可读不可写。

set：一个给属性提供setter的方法（给对象属性设置值时候触发的函数）,设置值时将给如的值存储在一个中间介质变量中

get：一个给属性提供getter的方法（访问对象属性时触发的函数，返回值是当前属性的置），实质是将这个中间介质变量返回出去

```javascript
var obj = {
    _a: 5,
    set a(value){
        this._a = value;// this._a 就是一个临时存储数据的中间介质变量
    },//a(value)....相当于a: function(value)...
    get a(){
        return this._a;
    }
};
console.log(obj.a);//5
```

## 6.Object.getOwnPropertyDescriptor(object,string)

改方法返回指定对象里一个自由属性的属性描述对象

```javascript
var obj = {
    name: 'xiaocuo',
    age: 18
}
var propObj = Object.getOwnPropertyDescriptor(obj, 'name');
console.log(propObj);//
//{value: "xiaocuo", writable: true, enumerable: true, //configurable: true}
```

<span style='color:red'>拓展：显式声明变量与隐式声明变量的区别</span>

1.用var声明的变量是当前作用域的局部变量，隐式声明的变量为全局变量

2.显式声明的变量不可以使用delete删除，隐式的可以。如在全局作用于下，变量都是window的属性，可以通过object。getOwnPropertyDescriptor()方法来获取属性描述对象，查看configurable属性是否为true，则可以判断能否通过delete删除

```javascript
var i = 1;//显式声明变量
b = 2;//隐式声明
var propObjI = Object.getOwnPropertyDescriptor(window, 'i');
var propObjB = Object.getOwnPropertyDescriptor(window, 'b');
console.log(propObjI);//{value: 1, writable: true, enumerable: true, configurable: false}
console.log(propObjB);//{value: 2, writable: true, enumerable: true, configurable: true}
```

## 7.Object.defineProperties(obj1,obj2)

改方法直接在一个对象上定义或修改一个或多个新的属性，并返回该对象。

```javascript
var obj = {name: 'xm'};
Object.defineProperties(obj, {
    age: {
        value: 20,
        configurable: true,
        writable: true,
        enumerable: true
    },
    sex: {
        value: '男',
        configurable: true,
        writable: true,
        enumerable: true
    }
})
```

## 8.**Object. getOwnPropertyDescriptors()**

获取指定对象的所有自身属性的描述符，如果没有任何自身属性，则返回空对象。

语法: Object.getOwnPropertyDescriptors(obj)

## 9.Object.keys()

该方法返回一个数组，成员是参数对象自身（不包含继承）的所有可遍历的属性的键名

```javascript
var obj = { foo: 'abc', baz: 123 };
console.log(Object.keys(obj)); //[foo,baz]
```

## 10.**Object.values()**

方法返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键值。

```javascript  var obj = { foo: 'abc', baz: 123 };
var obj = { foo: 'abc', baz: 123 };
console.log(Object.values(obj));//['abc',123]
```

## 11.Object.assign()

方法用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target）。Object.assign(target,source, ...)方法的第一个参数是目标对象，后面的参数都是源对象。**注意：**如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性。

此方法使浅拷贝，智能拷贝第一层，复制的是地址。深拷贝的方法为：

```javascript
var obj1 = {a: 1, b: 2, c: {d: 4, e: 5}};
var obj2 = JSON.parse(JSON.stringify(obj1));
console.log(obj1.c === obj2.c);
```

## 12.Object.is()

方法用来比较两个值是否严格相等，与严格比较运算符（===）的行为基本一致。严格比较运算符比较的是数据类型和地址

不同之处只有两个：一是+0不等于-0，二是NaN等于自身。

```javascript
Object.if({},{});//false

Object.is(+0,-0);//false +0 === -0为true
Object.is(NaN,NaN);//true  NaN === NaN为false
```

