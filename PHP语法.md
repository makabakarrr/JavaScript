# PHP语法

PHP：（Hypertext Preprocessor）超文本预处理器，是一种创建动态交互性站点的强有力的<span style="color:red">服务器端</span>脚本语言（弱类型语言）；是一种被广泛使用的开源脚本语言；文件能够包含文本、HTML、css、js以及PHP代码；

<p style="color:red;font-weight:bold">
    PHP代码在服务器上执行，输出结果一纯文本返回到浏览器页面<br/>
    PHP语句必须以‘;’结束。
</p>

## 1 数据类型

**四种标量类型：**

1. 布尔型（boolean）
2. 整型（integer）
3. 浮点型（float）
4. 字符串（string）

**两种符合类型**

1. 数组（Array）
2. 对象（Object）

**两种特殊类型**

1. null
2. 资源（Resource）

## 2 变量

变量是用于存储数据的容器。PHP没有声明变量的命令，变量在第一次复制时候被创建。

PHP变量规则：

1. 变量以$开始，后面跟着变量的名称。
2. 变量名必须以字母或者下划线开始。
3. 变量名只能包含字母数字字符以及下划线（A-Za-z、0-9和_）。
4. 变量名不能包含空格。
5. 变量名是区分大小写的。（PHP对大小写敏感）

## 3 变量作用域

变量的作用域是脚本中变量可以被引用/使用的部分。PHP有四种不同的作用域：

1. local
2. global
3. static
4. parameter

#### a.全局作用域

在所有函数外部定义的变量，拥有全局作用域，作用于从定义处到文件结尾。<span style="color:red">函数内部不能访问全局变量，需要使用global关键字或者访问$GLOBALS数组。</span>PHP会将所有变量存储在$GLOBALS数组中，访问形式为$GLOBALS[index]，index为变量名称

```php
<?php
$x = 5;
$y = 10;
function test()
{
    //访问数组方式：
    //$GLOBALS['y'] = $GLOBALS['x'] + $GLOBALS['y'];
    
    //通过global关键字
    global $x,$y;
    $y = $x + $y;
    
}
test();
echo $y;//15
?>
```

#### b.局部作用域

在函数内部定义的变量是局部变量，只能在函数内部访问。

#### c.static作用域

当一个函数完成时，它的所有变量通常都会被删除。然而，有时候您希望某个局部变量不要被删除。

要做到这一点，请在第一次声明变量时使用 **static** 关键字，每次调用该函数时，该变量将会保留着函数前一次被调用时的值。代码如下：

```php
<?php
    function myTest()
    {
        static $x=0;
        echo $x;
        $x++;
        echo PHP_EOL;    // 换行符，只在HTML内容里换行，显示出来的只有一个空格字符
    }

    myTest();//0
    myTest();//1
    myTest();//2
?>
```

#### d.参数作用域

参数是通过调用代码将值传递给函数的局部变量。参数是在参数列表中声明的，作为函数声明的一部分。

## 4 PHP输出语句

### (1) echo

echo是一个语言结构，使用时可以不加括号，也可以加括号，如果输出多个值不能使用括号，多个值之间使用逗号隔开。echo使用来输出字符串的，在输出之前都会将参数转换为字符串类型

echo true输出1；echo false不输出任何内容

### (2) print

print是一个语言结构，使用时可以不加括号，只能输出一个值，速度比echo慢。print有返回值，输出成功返回1，失败返回0

### (3) print_r()

print_r()是函数，以更容易理解的形式打印变量

```php
<?php
	$arr1 = ['a','b','c']; 
	print_r($arr1);
	//Array([0] => a [1] => b [2] => c)
?>
```



### (4) var_dump()

var_dump显示关于一个或多个表达式的结构信息，包括表达式的类型与值，如下所示：

```php
<?php
$arr1 = ['a','b','c'];
$str1 = '好好学习，天天向上！';

var_dump($str1);//输出 string '好好学习，天天向上！' (length=30)一个中文站3个字符数
var_dump($arr1);
//输出 array (size=3)
 		0 => string 'a' (length=1)
        1 => string 'b' (length=1) 
            ...
?>
```

## 5 PHP字符串变量

PHP中字符串可以用三种方法定义：

1. 单引号形式
2. 双引号形式
3. heredoc结构形式

单引号与双引号的区别：当单引号中有变量时，变量会被当做字符串输出

​										  当双引号中右变量时，变量会被解析再与其他内容拼接在一起

```php
<?php
	$str1 = "你好！";
    $str2 = "再见！";
    echo "$str1 $str2";//你好！ 再见！
    echo "<br/>";
    echo $str1.$str2;//你好！再见！
    echo "<br/>";
    echo $str1." ".$str2;// 你好！ 再见！
?>
```

**heredoc使用方法**

首先以‘<<<标识符’开始，以‘标识符’结束，结束标识符必须顶头写，且结束后面必须有分号；该标识符不能出现在正文中；位于开始标记与结束标记之间的变量可以被解析，不需要使用并置连接符来连接，但是不能解析函数

**strlen()函数**

```php
<?php
	$str1 = "你好！";
	echo strlen($str1);//输出 9 字符串的长度
?>
```

**strpos函数**

在字符串内查找一个字符或一段文本，返回第一个匹配的位置，如果不存在，返回false

```php
<?php 
echo strpos("Hello world!","world"); //6
//echo false 不输出任何内容 echo true输出1
?>
```

## 6 PHP数组

数组是一个能在单个变量中存储多个值的特殊变量，PHP中有三种类型的数组：

#### a.数值数组

数值数组是带有数字ID键的数组，又称索引数组。创建方式：

> 1.自动分配ID键：

```php
$arr = array("a","b","c");$arr = array("a","b","c");
```

> 2.人工分配ID键：

```php
$arr[0] = "a";
$arr[1] = "b";
$arr[2] = "c";
```

**获取数组的长度**

使用count()函数，该函数返回数组的长度：

```php
<?php
	$arr = array("a","b","c");
	echo count($arr);
?>
```

**遍历数值数组**

使用for循环遍历：

```php
<?php
	$arr = array("a","b","c");
	$len = count($arr);
	
	for($i=0; $i<$len; $i++){
        echo $arr[$i];
    }
?>
```

#### b.关联数组















