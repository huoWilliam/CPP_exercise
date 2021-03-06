# 12.1 动态内存与智能指针

## 12.1.1 shared_ptr 类

### 练习 12.1

> 在此代码的结尾，b1 和 b2 各包含多少个元素？
>
> ```cpp
> StrBlob b1;
> {
>   StrBlob b2 = {"a", "an", "the"};
>   b1 = b2;
>   b2.push_back("about");
> }
> ```

`b1` 包含 4 个元素，`b2` 离开作用域时被销毁了。

### [练习 12.2](12_2.h)

> 编写你自己的 StrBlob 类，包含 const 版本的 front 和 back。

### 练习 12.3

> StrBlob 需要 const 版本的 push_back 和 pop_back 吗？如果需要，添加进去。否则，解释为什么不需要。

不需要，`push_back` 和 `pop_back` 会修改 `StrBlob` 的数据成员，而 `const` 对象无法修改内容。

### 练习 12.4

> 在我们的 check 函数中，没有检查 i 是否大于 0。为什么可以忽略这个检查？

`check` 函数中参数 `i` 的类型是 `std::vector<std::string>::size_type`，它是一个无符号类型的值（第 79 页）。

### 练习 12.5

> 我们未编写接受一个 initializer_list explicit（参见 7.5.4 节，第 264 页）参数的构造函数。讨论这个设计策略的优点和缺点。

构造函数不声明为 `explicit` 则可以使用初始化列表隐式地创建 `StrBlob` 对象，使用更方便，但不够直观，且在程序中可能产生歧义；而声明为 `explicit` 则只能用于直接初始化（第 265 页）。

## 12.1.2 直接管理内存

### [练习 12.6](12_6.cpp)

> 编写函数，返回一个动态分配的 int 的 vector。将此 vector 传递给另一个函数，这个函数读取标准输入，将读入的值保存在 vector 元素中。再将 vector 传递给另一个函数，打印读入的值。记得在恰当的时刻 delete vector。

### [练习 12.7](12_7.cpp)

> 重做上一题，这次使用 shared_ptr 而不是内置指针。

### 练习 12.8

> 下面的函数是否有错误？如果有，解释错误原因。
>
> ```cpp
> bool b() {
>   int* p = new int;
>   // ...
>   return p;
> }
> ```

指针 `p` 离开作用域时，它所指向的动态内存没有释放。

### 练习 12.9

> 解释下面代码执行的结果。
>
> ```cpp
> int *q = new int(42), *r = new int(100);
> r = q;
> auto q2 = make_shared<int>(42), r2 = make_shared<int>(100);
> r2 = q2;
> ```

1. `q` 和 `r` 指向动态内存，所指对象的值分别为 `42` 和 `100`
2. 修改 `r`，和 `q` 指向相同的内存，但不释放整型对象 `100` 的动态内存
3. `q2` 和 `r2` 为 `shared_ptr<int>`，所指对象的值分别为 `42` 和 `100`
4. 修改 `r2`，和 `q2` 指向相同的内存，销毁整型对象 `100`，且释放其动态内存

## 12.1.3 shared_ptr 和 new 结合使用

### 练习 12.10

> 下面的代码调用了第 413 页中定义的 process 函数，解释此调用是否正确。如果不正确，应如何修改？
>
> ```cpp
> shared_ptr<int> p(new int(42));
> process(shared_ptr<int>(p));
> ```

正确。实参 `shared_ptr<int>(p)` 会创建一个 `p` 的临时拷贝，`process` 的参数是传值方式传递的，实参会被拷贝到 `ptr` 中，即 `p` 被拷贝 2 次，引用计数值为 3；当 `process` 结束时，临时拷贝和局部变量 `ptr` 都被销毁，`p` 的引用计数值为 1。

### 练习 12.11

> 如果我们像下面这样调用 process，会发生什么？
>
> ```cpp
> process(shared_ptr<int>(p.get()));
> ```

实参 `shared_ptr<int>(p.get())` 创建与 `p` 指向相同内存的临时 `shared_ptr<int>`，引用计数为 1，并被拷贝到 `ptr` 中；当 `process` 结束时，临时 `shared_ptr<int>` 和局部变量 `ptr` 都被销毁，`p` 指向的内存已经被释放，再试图使用 `p` 时，将发生未定义的行为。

### 练习 12.12

> p 和 sp 的定义如下，对于接下来的对 process 的每个调用，如果合法，解释它做了什么，如果不合法，解释错误原因：
>
> ```cpp
> auto p = new int();
> auto sp = make_shared<int>();
> ```
>
> (a) `process(sp);`
>
> (b) `process(new int());`
>
> (c) `process(p);`
>
> (d) `process(shared_ptr<int>(p));`

- 合法。`sp` 作为实参拷贝到局部变量 `ptr`
- 不合法。不能将 `int*` 转换为一个 `shared_ptr<int>`
- 不合法。同上
- 合法。创建与 `p` 指向相同内存的临时 `shared_ptr<int>`，作为实参拷贝到局部变量 `ptr`，但需注意此时不能再使用内置指针来访问 `shared_ptr<int>` 所指向的内存了（第 413 页）。

### 练习 12.13

> 如果执行下面的代码，会发生什么？
>
> ```cpp
> auto sp = make_shared<int>();
> auto p = sp.get();
> delete p;
> ```

`p` 与 `sp` 指向相同的内存，`delete p;` 后，该内存空间已经被释放，`sp` 变成空悬指针，使用 `sp` 会发生未定义的行为，且当 `sp` 离开其作用域时被销毁时，这块内存会被第二次 `delete`。

## 12.1.4 智能指针和异常

### 练习 12.14

> 编写你自己版本的用 shared_ptr 管理 connection 的函数。

```cpp
void end_connection(connection* p) { disconnect(*p); }

void f(destination& d){
    connection c = connect(&d);
    shared_ptr<connection> p(&c, end_connection);
    /*...*/
}
```

### 练习 12.15

> 重写第一题的程序，用 lambda（参见 10.3.2 节，第 346 页）代替 end_connection 函数。

```cpp
void f(destination& d){
    connection c = connect(&d);
    shared_ptr<connection> p(&c, [](connetion* p){
        disconnect(*p);
    });
    /*...*/
}
```

## 2.1.5 unique_ptr

### 练习 12.16

> 如果你试图拷贝或赋值 unique_ptr，编译器并不总是能给出易于理解的错误信息。编写包含这种错误的程序，观察编译器如何诊断这种错误。

报错 `error: object of type 'std::__1::unique_ptr<int, std::__1::default_delete<int> >' cannot be assigned because its copy assignment operator is implicitly deleted`

### 练习 12.17

> 下面的 unique_ptr 声明中，哪些是合法的，哪些可能导致后续的程序错误？解释每个错误的问题在哪里。
>
> ```cpp
> int ix = 1024, *pi = &ix, *pi2 = new int(2048);
> typedef unique_ptr<int> IntP;
> ```
>
> (a) `IntP p0(ix);`
>
> (b) `IntP p1(pi);`
>
> (c) `IntP p2(pi2);`
>
> (d) `IntP p3(&ix);`
>
> (e) `IntP p4(new int(2048));`
>
> (f) `IntP p5(p2.get());`

- 不合法， `ix` 不是动态对象的指针
- 合法，但会导致后续的程序错误，因为 `pi` 不是动态对象的指针，而 `p1` 离开作用域时会调用 `delete` 销毁对象并释放内存，导致程序错误
- 合法
- 不合法，`&ix` 不是动态对象的指针
- 合法
- 合法，但会导致后续的程序错误，`p2` 和 `p5` 指向相同的内存，当 `p2` 和 `p5` 离开作用域时，这块内存会被 `delete` 两次。*永远不要用 `get` 初始化另一个智能指针或为另一个智能指针赋值（第 414 页）。*

### 练习 12.18

> shared_ptr 为什么没有 release 成员？

`unique_ptr` 独占所指向的对象，某个时刻只能有一个 `unique_ptr` 指向一个给定的对象，需要使用 `release` 成员放弃对指针的控制权，将所有权从一个（非 const 的）`unique_ptr` 转移给另一个 `unique_ptr`；而 `shared_ptr` 允许多个指针指向同一个对象，不需要使用 `release` 放弃控制权。