# 6.1 函数基础

### 练习 6.1

> 实参和形参的区别是什么？

- 形参是定义函数时函数名后括号中的变量名；实参是调用函数时函数名后括号中的表达式
- 每个形参都是含有一个声明符的声明，只在调用时占用存储单元；实参是常量、变量或表达式，是形参的初始值，其类型必须与对应的形参类型匹配

### 练习 6.2

> 请指出下列函数哪个有错误，为什么？应该如何修改这些错误呢？
>
> (a)
>
> ```cpp
> int f() {
> string s;
> // ...
> return s;
> }
> ```
>
> (b)
>
> ```cpp
> f2(int i) { /* ... */ }
> ```
>
> (c)
>
> ```cpp
> int calc(int v1, int v1) { /* ... */ }
> ```
>
> (d)
>
> ```cpp
> double square (double x) return x * x;
> ```

- 函数返回值的类型与函数的返回类型不一致，修改为：

  ```C++
  string f() {
  string s;
  // ...
  return s;
  }
  ```

- 函数没有返回类型，修改为：

  ```C++
  void f2(int i) { /* ... */ }
  ```

- 函数的两个形参同名，修改为：

  ```c++
  int calc(int v1, int v2) { /* ... */ }
  ```

- 函数体需要使用花括号，修改为：

  ```cpp
  double square (double x) { return x * x; }
  ```

### [练习 6.3](6_3.cpp) 

> 编写你自己的 fact 函数，上机检查是否正确。

### [练习 6.4](6_4.cpp)

> 编写一个与用户交互的函数，要求用户输入一个数字，计算生成该数字的阶乘。在 main 函数中调用该函数。

### [练习 6.5](6_5.cpp) 

> 编写一个函数输出其实参的绝对值。

## 6.1.1 局部对象

### 练习 6.6

> 说明形参、局部变量以及局部静态变量的区别。编写一个函数，同时达到这三种形式。

- 形参是一种自动对象，函数开始时为形参分配存储空间，因为形参定义在函数体作用域之内，所以一旦函数终止，形参也就被销毁
- 局部变量的生命周期依赖于定义的方式：
  - 普通局部变量：当函数的控制路径经过变量定义语句时创建该对象，当到达定义所在的块末尾时销毁它
  - 局部静态变量：在程序的执行路径第一次经过对象定义语句时初始化，直到程序终止时销毁
- 局部静态变量在程序的执行路径第一次经过对象定义语句时初始化，并且直到程序终止才被销毁，在此期间即使对象所在的函数结束执行也不会对它有影响

```c++
int func(int val){ //形参
    static int ival = 0; //静态局部变量
    return val + ival++;
}

int main(){
    int i = 5; //局部变量
    while(i-- > 0){
        cout << func(1) << endl;
    }
    return 0;
}
```

### [练习 6.7](6_7.cpp)

> 编写一个函数，当它第一次被调用时返回 0，以后每次被调用返回值加 1。

## 6.1.2 函数声明

### [练习 6.8](Chapter6.h)

> 编写一个名为 Chapter6.h 的头文件，令其包含 [6.1 节练习](#61-函数基础)（第 184 页）中的函数声明。

## 6.1.3 分离式编译

### 练习 6.9

> 编写你自己的 fact.cc 和 factMain.cc，这两个文件都应该包含上一小节的练习中编写的 Chapter6.h 头文件。通过这些文件，理解你的编译器是如何支持分离式编译的。

-  [fact.cc](..\..\cpp-primer-exercises\第 6 章 函数\fact.cc) 
-  [factMain.cc](..\..\cpp-primer-exercises\第 6 章 函数\factMain.cc) 

# 6. 2 参数传递

## 6.2.1 传值参数

### [练习 6.10](6_10.cpp)

> 编写一个函数，使用指针形参交换两个整数的值。在代码中调用该函数并输出交换后的结果，以此验证函数的正确性。

## 6.2.2 传引用参数

### [练习 6.11](6_11.cpp)

> 编写并验证你自己的 reset 函数，使其作用于引用类型的参数。

### 练习 6.12

> 改写 6.2.1 节中[练习 6.10](#练习-610)（第 188 页）的程序，使其引用而非指针交换两个整数的值。你觉得哪种方法更易于使用呢？为什么？

```cpp
void swap(int &val1, int &val2) {
  int tmp = val1;
  val1 = val2;
  val2 = tmp;
}
```

引用更易于使用。相比指针，引用不需要多次解引用

### 练习 6.13

> 假设 T 是某种类型的名字，说明以下两个函数声明的区别：一个是 void f(T)，另一个是 void f(&T)。

前者是值传递，后者使用引用传递

### 练习 6.14

> 举一个形参应该是引用类型的例子，再举一个形参不能是引用类型的例子。

- **当某种类型不支持拷贝操作时，或对其进行拷贝效率较低**，那么函数可以通过引用形参访问该类型的对象
- **函数需要改变形参内容，但是又不能影响传入函数的实参时，**形参不能使用引用类型；**实参是右值时，**形参不能为引用类型。

### 练习 6.15

> 说明 find_char 函数中的三个形参为什么是现在的类型，特别说明为什么 s 是常量引用而 occurs 是普通引用？为什么 s 和 occurs 是引用类型而 c 不是？如果令 s 是普通引用会发生什么情况？如果令 occurs 是常量引用会发生什么情况？

- `string` 对象可能会非常长，直接拷贝比较低效，且函数不需要修改 `string` 对象的内容，故使用常量引用；函数目的是查找字符，且不修改形参的值，故使用 `char` 类型；函数通过 `occurs` 返回字符出现的次数，`occurs` 的值是可变的，故使用引用类型
- 若 `s` 为普通引用，则字符串的内容可能被修改，**且无法传入字面值作为实参**
- 若 `occurs` 为常量引用，则由于 `++occurs;` 语句导致编译报错

## 6.2.3 const 形参和实参

### 练习 6.16

> 下面的这个函数虽然合法，但是不算特别有用。指出它的局限性并设法改善。
>
> ```cpp
> bool is_empty(string& s) { return s.empty(); }
> ```

- 无法传入字面值作为实参，修改如下：

  ```c++
  bool is_empty(const string& s) { return s.empty(); }
  ```

### 练习 6.17

> 编写一个函数，判断 string 对象中是否含有大写字母。编写另一个函数，把 string 对象全部改写成小写形式。在这两个函数中你使用的形参类型相同吗？为什么？

```C++
// 判断 string 对象中是否含有大写字母
bool has_upper(const string& s){
    for(auto c : s){
        if(isupper(c))
            return true;
    }
    return false;
}

// 把 string 对象全部改写成小写形式
void to_lower(string& s){
    for(auto &c : s){  // 注意使用&c
        if(isupper(c))
            c = tolower(c);
    }
}
```

不同，`has_upper`使用常量引用，因为该函数不需要改变传入的实参的值。`to_lower`需要对实参进行修改，故使用普通引用

### 练习 6.18

> 为下面的函数编写函数声明，从给定的名字中推测函数具备的功能。
>
> (a) 名为 compare 的函数，返回布尔值，两个参数都是 matrix 类的引用。
>
> (b) 名为 change_val 的函数，返回 vector<int> 的迭代器，有两个参数：一个是 int，另一个是 vector<int> 的迭代器。

- 比较两个`matrix`类型的对象是否相等

  ```c++
  bool compare(const matrix& m1, const matrix& m2);
  ```

- 修改`vector<int>`的迭代器所指向的元素的值

  ```cpp
  vector<int>::iterator change_val(int val, vector<int>::iterator iter)
  ```

### 练习 6.19

> 假定有如下声明，判断哪个调用合法、哪个调用不合法。对于不合法的函数调用，说明原因。
>
> ```
> double calc(double);
> int count(const string &, char);
> int sum(vector<int>::iterator, vector<int>::iterator, int);
> vector<int> vec(10);
> ```
>
> (a) `calc(23.4, 55.1);`
>
> (b) `count("abcda",'a');`
>
> (c) `calc(66);`
>
> (d) `sum(vec.begin(), vec.end(), 3.8);`

- 不合法，传入的实参数量和形参数量不匹配
- 合法
- 合法，`int`可以类型转换为`double`
- 合法，只是3.8会以3.0的方式传入

### 练习 6.20

> 引用形参什么时候应该是常量引用？如果形参应该是常量引用，而我们将其设为了普通引用，会发生什么情况？

- 当函数不需要修改传入的实参的值；传入的实参是常量或者常量引用；函数使用字面值常量作为实参初始化形参；
- 实参与形参的类型不匹配导致编译错误；无法传入字面值常量作为实参

## 6.2.4 数组形参

### 练习 6.21

> 编写一个函数，令其接受两个参数：一个是 int 型的数，另一个是 int 指针。函数比较 int 的值和指针所指的值，返回较大的那个。在该函数中指针的类型应该是什么？

```c++
int get_larger(int val1, const int* p_val2){
    return val1 > *p_val2 ? val1 : *p_val2;
}
```

指向类型为 `const int*`，是指向 `int` 常量的指针

### 练习 6.22

> 编写一个函数，令其交换两个 int 指针。

```cpp
void swap_pointer(int* p1, int* p2){
    int* temp = p1;
    p1 = p2;
    p2 = temp;
}
```

### [练习 6.23](6_23.cpp)

> 参考本节介绍的几个 print 函数，根据理解编写你自己的版本。依次调用每个函数使其输入下面定义的 i 和 j :
>
> ```cpp
> int i = 0, j[2] = {0, 1};
> ```

### 练习 6.24

> 描述下面这个函数的行为。如果代码中存在问题，请指出并改正。
>
> ```cpp
> void print(const int ia[10]) {
>   for (size_t i = 0; i != 10; ++i) 
>       cout << ia[i] << endl;
> }
> ```

输出数组中的元素。**形参的维度表示期望的数组维度，实参不一定，因此可能发生数组越界**

```cpp
void print(int *beg, int *end) {
  while (beg != end) {
    cout << *beg++ << endl;
  }
}
```

## 6.2.5 main：处理命令行选项

### [练习 6.25](6_25.cpp)

> 编写一个 main 函数，令其接受两个实参。把实参的内容连接成一个 string 对象并输出出来。

### [练习 6.26](6_26.cpp)

> 编写一个程序，使其接受本节所示的选项；输出传递给 main 函数的实参内容。

## 6.2.6 含有可变形参的函数

### 练习 6.27

> 编写一个函数，它的参数是 initializer_list<int> 类型的对象，函数的功能是计算列表中所有元素的和。

```cpp
int sum(initializer_list<int> il){
    int s = 0;
    for(const auto &elem : il){
        s += elem;
    }
    return s;
}
```

### 练习 6.28

> 在 error_msg 函数的第二个版本中包含 ErrCode 类型的参数，其中循环内的 elem 是什么类型？

`elem`是`string`对象的常量引用

### 练习 6.29

> 在范围 for 循环中使用 initializer_list 对象时，应该将循环控制变量声明成引用类型吗？为什么？

循环控制变量声明成引言类型更加高效，且**由于无法修改 initializer_list 对象的值，应该声明为常量引用类型**

# 6.3 返回类型和 return 语句

## 6.3.2 有返回值的函数

### 练习 6.30

> 编译第 200 页的 str_subrange 函数，看看你的编译器是如何处理函数中的错误的。

编译器输出 `error: non-void function 'str_subrange' should return a value [-Wreturn-type]`

### 练习 6.31

> 什么情况下返回的引用无效？什么情况下返回常量的引用无效？

- 返回局部对象的引用是无效的
- 希望返回的对象被修改时，返回常量的引用无效

### 练习 6.32

> 下面的函数合法吗？如果合法，说明其功能；如果不合法，修改其中的错误并解释原因。
>
> ```cpp
> int &get(int *array, int index) { return array[index]; }
> int main() {
>   int ia[10];
>   for (int i = 0; i != 10; ++i) get(ia, i) = i;
> }
> ```

合法，`get` 函数返回 `array` 所指向的整型数组中下标为 `index` 的元素的引用，`main` 函数使用循环调用 `get` 函数，将数组 `ia` 的元素依次赋值为其对应的下标的值

### [练习 6.33](6_33.cpp)

> 编写一个递归函数，输出 vector 对象的内容。

### 练习 6.34

> 如果 factorial 函数的停止条件如下所示，将发生什么？
>
> ```
> if (val != 0)
> ```

若 `val` 为正，从结果上来说没有区别，但是过程中多乘了个 1；若 `val` 为负，则函数将不断递归直到自身程序栈空间耗尽为止。

### 练习 6.35

> 在调用 factorial 函数时，为什么我们传入的值是 val - 1 而非 val-- ？

后置递减运算符返回修改之前的对象，所以递归传入的参数一直未改变，函数将一直递归下去。

## 6.3.3 返回数组指针

### 练习 6.36

> 编写一个函数的声明，使其返回数组的引用并且该数组包含 10 个 string 对象。不要使用尾置返回类型、decltype 或者类型别名。

```cpp
string (&func())[10];
```

### 练习 6.37

> 为上一题的函数再写三个声明，一个使用类型别名，另一个使用尾置返回类型，最后一个使用 decltype 关键字。你觉得哪种形式最好？为什么？

```cpp
// 使用类型别名
typedef string arrT[10];
arrT& func1();

// 使用尾置返回类型
auto func2()->string(&)[10];

// 使用decltype关键字
string str[10];
decltype(str)& func3();
```

使用 decltype 更好，更加简洁，且不需特别关心语法问题。

### 练习 6.38

> 修改 arrPtr 函数，使其返回数组的引用。

```cpp
decltype(odd)& arrPtr(int i){
    return (i % 2) ? odd : even;
}
```

# 6.4 函数重载

### 练习 6.39

> 说明在下面的每组声明中第二条语句是何含义。如果有非法的声明，请指出来。
>
> (a)
>
> ```cpp
> int calc(int, int);
> int calc(const int, const int);
> ```
>
> (b)
>
> ```c++
> int get();
> double get();
> ```
>
> (c)
>
> ```cpp
> int *reset(int *);
> double *reset(double *);
> ```

- 声明返回`int`类型的函数`calc`，其参数为两个`int`类型的常量；非法，重载函数时顶层const自动忽略，**一个拥有顶层const的形参无法与另一个没有顶层const的形参区分开**，故 `int calc(const int, const int);` 重复声明了 `int calc(int, int);`
- 声明返回 `double` 类型的函数 `get`，其形参列表为空；非法，`double get()` 与 `int get()` 只有返回类型不同，**重载函数不允许两个函数除了返回类型外其他所有的要素都相同**
- 声明返回 `double*` 类型的函数 `reset`，其形参为 `double*` 类型，函数重载了 `int *reset(int *);`

# 6.5 特殊用途语言特性

## 6.5.1 默认参数

### 练习 6.40

> 下面的哪个声明是错误的？为什么？
>
> (a) `int ff(int a, int b = 0, int c = 0);`
>
> (b) `char *init(int ht = 24, int wd, char bckgrnd);`

- 正确
- 错误，一旦某个形参被赋予了默认值，它后面的所有形参都必须有默认值

### 练习 6.41

> 下面的哪个调用是非法的？为什么？哪个调用虽然合法但显然与程序员的初衷不符？为什么？
>
> ```
> char *init(int ht, int wd = 80, char bckgrnd = ' ');
> ```
>
> (a) `init();`
>
> (b) `init(24, 10);`
>
> (c) `init(14, '*');`

- 非法，第一个形参没有默认值，必须使用实参初始化
- 合法
- 合法，但与初衷不符，`*`被强制类型转换为`int`类型传入第二个形参

### [练习 6.42](6_42.cpp)

> 给 make_plural 函数（参见 6.3.2 节，第 201 页）的第二个形参赋予默认实参 's', 利用新版本的函数输出单词 success 和 failure 的单数和复数形式。

## 6.5.2 内联函数和 constexpr 函数

### 练习 6.43

> 你会把下面的哪个声明和定义放在头文件中？哪个放在源文件中？为什么？
>
> (a) `inline bool eq(const BigInt&, const BigInt&) {...}`
>
> (b) `void putValues(int *arr, int size);`

- 放在头文件。内联函数可以在程序中多次定义，但是对于某个给定的内联函数，它的多个定义必须完全一致。
- 放在头文件。函数声明建议都放在头文件中

### 练习 6.44

> 将 6.2.2 节（第 189 页）的 isShorter 函数改写成内联函数。

```cpp
inline bool isShorter(const string &s1, const string &s2) {
  return s1.size() < s2.size();
}
```

### 练习 6.45

> 回顾在前面的练习中你编写的那些函数，它们应该是内联函数吗？如果是，将它们改写成内联函数；如果不是，说明原因。

**内联机制用于优化规模较小、流程直接、频繁调用的函数**

### 练习 6.46

> 能把 isShorter 函数定义成 constexpr 函数吗？如果能，将它改写成 constxpre 函数；如果不能，说明原因。

不能，constexpr 函数的返回类型及所有形参的类型都必须是字面值类型

## 6.5.3 调试帮助

### [练习 6.47](6_47.cpp)

> 改写 [6.3.2 节（第 205 页）练习中使用递归输出 vector 内容的程序](https://github.com/shoujiaxin/cpp-primer-exercises/blob/master/第 6 章 函数/README.md#练习-633)，使其有条件地输出与执行过程有关的信息。例如，每次调用时输出 vector 对象的大小。分别在打开和关闭调试器的情况下编译并执行这个程序。

### 练习 6.48

> 说明下面这个循环的含义，它对 assert 的使用合理吗？
>
> ```cpp
> string s;
> while (cin >> s && s != sought) { }  // 空函数体
> assert(cin);
> ```

读取用户输入的字符串，若不等于 `sought` 则进行循环；不合理，应改为 `assert(s == sought)`

# 6.6 函数匹配

### 练习 6.49

> 什么是候选函数？什么是可行函数？

- 候选函数是指在一组重载函数中，与被调用的函数同名、声明在调用点可见的函数
- 可行函数是指在候选函数中，形参数量和本次调用提供的实参数量相等、每个实参的类型与对应的形参类型相同或者能转换成形参的类型的函数

### 练习 6.50

> 已知有第 217 页对函数 f 的声明，对于下面的每一个调用列出可行函数。其中哪个函数是最佳匹配？如果调用不合法，是因为没有可匹配的函数还是因为调用具有二义性？
>
> (a) `f(2.56, 42)`
>
> (b) `f(42)`
>
> (c) `f(42, 0)`
>
> (d) `f(2.56, 3.14)`

- 可行函数：`void f(int, int);`和`void f(double, double = 3.14);`；调用不合法，该调用具有二义性。
- 可行函数：`void f(int);`和`void f(double, double = 3.14);`；前者为最佳匹配。
- 可行函数：`void f(int, int);`和`void f(double, double = 3.14);`；前者为最佳匹配。
- 可行函数：`void f(int, int);`和`void f(double, double = 3.14);`；后者为最佳匹配。

### [练习 6.51](6_51.cpp)

> 编写函数 f 的 4 版本，令其各输出一条可以区分的消息。验证上一个练习的答案，如果你的回答错了，反复研究本节内容直到你弄清自己错在何处。

## 6.6.1 实参类型转换

### 练习 6.52

> 已知有如下声明：
>
> ```
> void manip(int ,int);
> double dobj;
> ```
>
> 请指出下列调用中每个类型转换的等级。
>
> (a) `manip('a', 'z');`
>
> (b) `manip(55.4, dobj);`

- 通过类型提升实现的匹配
- 通过算术类型转换实现的匹配

### 练习 6.53

> 说明下列每组声明中的第二条语句会产生什么影响，并指出哪些不合法（如果有的话）。
>
> (a)
>
> ```cpp
> int calc(int&, int&); 
> int calc(const int&, const int&);
> ```
>
> (b)
>
> ```cpp
> int calc(char*, char*);
> int calc(const char*, const char*);
> ```
>
> (c)
>
> ```cpp
> int calc(char*, char*);
> int calc(char* const, char* const);
> ```

- 重载`int calc(int&, int&);`函数，使用常量引用作为形参
- 重载`int calc(char*, char*);`函数，使用指向 `char` 类型常量的指针作为形参
- 不合法，重复声明了 `int calc(char*, char*);`，重载函数无法区分是否有顶层 const

# 6.7 函数指针

### 练习 6.54

> 编写函数的声明，令其接受两个 int 形参并返回类型也是 int；然后声明一个 vector 对象，令其元素是指向该函数的指针。

```cpp
int func(int, int);
vector<int(*)(int, int)> vec; //写法1
vector<decltype(func) *> vec1; //写法2
using p_f = int (*)(int, int);
vector<p_f> vec2; //写法3
```

### 练习 6.55

> 编写 4 个函数，分别对两个 int 值执行加、减、乘、除运算；在上一题创建的 vector 对象中保存指向这些函数的指针。

```cpp
int add(int val1, int val2) { return val1 + val2; }
int minus(int val1, int val2) { return val1 - val2; }
int multiply(int val1, int val2) { return val1 * val2; }
int divide(int val1, int val2) { return val1 / val2; }

vec.push_back(add);
vec.push_back(minus);
vec.push_back(multiply);
vec.push_back(divide);
```

### [练习 6.56](6_56.cpp)

> 调用上述 vector 对象中的每个元素并输出结果。

```cpp
3
-1
2
0
```

