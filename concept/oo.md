## 理解面向对象
大家一定注意到在面试时总是会被问一遍又一遍的同样的陈词滥调的问题，例如：

+ 你觉得五年后你自己是什么样子（你对五年后的自己有什么期待）？
+ 你认为什么是你最大的弱点？

同样面试程序开发工作，面向对象也是高频考点之一。
### 1. 什么是面向对象？
#### 1.1 [面向对象的历史](https://en.wikipedia.org/wiki/Object-oriented_programming#History)
 面向对象是一种编程范式（也叫方法论、思维方法等）。目前大部分语言都是支持多种范式的，包括面向对象这种范式。
 + 1950<br>
 objects 和 oriented 等技术名词在 MIT 里出现，概念尚未明确。ALGOL 语言里有面向对象的一些早期形态，如消息、方法、成员函数等
 + 1960<br>
 Simula 语言初步实现了面向对象思想，一些重要的概念得以实现，如类、对象、继承、动态绑定等。
 不过 Simula 没有实现 private 和 public 等访问修饰器.
 + 1970<br>
 Smalltalk 出现。Smalltalk 主要是受 Simula 启发。Lisp 又受 Smalltalk 的影响，也开始接纳面向对象思想，最终导致了 Flavors（首个引入 mixin 的语言）、CommonLoops（支持多继承），而这两门语言有导致了『Common Lisp 对象系统』的出现。
 + 1980<br>
 1981 年 Adele Goldberg 撰文将 Smalltalk 和面向对象编程介绍给更多的人。1986 年第一届『面向对象编程峰会』举办，吸引了一千多人参加。期间，C++ 和 Objective-C 受此影响诞生。
 + 1990<br>
 面向对象思想被广泛接纳，甚至成为主流编程思想，尤其是用户界面编程领域。事件驱动编程在它的带领下也开始变得流行。一些原本不支持面向对象的语言也开始引入面向对象（BASIC、Pascal），并引发了一些问题。一些从一开始就支持面向对象，也支持以前的过程式编程的语言被发明出来，如 Python 和 Ruby。最重要的商业化面向对象语言是 Sun 公司开发的 Java 语言，以及微软公司开发的 C# 和 VB.NET。
#### 1.2 面向对象语言的分类
- 纯面向对象语言：一切都是对象，包括数字、字符串也是对象，如 Python、Ruby、Scala、Smalltalk
- 完全支持面向对象，也支持过程式，如 Java、C++、C#
- 本来不支持面向对象，后来加上的，如 PHP、Perl
= 看起来像面向对象，但没有完全使用面向对象（比如基于原型来模拟面向对象）：JavaScript、Lua
### 2. 面向对象有哪些特性？
提到面向对象的特性，有八个字自然而然浮现在我们脑海中年蹦出来了，那就是【封装】【抽象】【继承】【多态】
#### 2.1 封装
假设我们有一个程序，拥有一些逻辑不同的对象，它们根据程序中定义的规则相互通信。<br>
我们通过实现封装让每个对象在类中保持其私有状态时，其他对象无法直接访问此状态。与此同时，他们只能调用一个公共函数列表，也就是方法。对象通过自己管理自己的状态（除非明确允许，否则没有其他类可以触及它）。如果要与对象通信，则应使用提供的方法。<br>
假设我们正在构建一个小游戏。有人，还有一只猫，猫饿了，人就需要喂猫，人喂了猫，猫饥饿感就减少了。<br>
我们想要使用面向对象的思想进行封装，因此我们将所有“猫”的状态与动作封装到Cat类中。它可能看起来像这样：<br>
人可以喂猫，但人无法直接改变猫的饥饿程度。<br>
在这里，猫的“状态”的情绪，饥饿和能量是私人变量。它还有一个私有方法meow()。猫可以随时调用它(喵喵叫)，人（其他类）不能告诉猫什么时候喵喵叫。<br>
人和猫都可以做的是在公共方法sleep（），play（）和feed（）中定义。他们每个都以某种方式修改内部状态，并可能调用meow（）。因此，进行了私有状态和公共方法之间的绑定。——这是封装。
#### 2.2 抽象
抽象可以被认为是封装的延伸M<br>
在实际的应用中，程序通常非常大。并且单独的对象相互通信很多。因此，不断在这个维护这样的代码，并在其上增加新的功能特性是很复杂的，常常是牵一发而动全身。抽象就是为了缓解这个问题。<br>
应用抽象意味着对象只对外部公开使用它的方法，而隐藏对象内部实现细节。<br>
抽象就跟我们平时手机一样，手机组成很复杂，有硬件有软件。但使用它确很简单。我们只需触摸即可与手机进行互动。在我们触摸的时候手机做了什么？我们不必知道（也就是隐藏了实现细节），软件更新（也就是抽象内部逻辑更改）很少影响我们的使用。<br>
#### 2.3 继承
理解封装和抽象帮助我们能够开发和维护一个大的代码库。但是在实际面向对象编程中，我们常常会遇到一些对象，他们有着相同的逻辑，但是又不尽相同。<br>
我们是把他们隔离开为两个独立的，各自维护自身逻辑状态的两个类吗？但是他们的确有千丝万缕的联系。<br>
如果我们能够重用通用逻辑并将各自独特的逻辑提取出来就好了。<br>
实现这一目标的一种方法是继承<br>
这意味着我们通过（父）类派生来创建（子）类。这样，我们就拥有了一个层次结构。子类重用父类的所有字段和方法（公共部分），并且可以实现它自己的（独特部分）。<br>
例如：私人教师是教师的一种，而任何教师属于人。如果我们的计划需要管理公立和私立教师，还有其他类型的人，比如学生，我们可以通过继承实现。这样，每个类都拥有了父类的逻辑与特点，并且可以实现与维护自己独特的逻辑与状态。
#### 2.4 多态
通过继承，我们可以实现父类和子类。假设我们有一个父类和一些继承它的子类。有时我们想要使用一个集合（例如一个列表，它包含所有这些类的混合）；或者我们有一个为父类实现的方法，但我们也想将它用于子类。这时可以通过使用多态来解决。<br>
简而言之，多态性提供了一种使用与其父类完全相同的类的方法，因此不会混淆混合类型。但是每个子类都有自己的实现特点。<br>
这通常通过定义要重用的（父）接口来实现，它实现了一堆常用方法。然后，每个子类能够实现这些方法的自己版本。<br>
例如：动物能够发出声音，猫和狗继承动物的类，也具有发出声音的特性。但是猫发出的声音为"喵喵喵"，狗发出的声音的为"汪汪汪"。在程序中，他们都使用say()这个从animal继承来的方法，但是猫和狗有自己不同的实现。
### 3. 古老的面向对象——Smalltalk
#### 3.1 安装
[gst-win - Windows installer for gst](https://github.com/mcandre/gst-win)
下载安装，然后打开 cmd 打开，输入 gst 即可开始使用 Smalltalk。
#### 3.2 语法
[官方教程](https://www.gnu.org/software/smalltalk/manual/html_node/Getting-started.html#Getting-started)<br>
使用字符串单引号，双引号是注释，不要使用。
```
(1 + 2) * 3  // 9
'Hello, world' printNl // 输出 Hello world
```
数组
```
x := Array new: 20
x at: 1
x at: 1 put: 99
(x at: 1) + 1
```
Set<br>
使用英文句号断句
```
x := Set new 
x add: 5. x add: 7. x add: 'foo'
x remove: 5
x includes: 7
```
字典
```
y := Dictionary new
y at: 'One' put: 1
y at: 'Two' put: 2
y at: 1 put: 'One'
y at: 2 put: 'Two'


y at: 1
y at: 'Two'
y
y! 用于删除 y 的值
y
```
#### 3.3 Smalltalk中的对象
在Smalltalk中所有的东西都是对象，或者应该被当作对象处理。例如下面的表达式：

2 + 3.

应当被理解为：向对象2发送消息+，参数为对象3。

(15 * 19) + (37 squared)

可以理解为：向15发送消息'\*'，参数为19；向37发送消息squared；最后向15*19的结果发送消息'+'，参数为37 squared的结果。
### 理解面向对象
+ 面向对象的核心就是对象与对象之间交互。
+ 对象维护自己的状态和生命周期
+ 每个对象独立
+ 对象和对象直接通过消息传递来工作
