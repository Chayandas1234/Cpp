---
author: lunar
date: Tue 01 Sep 2020 10:22:12 AM CST
---

### C++编程条款

#### 条款1: 尽量以const, enum, inline替换#define

最主要的原因在于#define定义的宏在预处理期间就已经被替换, 导致在调试时期无法知晓原先的命名, 给调试带来困难.

其次, 对于#define没有作用域的概念, 也就不能提供任何封装性, 而const修饰的变量可以通过static限制在类内或者文件内. 

另外, const还具有类型, 编译器可以进行安全检查; #define则不行.

最后, const相比于#define还可以节省内存.
const定义常量从汇编的角度看来只是给出了对应的内存地址. 而不是像#define一样进行变量的拷贝.
```c++
#define PI 3.14
const double pi = 3.14;

double d = PI;
double f = PI;

double r = pi;
double e = pi;
```
这段代码里面d和f在内存中均有一份拷贝, 而r和e则共享一个数据.

#### 条款2: 尽可能使用const

const语法虽然变化多端, 但并不高深莫测. 如果关键const 出现在星号的左边, 表示被指物是常量; 如果出现在星号右边, 表示指针自身是常量.

如果被指物是常量, 则const既可以置于类型之前, 也可以置于类型之后.

常成员函数: 在类中, 在成员函数的括号之后添加const关键字代表将其声明为常成员函数, 常成员函数有两个特点:
1. 常成员函数只能读取对象的成员变量, 代表只读函数, 无法对变量进行修改;
2. 不能调用类中任何非const成员函数, 这点和static很像.

并且, 在C++中, 就算两个成员函数只有const和非const的区别, 也可以进行重载.

此外, const修饰的对象表示该对象为常量对象, 只能调用常量对象的const成员函数, 因为任何非const成员函数都有修改成员变量的企图.

#### 条款3: 永远不要在确保对象已经初始化之前使用对象

基本类型可以在定义时就初始化, 自定义类型可以通过构造函数初始化.

构造函数初始化写在函数体内都不是初始化, 而是赋值. 成员变量的初始化实在成员初始化列表里面完成的. 如果想避免初始化一边, 又赋值一遍.可以自己完成成员初始化列表.
```c++
class Person {
private:
    string name;
    int age;
public:
    Person(string name, int age);
};

Person::Person(string name, int age)
    :name(name),
     age(age){
    
}
```

冒号后面的动作即为成员初始化, 显然这样效率更高.

但是, 还请记住, 由于自己完成了成员初始化列表以后编译器就不会再完成一遍, 所以确保所有需要初值的成员变量已经完成初始化, 否则会进入没有初值的状态, 带来"不确定行为".

使用成员初始化列表的另一个缺点在于当存在多个构造函数时带来大量的重复代码, 这时可以将部分赋值和初始化区别不大, 或者说代价较小的成员变量放在一个函数里面, 供所有的构造函数进行调用.

### 条款5: 了解C++默默编写并调用了哪些函数


