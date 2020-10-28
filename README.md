# learnJs
# Some notes about Js

## 一、第一天

### **1**.关于 this 的一些思考 与感悟

 this这个 keyword非常的困惑,但是其实有一个好方法可以理解.

    1. 检查 ' . ' 左边是谁invoke 这个函数. 例如 xiaoming.age();  age函数里面有this, 然后 '. ' 旁边是xiaoming , 那么this就是指向xiaoming了.这种叫做 Implicit Binding.
    
    2. 如果点旁边没有,那就检查有没有用到 bind, apply, call 这三种, 有的话就是调用此方法的对象. 这种叫做 explicit binding.
    
    3. 如果上面两个都没有就检查代码里面有没有用到new 这个keyword, 有的话那就是指向new旁边的函数对象. 这种叫做new binding
    
    4. 上面三个都没有, 检查是不是有arrow function, 有arrow function的话就是, 那么指向是arrow function的lexical binding 的对象. 就是她的parent. 这种叫做 lexical binding
    
    5. 全部都没有如果不是strict mode那就是window对象了.. strict就是 error (undefined).
### ***2***.闭包

  1.当闭包 返回的函数中含有循环变量或者 会变化的变量时：

```
function count() {
        var arr = [];
        for (var i=1; i<=3; i++) {
            arr.push(function () {
                return i * i;
            });
        }
        return arr;
    }
    var results = count();
    var f1 = results[0];
    var f2 = results[1];
    var f3 = results[2];
```

  理解：

```
首先我们弄懂上面代码的运行流程：首先var results = count();之后，函数count已经被调用了，所以一次执行函数内的各段代码：var arr = [];，for (var i=1; i<=3; i++)，这个for循环尤其值得注意。

​	因为此时循环体执行了push方法，将一个个函数function () { return i * i;}添加到数组内，但是这个函数并没有被调用，还只是一个变量，所以for循环依次执行，直到i = 4。

​	因为闭包，内部函数function () { return i * i;}引用的i就是外部变量，for循环中的i = 4。所以，之后数组arr内的函数的i都是4。
​	调用函数count后，变量results已经是数组arr了。

​	数组里面元素依次是function f1() { return i * i;} function f2() { return i * i;} function f3() { return i * i;}。

​	但是三个函数都没有被调用，直到var f1 = results[0];，此时function f1() { return i * i;}开始执行，如上段所写，此时的i = 4，所以，返回值就是16了。
```

## 二、第二天

### 1.在Js的世界里，一切皆为对象，但是某些对象依然不太一样

```
typeof 123; // 'number'
typeof NaN; // 'number'
typeof 'str'; // 'string'
typeof true; // 'boolean'
typeof undefined; // 'undefined'
typeof Math.abs; // 'function'
typeof null; // 'object'
typeof []; // 'object'
typeof {}; // 'object'
```

### 2.`null`和`undefined`没有toString()方法，这两个特殊值要除外，虽然`null`还伪装成了`object`类型。

### 3.`number`对象调用`toString()`报SyntaxError：

```
123.toString(); // SyntaxError
```

特殊处理为：

```
123..toString(); // '123', 注意是两个点！
(123).toString(); // '123'
```

原因：

```
个人认为是，它将 123. 看成是一个小数，然后你直接跟toString()，它不会认为 . 是调用方法，而是看成是数字的一部分。
```

我这样写没有一点问题：

```
const number = 123;console.log(number.toString());
```

但是如果直接：

```
console.log(123.toString());
```

则会报错。目的是为了消除歧义。

### 4.为什么要用parseInt()或者parseFloat()函数来将其他类型的对象转化为数字

[https://www.cnblogs.com/wangyang0210/p/9526440.html]: 

```
个人觉得：如上述连接所言，如果使用Number()函数来将其他对象转化为Number，数字值转化正常。而：
--
-Boolean值，true->1,false->0
null值，转为0
字符串为空，转为0
字符串中是纯科学计数，数值会直接转为十进制的数值
undefined , 对象 , 数组 ,字符串不为纯数字和科学计数时 返回NaN
--
parseInt()
--数值正常转化,如果为浮点数,则转为整型,舍弃小数点后的数值
--
--数字开头的字符串,只转换数字整数部分,忽略字母部分
--
--二进制,八进制,十六进制转化时,转化为十进制的数值
--
--科学计数法,能解析时正常解析,不能时返回第一个数字
--
--Boolean,undefined , 对象 , 数组 ,空字符串,字符串开头不为纯数字和科学计数时 返回NaN
--
-parseFloat()
--
--
--数值正常转化,整型为整型,浮点型为浮点型
--
--数字开头的字符串,只转换数字整数部分,忽略字母部分
--
--二进制,八进制,十六进制转化时,转化为十进制的数值
--
--科学计数法,能解析时正常解析,超出范围则返回Infinity
--
--Boolean,undefined , 对象 , 数组 ,空字符串,字符串开头不为纯数字和科学计数时 返回NaN
--
--不能有多个小数点报语法错误
```

