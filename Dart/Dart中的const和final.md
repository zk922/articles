# Dart中的const和final

dart基础中，令人疑惑的一个地方首先就是const和final。finally与const令人疑惑的地方，其实主要在"编译时常量"的理解上。其实，忽视这个特性，也是可以实现很多效果的。但是这个特性正是dart的一个特点。

dart中常量在语法格式，行为表现上，都有很多另js，ts开发者很困惑的地方。其实，final，const应该从三种情况考虑，每种情况都是不同的：

1. 作用在变量（variable）上的final与const。
2. 作用在值（value）上的const。
3. 类中的final与const。

首先，需要了解下几个概念：
1. 对象的概念：对象，其实都应该是和c++中一致的。**一个对象，指的是分配给程序的一块内存区域。**
2. 上下文/作用域/context：这个概念可能都听过，也就是程序运行的上下文。js中，一般也就是this的指向作用空间。只是，dart里，this只存在于类体中了。

下面就分别区分下上面说的三种情况。

## 一、变量上的final与const

如果想让一个变量初始化后，它的值不可改变，那就把他声明成一个常量。首先我们可以肯定的是，给一个变量声明了const或者final后，这个变量就不能被再次赋值了。

``` dart
  const int a = 1;
  final int b = 1;

  a = 2;  // Error: Setter not found: 'a'.
  b = 2;  // Error: Setter not found: 'b'.
```
但是官网明确点出了一点，const的特点叫"compile-time constant"。而且，"Const variables are implicitly final"。意思就是说，const变量是编译时常量，并且，const变量也是final的。

当我们把const和final变量的值改为复杂类型的时候，就可以看出区别来了。

``` dart
const Map a = {'x': 1};
const Map b = {'x': 1};
final Map c = {'x': 1};
final Map d = {'x': 1};

print(a == b);   //true
print(a == c);   //false
print(c == d);   //false
```
按道里，我们每次写出一个字面量的`{'x': 1}`的时候，应该是都会创建一个新的Map对象，然后初始化给前面的变量的。但是，上面的a与b是相等的。结合前面说的对象的概念，那就是说，const声明的a与b是共用的同一个内存空间。而final声明的c与d，每次都是重新分配的一个内存空间。

再看种情况：
``` dart
  const Map a = {'x': 1};
  final Map b = {'x': 1};

  a['x'] = 2;    // Cannot set value in unmodifiable Map
  b['x'] = 2;    // 正常执行
```
这个就说明了一个问题，const声明的对象，属性值也是不可以写的了（这个特点，在第二节"作用在值上的const"再解释）。而final声明的对象，依然可以对成员属性进行修改。所以，这个final的特点，有点像js种的const。而dart中的const，本身有final的特点，不能再次赋值给变量；还有个特点是所有同样字面值的对象都是同一个对象，而且不能修改。

综上，"作用在变量"这一条我们总结出来的特点就是:

1. final仅是变量不能重新赋值，但是final对象仍然可以修改成员属性。
2. const即让变量不能重新赋值，又让同样的const字面量均为同一对象，而且不能修改。

## 二、作用在值上的const

官网有一种写法，是const后面直接跟着对象字面值，而不是变量名。(只能作用在复合对象上，基础类型num，String，bool不可以)
```dart
var a = const [1];
```
这种情况叫做常量值。

如果const后面的字面量是相同的，那么，就是同一个对象。
```dart
Map a = const {'x': 1};
Map b = const {'x': 1};
print(a == b);  //true
```

需要注意的是，常量对象的成员属性，也必须是常量值。对于成员，可以不显式的指明const，dart会自动帮我们推断。

``` dart
Map a = const { 'x': const {'y': 1}};
//与下面等效
Map b = const { 'x': {'y': 1}};
```

所以，结合第一节的特点，可以看出，const本质是对值的修饰，final是对变量的修饰。当const修饰变量标识的时候，可以理解为final修饰变量+const修饰值。
``` dart
const Map a = { 'x': {'y': 1}};

//equivalent
final Map a = const { 'x': const {'y': 1}};
```

## 三、class中的final与const，以及const constructor

在类中，const只能作用在static属性上。跟据官方人员解释，这是因为const值本身的目的就是复用，并且不可修改。作用在实例属性上，每个实例都创建一个const属性是一种空间浪费。

而final属性即可以作用在实例属性上，也可以作用在static属性上。作用在static属性上时候，必须直接初始化；**作用在实例属性上时候，则必须在构造函数前初始化。**dart中的初始化和c++中一样，是一个很重要的概念。
初始化final属性，使用Initializer list语法。
``` dart
class Point{
  
  final num x;

  final num y;
  
  Point(x, y): x = x, this.y = y; //每个初始化表达式的左值可以使用this，但一般都是省略。
  
}
```
上面写法有个简写方式：
```dart
class Point{
  
  final num x;

  final num y;
  
  Point(this.x, this.y);//上面写法的语法糖，无构造函数体时候，可以使用;结束，省去{}
  
}
```

const constructor是一种特殊的构造器。如果类构造函数调用时候，使用const修饰，并且接受同样的参数，那么就返回同一个实例对象。

const constructor因为实例是不变的，也就不允许有初始化后的构造函数体。
```dart
class Point{
  
  final num x;

  final num y;
  
  const Point(this.x, this.y);//const修饰构造函数，无构造函数体
  
}

void main(){
  Point a = const Point(1,2);
  Point b = const Point(1,2);
  Point c = Point(1,2);
  Point d = Point(1,2);

  print(a == b); //true  const返回的是常量实例，每次都是返回同一个对象
  print(a == c); //false c是普通实例
  print(c == d); //false 就算声明了const construcor，如果不使用const调用的化，每次都是返回新的实例
}
```

## 四、总结

其实，上面所有概念分离开理解的话，还是很清晰的。

final修饰变量（variables）。final声明的复合对象是不可改变变量指向，但是可以改成员属性的。

const本质是修饰值（values）。当用在修饰变量上时，本质是final变量+const值。const的值是深度不可改的。

对于const constructor，虽然也可以说是修饰值，但是具体内涵还是需要看下[这篇文章](https://japhr.blogspot.com/2012/12/dart-constant-constructors.html)中评论的解释。

dart中有个概念叫field，本质就是一个可选的有getter与setter的内存存储空间。而final本质就是创建一个没有setter的field，因而才必须初始化。

使用const constructor时候，我们可以看到，类的成员属性标为了final，但是只有在类实例化时候才初始化。

然而，cosnt constructor的目的不是去创建一个final field，使用其他方法也能实现同样的效果。真正的目的是去创建一个编译时常量。

这里就要理解下编译时常量的概念了。与编译时相对的是运行时，也就是说，在运行前，编译器就可以确定这个值是个定值了。这也是为什么const constructor不需要函数体的原因，因为肯定是定值，是不会有动态逻辑的。像获取时间这样的操作，是不可能作为编译时常量的。