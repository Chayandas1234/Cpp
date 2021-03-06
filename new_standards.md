---
author: lunar
date: Wed 23 Sep 2020 09:20:09 PM CST
---

## 探讨 C++ 新标准

> 《C++ Primer Plus》读书笔记

本章介绍C++11对C++所做的改进，包括以下主题：

- 新的声明方式
- 移动语义和右值引用
- 新的类功能
- Lambda 表达式
- 包装器模板 function
- 可变参数模板

#### 新声明

1. `auto`

    C++11以后可以将`auto`用于自动类型推导，大大简化了代码

2. `decltype`

    `decltype`将变量的类型声明为表达式指定的类型。

    `decltype(x) y;`

    上面的语句意思是让 y 与 x 的类型相同。

    这在定义模板时特别有用

    ```c++
    template<typename T, typename U>
    void func(T t, U u) {
        decltype(T*U) temp;
    }
    ```

3. 返回类型后置

    C++11新增了一种语法，允许在函数名和参数列表之后指定返回类型。

    使得你能够使用`decltype`来指定模板函数的返回类型：

    ```c++
    template<typename T, typename U>
    auto func(T t, U u) -> decltype(T*U) {
        ...
    }
    ```

4. 模板别名：`using=`

    主要用于创建模板的别名。

    ```c++
    template<typename T>
    using arr = std::array<T,12>;
    arr<double> a1;
    ```

    `typedef`无法实现这种程度的别名。

5. `nullptr`

    以前是使用整数0来表示空指针。

#### 右值引用与移动语义

可以参照这篇[文章](reference.md)

#### 新的类功能

**特殊的成员函数**

在原有4个特殊成员函数的基础上，又新增了：移动构造函数和移动赋值运算符。

**默认的方法和禁用的方法**

假定你要使用某个默认的函数，而这个函数由于某种原因不会自动的创建。例如自己写了移动构造函数，编译器就不会自动创建默认的构造函数。

为此，你可以使用`default`关键字显式地声明这些函数的默认版本：

另一方面，通过关键字`delete`可以禁止编译器使用特定方法。

使用方法为：

```c++
Someclass() = default;
Someclass(const Someclass&) = delete;
```

**委托构造函数**

允许在一个构造函数的定义中使用另一个构造函数。

**Inherite constructors**

C++11允许在子类中使用`using Base::func;`使用父类的函数，包括所有名为`func`的函数。并且这些函数中如果有在子类中被重新定义的话，会被重载。

更神奇的是，构造函数也可以使用这种方法进行被子类使用。

**Manage virtual methods：override & final**

使用虚函数的一个缺点就是在子类中的同名函数会覆盖父类的版本，无法进行重载。

为此，在C++11中可以将`override`关键字添加到参数列表之后，表示要重载父类的函数。

同样，如果想要在父类想要禁止派生类中覆盖某个方法，可以在参数列表后面加上`final`关键字表示禁止重载。（越来越Java化了）

### Lambda 函數

#### 比較函數指針、函数符和Lambda函数

C++11引入了Lambda匿名函数，语法格式为

```c++
[](type x) {return expression;}
```

这是只有一条返回语句的形式。当函数体不止这一条语句时，需要指定返回类型，使用后置返回类型的形式：

```c++
[](type x)->type{body;return expression;}
```

### 包装器

通过标准库中的`function`对象可以将可调用对象包装，返回一个统一的对象。

语法格式为

```c++
std::function<func(type x,type y)> func_instance;
```

随后便可以像函数一样调用。

### 可变参数模板

可变参数模板（variadic tempalte）使得你可创建可接受可变数量的参数的模板函数和模板类。

语法格式为：

```c++
template<typename... Args>
void func(Args... args) {
    
}
```

`Args`允许接受大于等于零数量的任意类型的参数。

问题是我们不能通过索引直接将参数直接取出来。正确的方法是使用递归（真不懂为什么设计成这样），比如下面这种方法：

```c++
//零参数的同名函数用于终止递归
void func() {
    //do something
}

//被调用的函数
template<typename T, typename... Args>
void func(T t, Args... args) {
    //do something，t可以表示在当前递归里args的第一个参数
    func(args); //在这里递归调用
}

//如果想要在只剩下一项参数时，还可以定义这样一个函数
template<typename T>
void func(T t) {
    //do something
}
```



