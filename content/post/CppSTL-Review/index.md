---
title: "C++ STL 期末复习"
date: 2024-12-27 20:52:00
lastmod: 2025-02-07 14:59:47
tags:
  - 学习
categories:
  - 学习
slug: "CppSTL-Review"
---
> 这篇文章的大部分内容都由ChatGPT辅助完成。<br>

# 一、STL概述

## 1. 什么是STL？

C++ 标准模板库（Standard Template Library，STL）是一套功能强大的 C++ 模板类和函数的集合，它提供了一系列通用的、可复用的算法和数据结构。

## 2. STL的组成部分

容器，算法，迭代器。

## 3. STL主要使用了什么技术实现？

模板。

# 二、模板与运算符重载

## 1. 函数模板，类模板

```c++
// 函数模板
template <typename T>
T add(T a, T b) {
    return a + b;
}
//类模板
template <typename T>
class ClassName {
    T data; // 使用类型参数 T
public:
    ClassName(T value) : data(value) {} // 构造函数
    T getData() const { return data; }  // 返回 data
};
```

## 2. traits技术

**主要思想**：提取不同类的共性，以便统一处理。

Traits 技术的核心思想是通过定义一个特性类（或结构体），在其中对类型的特征进行描述。这些描述可以是类型信息、操作支持情况等，通常通过模板类来实现。通过 `traits`，可以在编译期获取一个类型的“能力”或“特征”，从而做出不同的决策。

#### 使用 Traits 技术的示例

假设我们需要定义一个 is_integral trait 来判断一个类型是否为整数类型：

```c++
template <typename T>
struct is_integral {
    static const bool value = false; // 默认不是整数类型
};
// 特化：整数类型的 trait
template <>
struct is_integral<int> {
    static const bool value = true;
};
template <>
struct is_integral<long> {
    static const bool value = true;
};
template <>
struct is_integral<short> {
    static const bool value = true;
};
// 使用示例
int main() {
    std::cout << "int is integral: " << is_integral<int>::value << std::endl;   // 输出: 1
    std::cout << "double is integral: " << is_integral<double>::value << std::endl; // 输出: 0
    return 0;
}
```

在这个例子中：

`is_integral` 是一个 trait，用于判断给定类型 T 是否是整数类型。

`is_integral<int>`、`is_integral<long>` 等是类型特化，表示这几种类型是整数类型。

`is_integral<double>` 会返回 false，因为 double 不是整数类型。

## 3. 运算符重载

### 成员函数运算符重载（二元运算符重载）

二元运算符（如 `+`、`-`、`*`、`/` 等）需要两个操作数。

当重载为成员函数时，左操作数是调用对象（隐式传递），右操作数通过函数参数传递。

一个重载的例子：

```c++
// 重载加号运算符
#include <iostream>
using namespace std;
class Complex {
private:
    double real, imag;
public:
    Complex(double r = 0, double i = 0) : real(r), imag(i) {}
    // 成员函数重载 +
    Complex operator+(const Complex &other) const {
        return Complex(real + other.real, imag + other.imag);
    }
    void print() const {
        cout << real << " + " << imag << "i" << endl;
    }
};
int main() {
    Complex c1(1.0, 2.0), c2(3.0, 4.0);
    Complex c3 = c1 + c2; // 调用重载的 + 运算符
    c3.print();           // 输出: 4 + 6i
    return 0;
}
```

**只能使用成员函数重载的运算符有：=、()、[]、->。**

### 友元运算符重载

**友元运算符重载**是 C++ 中的一种特殊情况，允许我们将运算符函数定义为类的“友元函数”，使其可以访问类的私有成员。友元运算符函数的重载一般用于重载需要操作类成员的运算符，特别是二元运算符，如加法（`+`）或赋值（`=`）。

- 友元函数并不属于类的一部分，但它能够访问类的私有和保护成员。

- 友元运算符重载允许我们在类外部定义运算符的行为，并且能够访问类的私有成员。

一个重载的例子：

```c++
// 重载 >> 与 << ，输出复数
#include <iostream>
using namespace std;
class Complex {
private:
    double real, imag;
public:
    Complex(double r = 0, double i = 0) : real(r), imag(i) {}
    // 友元运算符重载
    friend ostream& operator<<(ostream &out, const Complex &c);
    friend istream& operator>>(istream &in, Complex &c);
};
// 输出流运算符重载
ostream& operator<<(ostream &out, const Complex &c) {
    out << c.real << " + " << c.imag << "i";
    return out;
}
// 输入流运算符重载
istream& operator>>(istream &in, Complex &c) {
    in >> c.real >> c.imag;
    return in;
}
int main() {
    Complex c1(1.0, 2.0);
    cout << "Complex number: " << c1 << endl;  // 调用重载的 << 运算符
    Complex c2;
    cout << "Enter a complex number (real and imaginary): ";
    cin >> c2;  // 调用重载的 >> 运算符
    cout << "You entered: " << c2 << endl;
    return 0;
}
```

**&gt;&gt;和<<只能使用友元函数重载。**

# 三、迭代器

## 1. 迭代器分多少种？不同容器对应的迭代器类型？

| **类型**       | **单向遍历** | **双向遍历** | **随机访问** | **读写能力** | **示例容器**                             |

| -------------- | ------------ | ------------ | ------------ | ------------ | ---------------------------------------- |

| 输入迭代器     | ✅            | ❌            | ❌            | 只读         | `std::istream`                           |

| 输出迭代器     | ✅            | ❌            | ❌            | 只写         | `std::ostream`                           |

| 前向迭代器     | ✅            | ❌            | ❌            | 读写         | `std::forward_list`                      |

| 双向迭代器     | ✅            | ✅            | ❌            | 读写         | `list`,`set`,`multiset`,`map`,`multimap` |

| 随机访问迭代器 | ✅            | ✅            | ✅            | 读写         | `vector`,`deque`                         |

队列（`queue`）和堆栈（`stack`）本身不提供迭代器，迭代器类型由其封装而成的容器决定。

`bitset`容器不提供迭代器，其访问由数组下标完成。

## 2.迭代器在STL中起到什么作用？

迭代器可以提供统一的方法访问容器中的元素。同时，迭代器可以使容器和算法分离，使算法更具有普适性。

## 3.迭代器如何访问对应元素及内部变量？

迭代器通过重载操作符（如 `*` 和 `++`）来访问容器中的元素和实现遍历。

* `*` 和 `->` 用于访问迭代器指向的元素或成员。

* `++` 用于移动迭代器。

* 随机访问迭代器支持额外的操作，如 `+`, `-`, 和 `[]`。

## 4.熟练使用迭代器的advance、distance、next函数，为何要使用这些函数？

### `advance`函数

将一个迭代器向前或向后移动指定的距离，支持任意类型的迭代器（前向、双向、随机访问）。

用法：

````c++
// 函数用法：advance(迭代器指针,移动距离)
#include <vector>
#include <iostream>
#include <iterator> // for std::advance
int main() {
    std::vector<int> vec = {10, 20, 30, 40, 50};
    auto it = vec.begin();
    std::advance(it, 3); // 将迭代器向前移动 3 个位置
    std::cout << *it << std::endl; // 输出: 40
    return 0;
}
````

### `distance`函数

计算两个迭代器之间的距离（即两个迭代器间元素的数量）。

用法：

```c++
// 函数用法：distance(迭代器指针1,迭代器指针2)
#include <vector>
#include <iostream>
#include <iterator> // for std::distance
int main() {
    std::vector<int> vec = {10, 20, 30, 40, 50};
    auto it1 = vec.begin();
    auto it2 = vec.end();
    std::cout << std::distance(it1, it2) << std::endl; // 输出: 5
    return 0;
}
```

### `next`函数

创建一个新的迭代器，指向当前迭代器的指定偏移位置，而不修改原来的迭代器。支持所有的迭代器。

用法：

```c++
// 函数用法：next(迭代器指针,偏移量)
#include <vector>
#include <iostream>
#include <iterator> // for std::next
int main() {
    std::vector<int> vec = {10, 20, 30, 40, 50};
    auto it = vec.begin();
    auto newIt = std::next(it, 2); // 获取当前迭代器之后第 2 个位置的迭代器
    std::cout << *newIt << std::endl; // 输出: 30
    return 0;
}
```

### 使用函数的原因

简单高效，易于阅读，可以避免出错。

# 四、输入输出流

## 1. 都有哪些流？

| **流类型**     | **功能描述**                                   | **常用类**                                                   |

| -------------- | ---------------------------------------------- | ------------------------------------------------------------ |

| 标准输入输出流 | 用于从控制台输入或输出到控制台。               | `std::cin`, `std::cout`,    |

| 文件流         | 用于文件的输入和输出操作。                     | `std::ifstream`, `std::ofstream`, `std::fstream`             |

| 字符串流       | 用于基于字符串的输入和输出操作（内存中的流）。 | `std::istringstream`, `std::ostringstream`, `std::stringstream` |

| 二进制流       | 用于处理二进制数据。                           | 通常使用 `std::ifstream` 和 `std::ofstream` 配合二进制模式。 |

## 2. 状态如何检测？

使用流的成员函数 `std::ios::good()`, `std::ios::fail()` 等可以检测流的状态。常用的状态位如下：

- **`std::ios::good()`**：流状态正常，无错误。

- **`std::ios::eof()`**：到达文件或输入的末尾。

- **`std::ios::fail()`**：输入或输出操作失败。

- **`std::ios::bad()`**：流发生不可恢复的错误。

示例代码如下：

```c++
#include <iostream>
#include <fstream>
int main() {
    std::ifstream file("example.txt");
    if (!file.is_open()) {
        std::cerr << "File could not be opened!" << std::endl;
        return 1;
    }
    int number;
    while (file >> number) {
        std::cout << "Read: " << number << std::endl;
    }
    if (file.eof()) {
        std::cout << "End of file reached." << std::endl;
    } else if (file.fail()) {
        std::cerr << "Input operation failed!" << std::endl;
    } else if (file.bad()) {
        std::cerr << "Critical error occurred!" << std::endl;
    }
    file.close();
    return 0;
}
```

## 3.`cin.get()`与`cin.getline()`使用

### `cin.get()`

- 用于从输入流中读取单个字符。

**示例代码**：

```c++
// 用法：cin.get()
include <iostream>
int main() {
    char c;
    std::cout << "Enter a character: ";
    c = std::cin.get(); // 读取单个字符
    std::cout << "You entered: " << c << std::endl;
    return 0;
}
```

### `cin.getline()`

- 用于从输入流中读取一行文本（直到遇到换行符），并丢弃换行符。

- 如果输入的字符数超过缓冲区大小，多余的字符会被丢弃。

**示例代码**：

```c++
// 用法：cin.getline(char类型的数组指针,读取的字符数量)
// char类型的数组指针用作缓冲区
#include <iostream>
int main() {
    char buffer[20];
    std::cout << "Enter a line: ";
    std::cin.getline(buffer, 20); // 读取最多 19 个字符，最后一个留给 '\0'
    std::cout << "You entered: " << buffer << std::endl;
    return 0;
}
```

## 4. `stringstream`相关操作举例

### 实现 `int`<——>`string` 转换

**示例代码**：

```c++
#include <iostream>
#include <sstream>
#include <string>
int main() {
    int num = 42;
    std::string str;
    // int -> string
    std::stringstream ss;
    ss << num; // 插入num到流中
    str = ss.str(); //提取为字符串赋值给str
    std::cout << "Integer to string: " << str << std::endl;
    // string -> int
    ss.clear(); // 清空流
    ss.str("123"); // 设置流的内容为字符串123
    ss >> num; // 提取流中数据，转换为整型变量赋值
    std::cout << "String to integer: " << num << std::endl;
    return 0;
}
```

### 字符串分解

可以将字符串分解为多个部分，例如按空格分隔。

**示例代码**：

```c++
#include <iostream>
#include <sstream>
#include <string>
int main() {
    std::string line = "10 20 30 40";
    std::stringstream ss(line); //包装字符串为流
    int number;
    while (ss >> number) { //提取每个数输出
        std::cout << "Extracted number: " << number << std::endl;
    }
    return 0;
}
```

### 字符串合并

将多个数据合并为一个字符串。

**示例代码**：

```c++
#include <iostream>
#include <sstream>
#include <string>
int main() {
    std::stringstream ss;
    ss << "Hello" << " " << "World" << "!" << " " << 2024; //插入字符到流中
    std::string result = ss.str(); //把流中字符全部输出
    std::cout << "Merged string: " << result << std::endl;
    return 0;
}
```

# 五、容器

## 1. 容器的分类

| 容器名称   | 容器概念                 | 对应容器                          |

| ---------- | ------------------------ | --------------------------------- |

| 序列性容器 | 按照线性排列储存值       | `vector`,`deque`,`list`           |

| 关联式容器 | 根据键值检索数据         | `set`,`multiset`,`map`,`multimap` |

| 容器适配器 | 根据前两种容器再封装得到 | `stack`,`queue`                   |

## 2. `vector`,`deque`

###  `vector`

#### 初始化

```c++
#include <vector>
// 默认构造
std::vector<int> v1;
// 指定大小初始化
std::vector<int> v2(10);  // 包含10个默认值为0的元素
// 指定大小和初始值
std::vector<int> v3(5, 100);  // 包含5个元素，值为100
// 使用初始化列表
std::vector<int> v4 = {1, 2, 3, 4, 5};
// 复制构造
std::vector v5(v4);
```

#### `push_back`

向`vector`末尾添加元素

```c++
v1.push_back(10);
v1.push_back(20);
```

#### `pop_back`

删除`vector`末尾的元素

```c++
v1.pop_back();
```

#### `insert`

在指定位置插入元素

```c++
v1.insert(v1.begin() + 1, 15);   // 在第2个位置插入元素15
v1.insert(v1.begin(), 5);        // 在第一个位置插入元素5
```

#### `erase`

删除指定位置的元素

```c++
v1.erase(v1.begin() + 1);  // 删除第2个元素
```

#### `assign`

重新指定`vector`的大小和元素值

```c++
v1.assign(3, 50);  // 将v1重置为包含3个元素，每个元素的值为50
```

#### 迭代器遍历

使用迭代器遍历`vector`

```c++
for (iterator it = v1.begin(); it != v1.end(); it++) {
    std::cout << *it << " ";
}
```

或者使用范围-based for 循环

```cpp
for (const auto& value : v1) {
    std::cout << value << " ";
}
```

### `deque`

#### 初始化

```c++
#include <deque>
// 默认构造
std::deque<int> dq1;
// 指定大小初始化
std::deque<int> dq2(10);  // 包含10个默认值为0的元素
// 指定大小和初始值
std::deque<int> dq3(5, 100);  // 包含5个元素，每个的值为100
// 使用初始化列表
std::deque<int> dq4 = {1, 2, 3, 4, 5};
// 复制构造
std::deque<int> dq5(dq4);
```

#### `push_back`

向`deque`末尾添加元素

```c++
dq1.push_back(10);
dq1.push_back(20);
```

#### `push_front`

向`deque`前端添加元素

```c++
dq1.push_front(5);
```

#### `pop_back`

删除`deque`末尾的元素

```c++
dq1.pop_back();
```

#### `pop_front`

删除`deque`前端的元素

```c++
dq1.pop_front();
```

##### `insert`

在指定位置插入元素

```c++
dq1.insert(dq1.begin() + 1, 15);   // 在第2个位置插入元素15
dq1.insert(dq1.begin(), 5);        // 在第一个位置插入元素5
```

#### `erase`

删除指定位置的元素

```c++
dq1.erase(dq1.begin() + 1);  // 删除第2个元素
```

#### `assign`

重新指定`deque`的大小和元素值

```c++
dq1.assign(3, 50);  // 将dq1重置为包含3个元素，每个元素的值为50
```

#### 迭代器遍历

使用迭代器遍历`deque`

```c++
for (iterator it = dq1.begin(); it != dq1.end(); it++) {
    std::cout << *it << " ";
}
```

## 3. `list`

### 特点

双向列表，使用双向迭代器，不支持随机访问，动态大小

### 结构

由一个个节点组成。每个节点包含指向前一个节点的指针和后一个节点的指针。

### 操作

#### 初始化

方法与前两个容器类似。

#### 插入/删除

可以使用`push`类方法与`pop`类方法。

此外还可以使用`insert`，使用指定位置进行插入：

```c++
iterator it = lst1.begin();
lst1.insert(it, 15);    // 在首位置插入15
lst1.insert(++it, 25);  // 在第二个位置插入25
```

使用`insert`也可以插入多个相同的元素：

```c++
iterator it = lst1.begin();
lst1.insert(it, 3, 100);  // 在首位置插入3个元素，值为100
```

对于删除操作，还可以使用以下的操作：

- `erase`：删除指定位置的元素

```c++
iterator it = lst1.begin();
lst1.erase(it);  // 删除指向首元素的迭代器位置的元素
```

`erase`操作也可以用来删除指定范围的元素：

```c++
iterator it1 = lst1.begin();
iterator it2 = ++lst1.begin();
lst1.erase(it1, it2);  // 删除从it1到it2之间的所有元素
```

- `remove`：删除所有与指定值匹配的元素（注意：`remove` 只是移除元素，但并不会真正删除，实际删除需要配合 `erase` 使用）

```c++
lst1.remove(20);  // 删除所有值为20的元素
```

- `clear`：删除所有元素

```c++
lst1.clear();  // 清空整个链表
```

#### 遍历

使用迭代器进行正向遍历：

```c++
for (iteartor it = lst1.begin(); it != lst1.end(); it++) {
    std::cout << *it << " ";  // 访问元素
}
```

`std::list` 支持双向迭代，可以用 `--it` 实现反向遍历：

```c++
for (iteartor it = lst1.end(); it != lst1.begin(); ) {
    --it;  // 向前移动
    std::cout << *it << " ";
}
```

#### 迭代器的插入与删除

- 在指定位置插入元素：

```c++
iterator it = lst1.begin();
lst1.insert(it, 100);  // 在迭代器位置插入100
```

- 删除指定位置的元素：

```c++
iterator it = lst1.begin();
lst1.erase(it);  // 删除迭代器指向的元素
```

## 4. `stack`、`queue`、`priority_queue`

### `stack`

#### 特点

- **后进先出（LIFO）**：`std::stack` 是一个栈，遵循后进先出（Last In, First Out）原则。即最后压入栈的元素最先被弹出。

- **限制访问**：`std::stack` 只允许从栈顶访问元素，并且只能执行 `push()`、`pop()` 和 `top()` 操作。不能像其他容器那样遍历栈中的元素。

- **底层容器**：`std::stack` 通常是基于 `deque` 或 `vector` 来实现的。

#### 初始化

```c++
#include <stack>
#include <deque>
#include <vector>
// 使用默认底层容器 deque
std::stack<int> s1;  // 空栈
// 使用指定的底层容器 deque
std::stack<int, std::deque<int>> s2;
// 使用指定的底层容器 vector
std::stack<int, std::vector<int>> s3;
// 使用初始化列表初始化栈
std::stack<int> s4;
s4.push(10);
s4.push(20);
```

#### 常用操作

- `push(value)`：将元素压入栈顶。

- `pop()`：弹出栈顶元素。

- `top()`：访问栈顶元素。

- `empty()`：检查栈是否为空。

- `size()`：获取栈中的元素数量。

```c++
std::stack<int> s;
s.push(10);
s.push(20);
std::cout << s.top() << std::endl;  // 输出 20
s.pop();
std::cout << s.top() << std::endl;  // 输出 10
```

#### 适合的底层容器

- **`deque`**：`deque` 是 `std::stack` 的默认底层容器，因为它支持高效的两端插入和删除操作。栈操作只在容器的一端进行，因此 `deque` 是理想选择。

- **`vector`**：`vector` 也可以作为底层容器，但由于 `push_back` 和 `pop_back` 操作的时间复杂度与容器大小有关（可能会触发重新分配内存），因此在某些情况下可能不如 `deque` 高效。

#### 遍历

利用`vector`容器来临时保存：

```c++
#include <iostream>
#include <stack>
#include <vector>
int main() {
    std::stack<int> s;
    s.push(10);
    s.push(20);
    s.push(30);
    // 使用临时容器来遍历栈
    std::vector<int> temp;
    while (!s.empty()) {
        temp.push_back(s.top());
        s.pop();
    }
    // 打印栈的元素（逆序）
    for (auto val : temp) {
        std::cout << val << " ";
    }
    return 0;
}
```

###  `queue`

#### 特点

- **先进先出（FIFO）**：`std::queue` 是一个队列，遵循先进先出（First In, First Out）原则。即最早加入队列的元素最先被删除。

- **限制访问**：`std::queue` 只允许从队列的前端删除元素（`pop()`）和从队列的后端添加元素（`push()`）。不能像其他容器那样直接访问或修改队列中的元素。

- **底层容器**：`std::queue` 通常是基于 `deque` 来实现的，但也可以使用其他容器，如 `list`。

#### 初始化

```c++
#include <queue>
#include <deque>
// 使用默认底层容器 deque
std::queue<int> q1;  // 空队列
// 使用指定的底层容器 deque
std::queue<int, std::deque<int>> q2;
// 使用指定的底层容器 list
std::queue<int, std::list<int>> q3;
```

#### 常用操作

- `push(value)`：将元素添加到队列的末尾。

- `pop()`：删除队列的前端元素。

- `front()`：访问队列的前端元素。

- `back()`：访问队列的末端元素。

- `empty()`：检查队列是否为空。

- `size()`：获取队列中的元素数量。

```c++
std::queue<int> q;
q.push(10);
q.push(20);
std::cout << q.front() << std::endl;  // 输出 10
q.pop();
std::cout << q.front() << std::endl;  // 输出 20
```

#### 适合的底层容器

- **`deque`**：`deque` 是 `std::queue` 的默认底层容器，因为它支持高效的两端插入和删除操作，适合队列操作。

- **`list`**：`list` 也是一个合适的底层容器，因为它在两端插入和删除操作的时间复杂度是 O(1)。

#### 遍历

利用`vector`容器进行操作：

```c++
#include <iostream>
#include <queue>
#include <vector>
int main() {
    std::queue<int> q;
    q.push(10);
    q.push(20);
    q.push(30);
    // 使用临时容器来遍历队列
    std::vector<int> temp;
    while (!q.empty()) {
        temp.push_back(q.front());
        q.pop();
    }
    // 打印队列的元素（顺序）
    for (auto val : temp) {
        std::cout << val << " ";
    }
    return 0;
}
```

### `priority_queue`

#### 特点

- **优先级队列**：`std::priority_queue` 是一种特殊类型的队列，其中元素按优先级排序。具有较高优先级的元素总是排在队列前面。

- **底层容器**：`std::priority_queue` 是基于堆（通常是二叉堆）实现的，因此它的插入和删除操作的时间复杂度是 O(log n)。

- **限制访问**：`std::priority_queue` 只允许访问队列的顶部元素（即最大或最小元素，取决于排序规则），无法直接访问其他元素。

#### 初始化

```c++
#include <queue>
#include <vector>
// 使用默认底层容器 vector，按默认的降序排序（最大堆）
std::priority_queue<int> pq1;
// 使用指定的底层容器 vector，按自定义的升序排序（最小堆）
std::priority_queue<int, std::vector<int>, std::greater<int>> pq2;
// 使用自定义比较器
struct Compare {
    bool operator()(int a, int b) {
        return a > b;  // 升序排列
    }
};
std::priority_queue<int, std::vector<int>, Compare> pq3;
```

#### 常用操作

- `push(value)`：将元素插入到优先级队列中。

- `pop()`：删除队列顶部的元素。

- `top()`：访问队列顶部的元素（优先级最高的元素）。

- `empty()`：检查队列是否为空。

- `size()`：获取队列中的元素数量。

```c++
std::priority_queue<int> pq;
pq.push(10);
pq.push(20);
pq.push(5);
std::cout << pq.top() << std::endl;  // 输出 20
pq.pop();
std::cout << pq.top() << std::endl;  // 输出 10
```

#### 适合的底层容器

- **`vector`**：`std::priority_queue` 默认使用 `vector` 作为底层容器，基于堆实现。`vector` 提供了良好的内存布局和随机访问特性。

- **`deque`**：`deque` 也可以作为底层容器，但通常情况下，`vector` 会是更高效的选择。

#### 遍历

利用`vector`容器进行操作：

```c++
#include <iostream>
#include <queue>
#include <vector>
int main() {
    std::priority_queue<int> pq;
    pq.push(10);
    pq.push(20);
    pq.push(5);
    // 使用临时容器来遍历 priority_queue
    std::vector<int> temp;
    while (!pq.empty()) {
        temp.push_back(pq.top());
        pq.pop();
    }
    // 打印队列的元素（优先级顺序）
    for (auto val : temp) {
        std::cout << val << " ";
    }
    return 0;
}
```

## 5. `bitset`容器

`bitset`是一个存放二进制数的容器。

### 初始化

**默认初始化**（所有位为0）：

```c++
std::bitset<8> b;  // 默认所有位为0
```

**使用整数初始化**： 你可以使用整数（例如 `unsigned int`）来初始化 `bitset`，这将根据整数的二进制表示来设置对应的位。

```c++
std::bitset<8> b(42);  // b = 00101010 (二进制表示42)
```

**使用字符串初始化**： 你也可以使用一个二进制字符串（必须只包含 `0` 和 `1`）来初始化 `bitset`，并且字符串的长度必须不超过 `bitset` 的大小。

```c++
std::bitset<8> b("10101010");  // b = 10101010
```

**使用部分初始化**： 如果初始化的字符串长度小于 `bitset` 的大小，剩余的位将被设置为 `0`。

```c++
std::bitset<8> b("101");  // b = 00000101
```

### `to_string`的使用

`to_string()` 函数将 `bitset` 转换为一个包含二进制表示的字符串。该字符串由字符 `'0'` 和 `'1'` 构成，表示 `bitset` 中的位。

### 示例：

```c++
#include <iostream>
#include <bitset>
#include <string>
int main() {
    std::bitset<8> b(42);  // b = 00101010 (二进制表示42)
    std::string binaryString = b.to_string();
    std::cout << "Binary string: " << binaryString << std::endl;  // 输出: 00101010
    return 0;
}
```

### `to_ulong`的使用

`to_ulong()` 函数将 `bitset` 转换为 `unsigned long` 类型的数值。它返回 `bitset` 对应的整数值。如果 `bitset` 的大小超过了 `unsigned long` 能表示的最大位数，使用 `to_ulong()` 会抛出一个 `std::overflow_error` 异常。

### 示例：

```c++
#include <iostream>
#include <bitset>
int main() {
    std::bitset<8> b(42);  // b = 00101010 (二进制表示42)
    unsigned long num = b.to_ulong();
    std::cout << "Unsigned long: " << num << std::endl;  // 输出: 42
    return 0;
}
```

# 6. 关联性容器

## 数据结构特点，迭代器

使用平衡二叉树。对于`set`，`multiset`，`map`，`multimap`，使用双向迭代器。

## 集合，映射容器的相同与区别

### `set`与`multiset`

二者默认都按照升序排列。

在初始化时，可以通过构造函数的参数来更改排列方式：

```c++
// set 默认升序排序
set<int> s = {3, 1, 4, 2};
for (int x : s) cout << x << " ";  // 输出：1 2 3 4
cout << endl;
// 自定义降序排序
set<int, greater<int>> s_desc = {3, 1, 4, 2};
for (int x : s_desc) cout << x << " ";  // 输出：4 3 2 1
cout << endl;
```

前者不允许重复值，后者允许。

### `map`与`multimap`

二者默认按照升序排列。在初始化时，也可以利用类似于`set`的方法进行自定义排序。

前者为一对一排列，后者为一对多排列。前者不允许重复值，后者允许。

### 初始化方法

#### `set`与`multiset`

二者初始化方法类似。以`set`举例：

```c++
#include <iostream>
#include <set>
using namespace std;
int main() {
    // 1. 空初始化
    set<int> s;
	// 2. 列表初始化
	set<int> s1 = {3, 1, 4, 2};
	// 3. 复制初始化
	set<int> s2(s1);  // 复制另一个 set
	// 4. 通过范围初始化
	int arr[] = {5, 6, 7};
	set<int> s3(arr, arr + 3);
	// 5. 自定义比较器初始化（降序）
	set<int, greater<int>> s4 = {3, 1, 4, 2};
	// 输出
	for (int x : s4) cout << x << " ";  // 输出：4 3 2 1
	return 0;
}
```

#### `map`与`multimap`

二者初始化方法类似。以`map`举例：

```c++
#include <iostream>
#include <map>
using namespace std;
int main() {
    // 1. 空初始化
    map<int, string> m;
    // 2. 列表初始化
    map<int, string> m1 = {{1, "one"}, {2, "two"}, {3, "three"}};
    // 3. 复制初始化
    map<int, string> m2(m1);  // 复制另一个 map
    // 4. 通过范围初始化
    pair<int, string> arr[] = {{4, "four"}, {5, "five"}};
    map<int, string> m3(arr, arr + 2);
    // 5. 自定义比较器初始化（降序）
    map<int, string, greater<int>> m4 = {{1, "one"}, {2, "two"}};
    // 输出
    for (auto &p : m4) cout << p.first << ": " << p.second << endl;  // 输出：2: two 1: one
    return 0;
}
```

### 添加新数据

#### `set`与`multiset`

1. **使用 `insert()` 方法**

   - `set`/`multiset` 都可以使用 `insert()` 方法添加新元素。

   - 返回值：

     - 对于 `set`：返回 `pair<iterator, bool>`，`bool` 表示是否插入成功。

     - 对于 `multiset`：返回插入的元素位置的迭代器。

2. **通过范围插入**

   - 可以使用 `insert` 将其他容器或范围的元素插入到当前容器中。

**示例代码**：

```c++
#include <iostream>
#include <set>
using namespace std;
int main() {
    // 初始化 set 和 multiset
    set<int> s = {1, 2, 3};
    multiset<int> ms = {1, 2, 3};
    // 添加新元素到 set
    auto result = s.insert(4);  // 插入元素 4
    cout << "Insert 4 into set: " << (result.second ? "Success" : "Failure") << endl; // 输出：Success
    result = s.insert(2);  // 插入元素 2（已存在）
    cout << "Insert 2 into set: " << (result.second ? "Success" : "Failure") << endl; // 输出：Failure
    // 添加新元素到 multiset
    ms.insert(4);  // 插入元素 4
    ms.insert(2);  // 插入重复的元素 2
    // 输出 set 和 multiset 的元素
    cout << "Set elements: ";
    for (int x : s) cout << x << " ";  // 输出：1 2 3 4
    cout << endl;
    cout << "Multiset elements: ";
    for (int x : ms) cout << x << " ";  // 输出：1 2 2 3 4
    cout << endl;
    return 0;
}
```

#### `map`与`multimap`

1. **使用 `insert()` 方法**

   - 插入一个 `pair` 类型的键值对。

   - 返回值：

     - 对于 `map`：返回 `pair<iterator, bool>`，`bool` 表示插入是否成功。

     - 对于 `multimap`：返回迭代器，指向插入元素的位置。

2. **通过 `[]` 运算符添加数据（仅限 `map`）**

   - `map` 可以使用 `[]` 直接插入键值对。

   - 如果键已存在，会更新对应的值；如果键不存在，会创建新的键值对。

示例代码：

```c++
#include <iostream>
#include <map>
using namespace std;
int main() {
    // 初始化 map 和 multimap
    map<int, string> m = {{1, "one"}, {2, "two"}};
    multimap<int, string> mm = {{1, "one"}, {2, "two"}};
    // 添加新元素到 map
    auto result = m.insert({3, "three"});  // 插入键值对 {3, "three"}
    cout << "Insert {3, 'three'} into map: " << (result.second ? "Success" : "Failure") << endl; // 输出：Success
    result = m.insert({2, "duplicate"});  // 插入已存在的键 2
    cout << "Insert {2, 'duplicate'} into map: " << (result.second ? "Success" : "Failure") << endl; // 输出：Failure
    // 使用 [] 添加或更新元素（仅限 map）
    m[4] = "four";  // 添加键值对 {4, "four"}
    m[2] = "updated";  // 更新键 2 的值
    // 添加新元素到 multimap
    mm.insert({3, "three"});  // 插入键值对 {3, "three"}
    mm.insert({2, "duplicate"});  // 插入重复的键 2
    // 输出 map 和 multimap 的元素
    cout << "Map elements: " << endl;
    for (auto &p : m) {
        cout << p.first << ": " << p.second << endl;  // 输出键值对
    }
    cout << "Multimap elements: " << endl;
    for (auto &p : mm) {
        cout << p.first << ": " << p.second << endl;  // 输出键值对
    }
    return 0;
}
```

### `lower_bound`和`upper_bound`的使用

- **`lower_bound(key)`**：返回第一个 **不小于（大于或等于）键 `key`** 的迭代器。

- **`upper_bound(key)`**：返回第一个 **大于键 `key`** 的迭代器。

**示例代码：**

```c++
#include <iostream>
#include <set>
#include <map>
using namespace std;
int main() {
    //set 示例
    set<int> s = {1, 2, 4, 5};
    auto lb = s.lower_bound(3);  // 找到第一个 >= 3 的元素
    auto ub = s.upper_bound(3);  // 找到第一个 > 3 的元素
    cout << "lower_bound(3): " << (lb != s.end() ? to_string(*lb) : "not found") << endl;
    cout << "upper_bound(3): " << (ub != s.end() ? to_string(*ub) : "not found") << endl;
    // map 示例
    // 在 map 中，主要是和键值对里的 key 比较
    map<int, string> m = {{1, "one"}, {2, "two"}, {4, "four"}, {5, "five"}};
    auto mlb = m.lower_bound(3);
    auto mub = m.upper_bound(3);
    cout << "map lower_bound(3): " << (mlb != m.end() ? to_string(mlb->first) : "not found") << endl;
    cout << "map upper_bound(3): " << (mub != m.end() ? to_string(mub->first) : "not found") << endl;
    return 0;
}
```

### 关联性容器的遍历

#### `set`与`multiset`

```c++
#include <iostream>
#include <set>
int main() {
    std::set<int> mySet = {1, 2, 3, 4, 5};
    // 使用迭代器遍历
    for (auto it = mySet.begin(); it != mySet.end(); ++it) {
        std::cout << *it << " ";
    }
    std::cout << std::endl;
    return 0;
}
```

#### `map`与`multimap`

```c++
#include <iostream>
#include <map>
int main() {
    std::map<int, std::string> myMap = {{1, "one"}, {2, "two"}, {3, "three"}};
    // 使用迭代器遍历
    for (auto it = myMap.begin(); it != myMap.end(); ++it) {
        std::cout << it->first << ": " << it->second << std::endl;
    }
    return 0;
}
```

## 7. `string`容器

### 初始化方法

#### **使用字符常量初始化**

```c++
std::string str = "Hello, World!";
```

这里 `str` 被初始化为一个包含 `"Hello, World!"` 的字符串。

#### **使用字符数组初始化**

```c++
const char* arr = "Hello, World!";
std::string str(arr);
```

这种方式将一个 `const char*` 类型的 C 风格字符串转换为 `std::string`。

#### **指定长度初始化**

```c++
std::string str(10, 'a');  // 创建一个包含 10 个字符 'a' 的字符串
```

这会创建一个长度为 10 的字符串，所有字符都是 `'a'`。

#### **使用子字符串初始化**

```c++
std::string str = "Hello, World!";
std::string subStr(str, 7, 5);  // 从位置 7 开始，提取 5 个字符
// subStr 将是 "World"
```

这种方法可以从现有字符串中提取一个子字符串。

#### **拷贝构造初始化**

```c++
std::string str1 = "Hello";
std::string str2 = str1;  // 通过拷贝构造函数初始化
```

通过拷贝另一个字符串初始化新的字符串。

### 常见操作

#### **`insert` 操作**

`insert` 用于在指定位置插入字符或字符串。

```c++
std::string str = "Hello, World!";
str.insert(5, " Beautiful");  // 在索引 5 位置插入 " Beautiful"
std::cout << str << std::endl;  // 输出 "Hello Beautiful, World!"
```

**说明：**

- `insert(pos, str)`：在 `pos` 位置插入字符串 `str`。

- `insert(pos, n, ch)`：在 `pos` 位置插入 `n` 个字符 `ch`。

#### **`replace` 操作**

`replace` 用于替换指定位置的子字符串。

```c++
std::string str = "Hello, World!";
str.replace(7, 5, "C++");  // 从位置 7 开始替换 5 个字符，替换成 "C++"
std::cout << str << std::endl;  // 输出 "Hello, C++!"
```

**说明：**

- `replace(pos, len, str)`：从 `pos` 位置开始，替换 `len` 长度的字符为 `str`。

####  **`erase` 操作**

`erase` 用于删除指定位置的字符或子字符串。

```c++
std::string str = "Hello, World!";
str.erase(5, 7);  // 从位置 5 开始删除 7 个字符
std::cout << str << std::endl;  // 输出 "Hello"
```

**说明：**

- `erase(pos, len)`：从 `pos` 位置开始删除 `len` 长度的字符。

- `erase(pos)`：删除从 `pos` 开始直到字符串末尾的所有字符。

### `string` 与 `stringstream`的结合

类型转换、字符串解析和数字字符串化等。

# 六、函数对象

## 1. 函数对象的概念

### 概念

**函数对象（Function Object）**是一个重载了`operator()`运算符的类或结构体的实例。

当我们对函数对象使用`()`操作时，实际上调用的是该对象的`operator()`函数。

比如这个例子：

```c++
class MyFunctor {
    public:
    int operator()(int x, int y) {
        return x + y;
    } // 重载()运算符
};
MyFunctor func;
int result = func(3, 5); // 等价于调用 func.operator()(3, 5)，result == 8
```

### 优点

1. **灵活性高**

   函数对象比普通函数更灵活，可以通过成员变量保存状态。

2. **效率更高**

   编译器可以内联函数对象的调用，从而减少函数调用的开销。

3. **便于与算法结合**

   函数对象可以作为参数传递给标准库算法，增强算法的通用性和可配置性。

## 2. 函数对象的分类

| 分类             | 定义                     | 示例              |

| ---------------- | ------------------------ | ----------------- |

| **发生器**       | 无参数，生成值           | `RandomGenerator` |

| **一元函数**     | 接受一个参数，返回一个值 | `Square`          |

| **一元判定函数** | 接受一个参数，返回布尔值 | `IsOdd`           |

| **二元函数**     | 接受两个参数，返回一个值 | `Add`             |

| **二元判定函数** | 接受两个参数，返回布尔值 | `Greater`         |

## 3.函数对象与容器结合使用举例

### 使用函数对象对容器排序

```
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
class Descending {
public:
    bool operator()(int x, int y) const {
        return x > y; // 降序排序规则
    }
};
int main() {
    vector<int> vec = {3, 1, 4, 1, 5, 9};
    // 使用函数对象 Descending 排序
    sort(vec.begin(), vec.end(), Descending());
    for (vector<int>::iterator it = vec.begin(); it != vec.end(); ++it) {
        cout << *it << " "; // 输出：9 5 4 3 1 1
    }
    return 0;
}
```

### 用函数对象筛选容器元素

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
class IsEven {
public:
    bool operator()(int x) const {
        return x % 2 == 0; // 判断是否为偶数
    }
};
int main() {
    vector<int> vec = {3, 1, 4, 1, 5, 9, 2, 6, 5};
    // 移除所有偶数
    vector<int>::iterator new_end = remove_if(vec.begin(), vec.end(), IsEven());
    vec.erase(new_end, vec.end());
    for (vector<int>::iterator it = vec.begin(); it != vec.end(); ++it) {
        cout << *it << " "; // 输出：3 1 1 5 9 5
    }
    return 0;
}
```

## 4.函数对象与算法结合使用举例

### 结合 `std::transform`

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
class Square {
public:
    int operator()(int x) const {
        return x * x; // 返回平方
    }
};
int main() {
    vector<int> vec = {1, 2, 3, 4};
    vector<int> result(vec.size());
    // 使用函数对象 Square 对每个元素求平方
    transform(vec.begin(), vec.end(), result.begin(), Square());
    for (vector<int>::iterator it = result.begin(); it != result.end(); ++it) {
        cout << *it << " "; // 输出：1 4 9 16
    }
    return 0;
}
```

### 结合 `std::accumulate`

```c++
#include <iostream>
#include <vector>
#include <numeric>
using namespace std;
class Add {
public:
    int operator()(int x, int y) const {
        return x + y; // 执行加法操作
    }
};
int main() {
    vector<int> vec = {1, 2, 3, 4};
    // 使用函数对象 Add 计算累加和
    int sum = accumulate(vec.begin(), vec.end(), 0, Add());
    cout << "Sum: " << sum << endl; // 输出：Sum: 10
    return 0;
}
```

## 5.系统函数对象

### **算术运算函数对象**

- `plus<T>`：加法

- `minus<T>`：减法

- `multiplies<T>`：乘法

- `divides<T>`：除法

- `modulus<T>`：取模

- `negate<T>`：取反

### **关系运算函数对象**

- `greater<T>`：大于

- `less<T>`：小于

- `greater_equal<T>`：大于等于

- `less_equal<T>`：小于等于

- `equal_to<T>`：等于

- `not_equal_to<T>`：不等于

### **逻辑运算函数对象**

- `logical_and<T>`：逻辑与

- `logical_or<T>`：逻辑或

- `logical_not<T>`：逻辑非

# 七、算法

## 1. 常用算法举例

### `count_if`

- 功能：统计容器中满足特定条件的元素个数。

- 函数原型：

  ```c++
  template <class InputIterator, class Predicate>
  typename iterator_traits<InputIterator>::difference_type
  count_if(InputIterator first, InputIterator last, Predicate pred);
  ```

  - `first, last`: 范围的起止迭代器。

  - `pred`: 条件谓词。

**示例：统计偶数个数**

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
class IsEven {
public:
    bool operator()(int x) const {
        return x % 2 == 0;
    }
};
int main() {
    vector<int> vec = {1, 2, 3, 4, 5, 6};
    // 使用 count_if 统计偶数个数
    int even_count = count_if(vec.begin(), vec.end(), IsEven());
    cout << "Even numbers: " << even_count << endl; // 输出：Even numbers: 3
    return 0;
}
```

### `count`

- 功能：统计容器中与指定值相等的元素个数。

- 函数原型：

  ```c++
  template <class InputIterator, class T>
  typename iterator_traits<InputIterator>::difference_type
  	count(InputIterator first, InputIterator last, const T &value);
  ```

  - `first, last`: 范围的起止迭代器。

  - `value`: 要统计的目标值。

**示例：统计数字 3 的出现次数**

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
int main() {
    vector<int> vec = {1, 3, 3, 4, 5, 3};
    // 使用 count 统计 3 的个数
    int count_three = count(vec.begin(), vec.end(), 3);
    cout << "Number of 3s: " << count_three << endl; // 输出：Number of 3s: 3
    return 0;
}
```

### `find`

- 功能：在容器范围中查找第一个与指定值相等的元素，返回迭代器。

- 函数原型：

  ```c++
  template <class InputIterator, class T>
  	InputIterator find(InputIterator first, InputIterator last, const T &value);
  ```

  - `first, last`: 范围的起止迭代器。

  - `value`: 要查找的目标值。

**示例：查找第一个值为 4 的位置**

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
int main() {
    vector<int> vec = {1, 2, 3, 4, 5};
    // 使用 find 查找值为 4 的元素
    vector<int>::iterator it = find(vec.begin(), vec.end(), 4);
    if (it != vec.end()) {
        cout << "Found 4 at position: " << distance(vec.begin(), it) << endl; // 输出：Found 4 at position: 3
    } else {
        cout << "4 not found" << endl;
    }
    return 0;
}
```

### `find_end`

- 功能：查找容器范围内最后一个子序列的位置。

- 函数原型：

  ```c++
  template <class ForwardIterator1, class ForwardIterator2>
  	ForwardIterator1 find_end(ForwardIterator1 first1, ForwardIterator1 last1, ForwardIterator2 first2, ForwardIterator2 last2);
  ```

  - `first1, last1`: 容器范围。

  - `first2, last2`: 子序列范围。

**示例：查找子序列 `{3, 4}` 的最后出现位置：**

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
int main() {
    vector<int> vec = {1, 2, 3, 4, 3, 4, 5};
    vector<int> sub = {3, 4};
    // 使用 find_end 查找子序列的最后位置
    vector<int>::iterator it = find_end(vec.begin(), vec.end(), sub.begin(), sub.end());
    if (it != vec.end()) {
        cout << "Last occurrence starts at position: " << distance(vec.begin(), it) << endl; // 输出：Last occurrence starts at position: 4
    } else {
        cout << "Subsequence not found" << endl;
    }
    return 0;
}
```

### `adjacent_find`

- 功能：查找范围中相邻两个相等元素的**第一个**位置，或满足特定条件的相邻元素。

- 函数原型：

  ```c++
  template <class ForwardIterator>
  	ForwardIterator adjacent_find(ForwardIterator first, ForwardIterator last);
  template <class ForwardIterator, class BinaryPredicate>
  	ForwardIterator adjacent_find(ForwardIterator first, ForwardIterator last,BinaryPredicate pred);
  ```

  - `pred`: 二元谓词，用于自定义相邻元素匹配条件。

**查找相邻相等元素：**

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
int main() {
    vector<int> vec = {1, 2, 2, 3, 4};
    // 使用 adjacent_find 查找相邻相等元素
    vector<int>::iterator it = adjacent_find(vec.begin(), vec.end());
    if (it != vec.end()) {
        cout << "Adjacent equal elements found at position: " << distance(vec.begin(), it) << endl; // 输出：Adjacent equal elements found at position: 1
    } else {
        cout << "No adjacent equal elements" << endl;
    }
    return 0;
}
```

**示例 2：查找相邻满足条件的元素：**

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
// 自定义二元谓词：相邻元素差值大于 2
// 使用自定义函数对象
class DiffGreaterThanTwo {
public:
    bool operator()(int x, int y) const {
        return abs(x - y) > 2; // 绝对值大于 2
    }
};
int main() {
    vector<int> vec = {1, 3, 6, 7};
    // 使用 adjacent_find 查找相邻差值大于 2 的元素
    vector<int>::iterator it = adjacent_find(vec.begin(), vec.end(), DiffGreaterThanTwo());
    if (it != vec.end()) {
        cout << "Adjacent elements with difference > 2 found at position: " << distance(vec.begin(), it) << endl; // 输出：Adjacent elements with difference > 2 found at position: 1
    } else {
        cout << "No such adjacent elements" << endl;
    }
    return 0;
}
```

## 2. 使用`copy`算法实现容器遍历输出

**代码示例：**

```c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <iterator>
using namespace std;
int main() {
    // 定义一个容器
    vector<int> vec = {1, 2, 3, 4, 5};
    // 使用 std::copy 和 std::ostream_iterator 遍历输出
    cout << "Container elements: ";
    copy(vec.begin(), vec.end(), ostream_iterator<int>(cout, " "));
    cout << endl;
    return 0;
```
