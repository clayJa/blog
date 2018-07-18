## if(condition) VS a == b
#### 你真的了解判断语句吗？
判断语句是程序设计中最基本的语句，我们无时无刻不在与其产生交集，或许在看到这篇文章的前一秒，你正写完一个条件判断以满足功能需求。但是，有时候碰到一些奇怪的面试题，我们或许会怀疑“我真的了解判断语句吗？”。
#### 牛刀小试
```
  if("hello") {
    console.log("hello");
  }
  if("") {
    console.log("empty);
  }
  if(" ") {
    console.log("blank");
  }
  if("[0]") {
    console.log("array");
  }
  if("0.00") {
    console.log("0.00")
  }
```
上面的是不是so easy，可能都觉得有些无聊了QAQ，嘿，先别走啊，再看看下面的。
```javascript
  "" == 0
  " " == 0
  "" == true
  " " == true
  !" " == true
  "hello" == true
  "0" == true
  undefined == null
  {} == true
  [] == true
```
```javascript
  var obj = {
    a: 0,
    valueOf: function() {return 1}
  }
  obj == "[object Object]"
  obj == 1
  obj == true
```
嗯~，好像需要思考了，，，接下来，我们来了解一些判断的内部比较原理。
#### 言归正传
##### 对于if(condition)
对于if括号中的表达式（这里指单个变量），会被强制转化为Boolean类型，转化规则如下：

 类型 | 结果 
------ | :------:
undefined | false  
null | false
Boolean | 直接判断
Number | +0、-0、NaN为false，其他为true
String | 空字符串为false，其他都为true
Object | true
##### 对于x == y类型判断
结果映射为下：

x | y | 结果
------ | :------: | :------:
null | undefined | true
Number | String | x == toNumber(y)
Boolean | (any) | toNumber(x) == y
Object | String or Number | toPrimitive(x) == y
##### 上表中的toNumber表示强制转化为Number，转化规则如下：
Type | Result
------ | :------:
undefined | NaN
null | 0
Boolean | true -> 1,false -> 0
String | "abc" -> NaN,"123" -> 123
##### toPrimitive表示对于Object类型转化规则，对于Object类型，先尝试调用valueOf方法获取结果。如果没有定义，再尝试调用toString方法获取结果。

##### 了解了这些你就可以任意使用if-else了？哦，不！
[look-up表了解一些](https://juejin.im/post/5b4b73e7f265da0f96287f0a)




