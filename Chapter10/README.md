# 10.1 概述

### [练习 10.1](10_1.cpp)

> 头文件 algorithm 中定义了一个名为 count 的函数，它类似 find， 接受一对迭代器和一个值作为参数。 count 返回给定值在序列中出现的次数。编写程序，读取 int 序列存入 vector 中，打印有多少个元素的值等于给定值。

### [练习 10.2](10_2.cpp)

> 重做上一题，但读取 string 序列存入 list 中。

# 10.2 初始泛型算法

## 10.2.1 只读算法

### [练习 10.3](10_3.cpp)

> 用 accumulate 求一个 vector<int> 中的元素之和。

### 练习 10.4

> 假定 v 是一个 vector<double>，那么调用 accumulate(v.cbegin(), v.cend(), 0) 有何错误（如果存在的话）？

只能得到整型的结果。`accumulate` 的第三个参数的类型决定了函数中使用哪个加法运算符以及返回值的类型（第 338 页）。

### 练习 10.5

> 在本节对名册 (roster) 调用 equal 的例子中，如果两个名册中保存的都是 C 风格字符串而不是 string，会发生什么？

若使用 `char*`，则会编译时会警告：`warning: ISO C++11 does not allow conversion from string literal to 'char *'`；使用 `const char*` 则可以正确比较两个 C 风格字符串是否相等。

## 10.2.2 写容器元素的算法

### [练习 10.6](10_6.cpp)

> 编写程序，使用 fill_n 将一个序列中的 int 值都设置为 0。

### 练习 10.7

> 下面程序是否有错误？如果有，请改正：
>
> (a)
>
> ```cpp
> vector<int> vec;
> list<int> lst;
> int i;
> while (cin >> i) lst.push_back(i);
> copy(lst.cbegin(), lst.cend(), vec.begin());
> ```
>
> (b)
>
> ```cpp
> vector<int> vec;
> vec.reserve(10);  // reverse 将在 9.4 节（第 318 页）介绍
> fill_n(vec.begin(), 10, 0);
> ```

- 传递给 `copy` 的目的序列至少要包含与输入序列一样多的元素，即 `vec` 的至少要跟 `lst` 一样大，修改如下

  ```cpp
  vector<int> vec;
  list<int> lst;
  int i;
  while (cin >> i) lst.push_back(i);
  vec.resize(lst.size());
  copy(lst.cbegin(), lst.cend(), vec.begin());
  ```

- `reserve(10)` 只是给容器分配至少能容纳 10 个元素的内存空间，而不改变容器中元素的数量（第 318 页），修改如下

  ```cpp
  vector<int> vec;
  vec.resize(10);
  fill_n(vec.begin(), 10, 0);
  ```

### 练习 10.8

> 本节提到过，标准库算法不会改变它们所操作的容器的大小。为什么使用 back_inserter 不会使这一断言失效？

使用 `back_inserter`，改变容器大小的是插入迭代器，而不是标准库算法，它通过调用 `push_back` 向容器中添加新元素。

## 10.2.3 重排容器元素的算法

### [练习 10.9](10_9.cpp)

> 实现你自己的 elimDups。分别在读取输入后、调用 unique 后以及调用 erase 后打印 vector 的内容。

### 练习 10.10

> 你认为算法不改变容器大小的原因是什么？

算法运行于迭代器之上，执行迭代器的操作，而不执行容器的操作，可以让算法不依赖于容器，只依赖于容器中元素的类型，这样算法可以用于标准库容器，也可以用于内置数组，有更好的通用性。

# 0.3 定制操作

## 10.3.1 向算法传递函数

### [练习 10.11](10_11.cpp)

> 编写程序，使用 stable_sort 和 isShorter 将传递给你的 elimDups 版本的 vector 排序。打印 vector 的内容，验证你的程序的正确性。

### [练习 10.12](10_12.cpp)

> 编写名为 compareIsbn 的函数，比较两个 Sales_data 对象的 isbn() 成员。使用这个函数排序一个保存 Sales_data 对象的 vector。

```cpp
bool compareIsbn(const Sales_data& data1, const Sales_data& data2) {
  return data1.isbn() < data2.isbn();
}	
```

### [练习 10.13](10_13.cpp)

> 标准库定义了名为 partition 的算法，它接受一个谓词，对容器内容进行划分，使得谓词为 true 的值会排在容器的前半部分，而使谓词为 false 的值会排在后半部分。算法返回一个迭代器，指向最后一个使谓词为 true 的元素之后的位置。编写函数，接受一个 string，返回一个 bool 值，指出 string 是否有 5 个或更多字符。使用此函数划分 words。打印出长度大于等于 5 的元素。

## 10.3.2 lambda 表达式

### 练习 10.14

> 编写一个 lambda，接受两个 int，返回它们的和。

```cpp
auto f = [](const int a, const int b) { return a + b; };
```

### 练习 10.15

> 编写一个 lambda，捕获它所在函数的 int，并接受一个 int 参数。lambda 应该返回捕获的 int 和 int 参数的和。

```cpp
void function(){
    /*...*/
    int val = 9;
    auto f = [val](const int a) { return val + a; };
    /*...*/
}
```

### [练习 10.16](10_16.cpp)

> 使用 lambda 编写你自己版本的 biggies。

### [练习 10.17](10_17.cpp)

> 重写 10.3.1 节练习 10.12（第 345 页）的程序，在对 sort 的调用中使用 lambda 来代替函数 compareIsbn。

### [练习 10.18](10_18.cpp)

> 重写 biggies，用 partition 代替 find_if。我们在 10.3.1 节练习 10.13（第 345 页）中介绍了 partition 算法。

### [练习 10.19](10_19.cpp)

> 用 stable_partition 重写前一题的程序，与 stable_sort 类似，在划分后的序列中维持原有元素的顺序。

## 10.3.3 lambda 捕获和返回

### [练习 10.20](10_20.cpp)

> 标准库定义了一个名为 count_if 的算法。类似 find_if，此函数接受一对迭代器，表示一个输入范围，还接受一个谓词，会对输入范围中每个元素执行。count_if 返回一个计数值，表示谓词有多少次为真。使用 count_if 重写我们程序中统计有多少单词长度超过 6 的部分。

### 练习 10.21

> 编写一个 lambda，捕获一个局部 int 变量，并递减变量值，直至它变为 0。一旦变量变为 0，再调用 lambda 应该不再递减变量。lambda 应该返回一个 bool 值，指出捕获的变量是否为 0。

```cpp
void func(){
    /*...*/
    int ival = 10;
    auto f = [&ival]() -> bool {
        if(ival==0)
            return 0;
        else{
            ival--;
            return false;
        }
    }
    /*...*/
}
```

## 10.3.4 参数绑定

### [练习 10.22](10_22.cpp)

> 重写统计长度小于等于 6 的单词数量的程序，使用函数代替 lambda。

### 练习 10.23

> bind 接受几个参数？

`bind` 接受一个可调用对象和一个逗号分隔的参数列表，对应可调用对象的参数，即 `bind` 的参数比它所接受的可调用的对象多一个。

### [练习 10.24](10_24.cpp)

> 给定一个 string，使用 bind 和 check_size 在一个 int 的 vector 中查找第一个大于 string 长度的值。

### [练习 10.25](10_25.cpp)

> 在 10.3.2 节（第 349 页）的练习中，编写了一个使用 partition 的 biggies 版本。使用 check_size 和 bind 重写此函数。

# 10.4 再探迭代器

## 10.4.1 插入迭代器

### 练习 10.26

> 解释三种插入迭代器的不同之处。

- `back_inserter`：只有容器支持 `push_back` 才能使用，调用 `push_back` 将元素插入容器末尾
- `front_inserter`：只有容器支持 `push_front` 才能使用，调用 `push_front` 将元素插入到容器第一个元素之前
- `inserter`：接受两个参数，第二个参数是一个指向给定容器的迭代器。调用 `insert` 将元素插入到给定迭代器所表示的元素之前

### [练习 10.27](10_27.cpp)

> 除了 unique（参见 10.2.3 节，第 343 页）之外，标准库还定义了名为 unique_copy 的函数，它接受第三个迭代器，表示拷贝不重复元素的目的位置。编写一个程序，使用 unique_copy 将一个 vector 中不重复的元素拷贝到一个初始化为空的 list 中

### [练习 10.28](10_28.cpp)

> 一个 vector 中保存 1 到 9，将其拷贝到三个其他容器中。分别使用 inserter、back_inserter 和 front_inserter 将元素添加到三个容器中。对每种 inserter，估计输出序列是怎样的，运行程序验证你的估计是否正确。

- 使用 `back_inserter` 和 `inserter` 得到 `1, 2, 3, 4, 5, 6, 7, 8, 9`
- 使用 `front_inserter` 得到 `9, 8, 7, 6, 5, 4, 3, 2, 1`

## 10.4.2 iostream 迭代器

### [练习 10.29](10_29.cpp)

> 编写程序，使用流迭代器读取一个文本文件，存入一个 vector 中的 string 里。

### [练习 10.30](10_30.cpp)

> 使用流迭代器、sort 和 copy 从标准输入读取一个整数序列，将其排序，并将结果写到标准输出。

### [练习 10.31](10_31.cpp)

> 修改前一题的程序，使其只打印不重复的元素。你的程序应该使用 unique_copy（参见 10.4.1 节，第 359 页）。

### [练习 10.32](10_32.cpp)

> 重写 1.6 节（第 21 页）中的书店程序，使用一个 vector 保存交易记录，使用不同算法完成处理。使用 sort 和 10.3.1 节（第 345 页）中的 compareIsbn 函数来排序交易记录，然后使用 find 和 accumulate 求和。

### [练习 10.33](10_33.cpp)

> 编写程序，接受三个参数：一个输入文件和两个输出文件的文件名。输入文件保存的应该是整数。使用 istream_iterator 读取输入文件。使用 ostream_iterator 将奇数写入第一个输入文件，每个值后面都跟一个空格。将偶数写入第二个输出文件，每个值都独占一行。

## 10.4.3 反向迭代器

### [练习 10.34](10_34.cpp)

> 使用 reverse_iterator 逆序打印一个 vector。

### [练习 10.35](10_35.cpp)

> 使用普通迭代器逆序打印一个 vector。

### 练习 10.36

> 使用 find 在一个 int 的 list 中查找最后一个值为 0 的元素。

```cpp
std::list<int> ilst{0, 1, 2, 3, 0, 4, 5, 6, 0, 7};
auto lastZero = find(ilst.crbegin(), ilst.crend(), 0);
```

### [练习 10.37](10_37.cpp)

> 给定一个包含 10 个元素的 vector，将位置 3 到 7 之间的元素按逆序拷贝到一个 list 中。

# 10.5 泛型算法结构

## 10.5.1 5 类迭代器

### 练习 10.38

> 列出 5 个迭代器类别，以及每类迭代器所支持的操作。

- 输入迭代器 (input iterator)：`==`、`!=`、`++`、`*`、`->`、顺序访问、单遍扫描、读取元素
- 输出迭代器 (output iterator)：`++`、`*`、单遍扫描、只能赋值一次
- 前向迭代器 (forward iterator)：`==`、`!=`、`++`、`*`、`->`、沿一个方向移动、读写元素
- 双向迭代器 (bidirectional iterator)：`==`、`!=`、`++`、`--`、`*`、`->`、正向/反向读写元素
- 随机访问迭代器 (random-access iterator)：`==`、`!=`、`++`、`--`、`*`、`->`、`<`、`<=`、`>`、`>=`、`+`、`+=`、`-`、`-=`、两个迭代器减法、下标运算符

### 练习 10.39

> list 上的迭代器属于哪类？vector 呢？

`list` 上的迭代器属于双向迭代器；`vector` 上的迭代器属于随机访问迭代器

### 练习 10.40

> 你认为 copy 要求哪类迭代器？reverse 和 unique 呢？

- `copy(beg, end, dest)` 要求 `beg` 和 `end` 为输入迭代器，`dest` 为输出迭代器（第 774 页）
- `reverse(beg, end)` 要求双向迭代器（第 777 页）
- `unique(beg, end)` 要求前向迭代器（第 777 页）

## 10.5.3 算法命名规范

### 练习 10.41

> 仅根据算法和参数的名字，描述下面每个标准库算法执行什么操作：
>
> ```cpp
> replace(beg, end, old_val, new_val);
> replace_if(beg, end, pred, new_val);
> replace_copy(beg, end, dest, old_val, new_val);
> replace_copy_if(beg, end, dest, pred, new_val);
> ```

- 用 `new_val` 替换 `beg` 到 `end` 左闭合区间内每个值为 `old_val` 的元素
- 用 `new_val` 替换 `beg` 到 `end` 左闭合区间内每个满足 `pred` 的元素
- 用 `new_val` 替换 `beg` 到 `end` 左闭合区间内每个值为 `old_val` 的元素，并拷贝到 `dest` 指定的目的序列
- 用 `new_val` 替换 `beg` 到 `end` 左闭合区间内每个满足 `pred` 的元素，并拷贝到 `dest` 指定的目的序列

# 10.6 特定容器算法

### [练习 10.42](10_42.cpp)

> 使用 list 代替 vector 重新实现 10.2.3 节（第 343 页）中的去除重复单词的程序。
