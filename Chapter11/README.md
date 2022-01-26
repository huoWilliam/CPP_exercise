# 11.1 使用关联容器

### 练习 11.1

> 描述 map 和 vector 的不同。

关联容器 `map` 中的元素是关键字-值 (key-value) 对；顺序容器 `vector` 是同一类型对象的集合。

### 练习 11.2

> 分别给出最适合使用 list、vector、deque、map 以及 set 的例子。

- `list`：在容器中间插入或删除元素的程序
- `vector`：保存同类型对象，且无特殊要求的程序
- `deque`：需要在容器头尾位置插入或删除元素，但不会在中间位置插入或删除元素的程序
- `map`：字典
- `set`：判断一个值是否在一个集合中

### [练习 11.3](11_3.cpp)

> 编写你自己的单词计数程序。

### [练习 11.4](11_4.cpp)

> 扩展你的程序，忽略大小写和标点。例如，"example."、"example,"和 "Example" 应该递增相同的计数器。

# 11.2 关联容器概述

## 11.2.1 定义关联容器

### 练习 11.5

> 解释 map 和 set 的区别。你如何选择使用哪个？

`map` 中的元素是关键字-值 (key-value) 对；`set` 中的每个元素只包含一个关键字。需要保存和查询值的时候使用 `map`，只保存关键字时使用 `set`。

### 练习 11.6

> 解释 set 和 list 的区别。你如何选择使用哪个？

`set` 中的关键字是唯一的、有序的；`list` 中可以有重复的值，且无序。不需要重复值时使用 `set`，否则使用 `list`。

### [练习 11.7](11_7.cpp)

> 定义一个 map，关键字是家庭的姓，值是一个 vector，保存家中孩子（们）的名。编写代码，实现添加新的家庭以及向已有家庭中添加新的孩子。

### [练习 11.8](https://github.com/shoujiaxin/cpp-primer-exercises/blob/master/第 11 章 关联容器/11.8.cpp)

> 编写一个程序，在一个 vector 而不是一个 set 中保存不重复的单词。使用 set 的优点是什么？

使用 `set` 可以无需特别处理保证存储的元素是不重复的。

## 11.2.2 关键字类型的要求

### 练习 11.9

> 定义一个 map，将单词与一个行号的 list 关联，list 中保存的是单词所出现的行号。

```cpp
std::map<std::string, std::list<std::size_t>> m;
```

### 练习 11.10

> 可以定义一个 vector<int>::iterator 到 int 的 map 吗？list<int>::iterator 到 int 的 map 呢？对于两种情况，如果不能，解释为什么。

可以定义 `vector<int>::iterator` 到 `int` 的 `map`，但是不能定义 `list<int>::iterator` 到 `int` 的 `map`，因为 `list` 的迭代器没有 `<` 运算符。

### 练习 11.11

> 不使用 decltype 重新定义 bookstore。

```cpp
std::multiset<Sales_data, bool (*)(const Sales_data&, const Sales_data&)> bookstore(compareIsbn);
```

## 11.2.3 pair 类型

### [练习 11.12](11_12.cpp)

> 编写程序，读入 string 和 int 的序列，将每个 string 和 int 存入一个 pair 中，pair 保存在一个 vector 中。

### [练习 11.13](11_13.cpp)

> 在上一题的程序中，至少有三种创建 pair 的方法。编写此程序的三个版本，分别采用不同的方法创建 pair。解释你认为哪种形式最易于编写和理解，为什么？

使用 `make_pair` 可以让程序意图更明显，且使用方便，更易于编写和理解。

### [练习 11.14](11_14.cpp)

> 扩展你在 11.2.1 节练习（第 378 页）中编写的孩子姓到名的 map，添加一个 pair 的 vector，保存孩子的名和生日。

# 11.3 关联容器操作

## 11.3.1 关联容器迭代器

### 练习 11.15

> 对一个 int 到 vector<int> 的 map，其 mapped_type、key_type 和 value_type 分别是什么？

- `mapped_type` 是 `vector<int>`
- `key_type` 是 `int`
- `value_type` 是 `pair<const int, vector<int>>`

### 练习 11.16

> 使用一个 map 迭代器编写一个表达式，将一个值赋予一个元素。

```cpp
map<int, int> m;
auto m_it = m.begin();
m_it->second = 1;
```

### 练习 11.17

> 假定 c 是一个 string 的 multiset，v 是一个 string 的 vector，解释下面的调用。指出每个调用是否合法：
>
> ```cpp
> copy(v.begin(), v.end(), inserter(c, c.end()));
> copy(v.begin(), v.end(), back_inserter(c));
> copy(c.begin(), c.end(), inserter(v, v.end()));
> copy(c.begin(), c.end(), back_inserter(v));
> ```

- 将 `v` 中的元素复制到 `c` 中
- 不合法，`c` 不支持 `push_back`
- 将 `c` 中的元素复制到 `v` 中
- 将 `c` 中的元素复制到 `v` 中

### 练习 11.18

> 写出第 382 页循环中 map_it 的类型，不要使用 auto 或 decltype。

```cpp
map_it` 的类型是 `std::map<std::string, std::size_t>::iterator
```

### 练习 11.19

> 定义一个变量，通过对 11.2.2 节（第 378 页）中的名为 bookstore 的 multiset 调用 begin() 来初始化这个变量。写出变量的类型，不要使用 auto 或 decltype。

```cpp
multiset<Sales_data, bool(*)(const Sales_data&, const Sales_data&)>::iterator ms_it = bookstore.begin();
```

## 11.3.2 添加元素

### [练习 11.20](11_20.cpp)

> 重写 11.1 节练习（第 376 页）的单词计数程序，使用 insert 代替下标操作。你认为哪个程序更容易编写和阅读？解释原因。

使用下标操作更容易编写和阅读，符合人们阅读和理解的习惯，形式简洁。

### 练习 11.21

> 假定 word_count 是一个 string 到 size_t 的 map，word 是一个 string，解释下面循环的作用：
>
> ```cpp
> while (cin >> word) ++word_count.insert({word, 0}).first->second;
> ```

读取输入到 `word` ，向 `word_count` 中插入关键字为 `word` ， 值为 `0` 的元素，并递增关键字为 `word` 元素的值。

### 练习 11.22

> 给定一个 map<string, vector<int>>，对此容器的插入一个元素的 insert 版本，写出其参数类型和返回类型。

- 参数类型为 `pair<string, vector<int>>`
- 返回类型为 `pair<map<string, vector<int>>::iterator, bool>`
- 