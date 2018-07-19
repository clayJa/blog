## JavaScript的作用域链
几乎每个JavaScript开发者初学时，都或多或少遇到一个bug（大多出现在编写异步函数的时候），产生这个bug的原因大多数“闭包”使用不当。因此，有些开发者为了避免产生这种bug，不使用“闭包”而采用其他方式编写函数。
然而，熟悉“闭包”和作用域链知识，可以是这一JavaScript中的难题成为以后编码的得力助手。

#### JavaScript闭包
闭包是为了获取某一变量的值并将其与原始作用域分离的行为，使其永久可用。 
下面的例子说明“闭包”带来的意料之外的结果。
##### helloBob
```javascript
  var helloStr = 'world';
  // 我想要构建一个函数，这个函数返回一个执行后能输出hello信息的函数
  var sayHello = function(name){
    return function(){
      console.log('Hello ' + name + '!');
    }
  }
  var sayGreeting = sayHello(helloStr);
  //我本想输出 Hello Bob
  helloStr = 'Bob';
  sayGreeting();//实际结果为：Hello world!
```
在这个例子中，我本想sayHello函数将访问helloStr变量，但是实际上在执行函数时却意外地捕获了该字符串变量的特定值。
下面这个异步计数器，你期望它输出什么呢?
##### asyncCounting
```javascript
for (var i = 0; i < 10; i++){
	setTimeout(function(){
		console.log(i);
	}, 1000);
}
```
实际输出结果为：
```javascript
10
10
10
10
10
10
10
10
10
10
```
在这个例子中，需要产生闭包，但实际上并没有。当函数一秒后输出值时，它将引用i的当前值，但是这个值很久以前就递增到了10。
想要理解函数何时捕获变量，何时不捕获变量，我们需要理解作用域。
#### 什么是作用域？
如果你把Javascript中的函数看作是状态机，那么作用域就是状态。在编写JavaScript代码时，无论在哪创建变量，这个变量都有一个作用域。如果它在函数内部，那么它的作用域就是该函数作用域。如果它没有函数作用域，那么它具有全局作用域。你可以将作用域看作一个结构化的对象，如下所示:
```javascript
{
	_scope,
	variables
}
```
_scope变量指向创建时所在的函数范围，或者全局范围（如果不在函数中）。这些范围将形成一个范围链，称为作用域链。variables变量是传递到函数中的所有变量的映射，或者将在函数中声明的变量(由于变量提升，函数中的变量都在函数的顶部声明，即使你可以将它们写在函数中其他任何地方)。
#### 闭包与范围链有什么关系?
当使用变量时，程序将遍历作用域链，直到找到该变量的条目。
将变量传递给一个函数，可以将该变量与其之前所在的作用域分离开来。
```javascript
var str1 = 'hello';
var printVar = function(v){
	return function(){
		console.log(v);
	};
};
//将变量传递给函数
var printHello = printVar(str1);
str1 = 'goodbye';
//将变量传递到函数中已经将其保存在函数的作用域中
printHello();//hello
```
在[Hello Bob](#user-content-helloBob)示例中，原来的字符串被保留了，因为它被传递到一个函数中，并保存在函数的作用域内，即使它在函数外部的变量被重新分配。
在[Hello Bob](#user-content-helloBob)示例的最后一行中，console.log语句的作用域链如下：
- scope (匿名函数作用域)
- cope.scope (sayGreeting函数作用域)
  + name: 'world'
- scope.scope.scope (全局作用域)
  + sayHello: function
  + helloStr: 'Bob'
  + sayGreeting: function
在异步计数示例中，一秒后当程序开始执行consol.log语句时。其作用域链如下：
+ scope (匿名函数作用域)
+ scope.scope (全局作用域)
  - i: 10
如果我们想要正确地重写异步计数示例，我们会生成一个闭包，这样它就会捕获i的当前值，而不是使用最终值。
```javascript
// 尽管我们变量名相同,但是我们使用传入的变量的当前值，而不是递增之后的值
var logI = function(i){
	return function(){
		console.log(i);
	};
};
for (var i = 0; i < 10; i++){
	setTimeout(logI(i), 1000);
}
```
在logI返回的匿名函数中捕获的i的值，现在在返回的匿名函数的范围之外是无法获取和改变的。这也是在Javascript中创建私有变量的一种方式。
#### 总结
+ 声明一个函数，就产生一个新的作用域
+ 函数在执行的过程中，先从自己的内部查找变量
+ 如果找不到，再从创建当前函数所在的作用域去查找，依次往上，直至全局作用域
