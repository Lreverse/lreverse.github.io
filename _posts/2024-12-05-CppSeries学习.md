---
title: CppSeries学习
date: 2024-12-05 16:58:23 +0800
categories: [Coding, c++]
tag: [c++]
math: true
mermaid: true
pin: true
---

笔者之前虽然使用c++写过项目，但感觉理解得仍不够深入。无意中发现了Cherno的这个教学系列，讲得非常好，覆盖了许多容易忽略的细节。除此之外，Cherno还会聊聊自己的开发经验和工作心得。总之，这是一个非常不错的视频系列。所以，与其干巴巴地看视频，不如动手记录一下。<br>
- [Cherno的C++系列(youtube)](https://youtu.be/18c3MTX0PK0?si=3IzWRhCDf4Rwe5eW)
- [b站搬运](https://www.bilibili.com/video/BV1uy4y167h2/?share_source=copy_web&vd_source=0f3ee5e33df078685d17452bf62e942f)


> c++基本特性：**封装**，**继承**，**多态**
{: .prompt-info }



### 翻译单元

> Translation Unit

一个源文件以及它所包含的所有头文件经过预处理后的结果


### 语法糖

> Syntactic sugar

语法糖是编程语言中的一个术语，它指的是在编程语言中添加的某种语法，这种语法对语言的功能没有实质性的扩展，但可以是代码更加简洁、易读或者更符合程序员的编程习惯


### 指针声明

```cpp
int* p1, p2;
```
这里`p1`是`int`型指针，但是`p2`是`int`变量

```cpp
int *p1, *p2;
```
这里的`p1`、`p2`都是指针


### struct和class的区别

class的默认访问权限是`private`的，而struct的默认访问权限是`public`，仅此而已。之所以c++要保留struct的，是因为要与c兼容。

实际的使用情况通常是取决于程序员的编程风格
- 当谈到plain old data（POD），更多使用struct类型，用来表示一些变量的结构。
- 继承更多使用class，通常会拥有更强大的功能


### static关键字

1. 在类或结构体外部使用
	- 只对定义它的翻译单元可见
		- 该变量的作用域仅限于当前文件或当前函数内，不会被其他文件或函数访问
	- 全局静态变量和局部静态变量
		- 初始化的静态变量会在数据段分配内存，未初始化的静态变量会在BSS段分配内存。直到程序结束，静态变量始终会维持前值。只不过全局静态变量和局部静态变量的作用域不一样

2. 在类或结构体内部使用
	- 静态变量
		- 该静态数据成员拥有一块单独的存储区，所有类的实例共享这一块内存
	- 静态方法
		- 没有类实例
		- 不能访问非静态变量


### 阻止类被实例化

1. 把构造函数设置为`private`
2. 把构造函数声明为`delete`（C++提供默认构造函数）


### 虚函数

虚函数引入了一种叫做动态联编（Dynamic Dispatch），它通过虚函数表（v-table）来实现编译。

- 当一个类包含虚函数时，编译器会为每个类创建一个虚函数表，表中存储了指向类中所有虚函数的指针。
- 每个对象都包含一个指向该类虚函数表的指针。


### 纯虚函数

```cpp
class A
{
public:
	virtural std::string GetClassName() = 0;
}
```

如果类中至少有一个函数被声明为纯虚函数，则这个类就是**抽象类**。抽象类给其他类提供一个可以继承的基类。其不能被用于实例化对象，只能作为**接口**使用。（若要实例化派生类，则需要在派生类里实现每一个纯虚函数）


### 常量指针和指针常量

常量指针：`const*`
指针常量：`*const`

```cpp
// 指向的数是一个常量
const int* a;
int const* b;

// 这个指针是个常量
int *const c;

// 两者结合
const int* const d;
```


### const和mutable

`const`可以用来修饰成员函数，称为常成员函数。这种函数承诺在其执行过程中不会修改它所操作的对象的状态

```cpp
class Entity
{
private:
	int m_x;
public:
	int GetX() const 
	{
		return m_x;
	}
}
```

- 在函数体内部，不能修改对象的数据成员，除非这些数据成员被声明为`mutable`
- 阅读代码的人可以很容易地知道这个函数不会修改对象的状态
- 当一个对象被声明为`const`时，只能调用它的`const`成员函数

标记类成员为`mutable`，说明类中使用`const`修饰的方法可以修改这个成员

```cpp
class Entity
{
private:
	int m_x;
	mutable int m_DebugCount = 0;
public:
	const int& GetX() const
	{
		m_DebugCount++;
		return m_x;
	}
}
```


### 成员初始化列表

按照在类中声明的顺序进行初始化

```cpp
class Entity
{
private:
	int m_x;
	int m_y;
public:
	Entity(int x, int y) : m_x(x), m_y(y) {} 
}
```


### 栈和堆

在堆上创建对象：
- 若对象太大
- 需要显式地控制对象的生存周期

在栈上创建对象：
- 速度更快
- 自动释放内存


### 隐式转换与explicit关键字

隐式转换，也可以叫做隐式构造函数，或隐式地调用构造函数。（通常只允许进行一次隐式转换，多次将报错）

```cpp
class Entity
{
private:
	m_x;
public:
	Entity(int x) : m_x(x) {}
}

int main()
{
	Entity e1(12);
	Entity e2 = 8;  // 这里会隐式地调用构造函数，将其转换成Entity类型的对象
}
```

`explicit`关键字禁用隐式转换，可以防止意外转换

```cpp
class Entity
{
private:
	m_x;
public:
	explicit Entity(int x) : m_x(x) {}
}

int main()
{
	Entity e1(12);
	Entity e2 = 8;  // 该语句将不被允许，会报错
}
```


### 函数重载和运算符重载

函数重载：可以有多个同名函数，但它们的参数列表（参数的个数、类型或者顺序）不同。编译器会根据调用函数时提供的实际参数来确定要调用的具体函数版本

```cpp
int add(int a, int b)
{
	return a + b;
}

float add(float a, float b)
{
	return a + b;
}
```

运算符重载：可以重新定义运算符，实现自定义类型的运算

```cpp
struct Vector2
{
	float x, y;

	Vector2(float x, float y) : x(x), y(y) {}
	
	Vector2 Add(const Vector2& other) 
	{
		return Vector2(x + other.x, y + other.y);
	}
	
	Vector2 operator+(const Vector2& other) const
	{
		return Add(other);
	}
	
	bool operator==(const Vector2& other) const
	{
		return x == other.x && y == other.y;
	}
	
	bool operator!=(const Vector2& other) const
	{
		return !(*this == other);
	}
}

std::ostream& operator<<(std::ostream& stream, const Vector2& other)
{
	stream << other.x << ", " << other.y;
	return stream;
}

int main()
{
	Entity e1(1, 3);
	Entity e2(2, 5);

	Entity result1 = e1.Add(e2);
	Entity result2 = e1 + e2;

	if (result1 == result2) {}

	std::cout << result1 << result2 << std::endl;
}
```

说明：
- `std::cout`是`ostream`的类型的对象
- 返回值是 `ostream&`（输出流的引用），这样做的目的是为了能够实现链式输出。`cout << a`返回`cout`对象本身，这样就能继续进行下一个`<<`操作


### this指针

`this`指针指向当前正在调用成员函数的对象。其在非静态成员函数中是隐含存在的，而在静态成员函数中是不存在的，因为静态成员函数不依赖于具体的对象实例，没有对象实例也就没有`this`指针


### 智能指针

使用普通指针的话，需要手动分配内存，并在使用完后释放内存。如果忘记释放内存，就会导致内存泄漏；而如果对同一块内存进行多次释放，可能会产生错误。所以使用智能指针可以帮助我们更方便、更安全地管理动态分配的内存。
- 在`<memory>`库中
- C++11引入

基本原理：作用域指针

```cpp
class Entity
{
public:
	Entity();
	~Entity(); 
}

class ScopedPtr
{
private:
	Entity* m_Ptr;
public:
	ScopedPtr(Entity* ptr) : m_Ptr(ptr) {}
	~ScopedPtr() { delete m_ptr; }
}

int main()
{
	{
		ScopedPtr e = new Entity();
		// ScopedPtr e(new Entity());
	}

	/*
		这个e是在栈上分配的，当它离开作用域时，e会被自动删除，并调用析构函数
		所以e指向的Entity对象也会被删除
	*/
}
```

`unique_ptr`

一个对象只能被一个`unique_ptr`所拥有，当这个`unique_ptr`被销毁时，它所指向的对象也会随之被销毁。

```cpp
#include <memory>

int main()
{
	{
		std::unique_ptr<Entity> ptr = std::make_unique<Entity>();  // 异常安全
		// std::unique_ptr<Entity> ptr(new Entity());
	}
	// 离开作用域后，对象被销毁
}
```

这个的原理实际上是把拷贝构造函数给删除了，所以不允许复制，只能有一个指针

```cpp
class ScopedPtr
{
	...
public:
	ScopedPtr(const ScopedPtr& other) = delete;
	...
}
```


`shared_ptr`

多个`shared_ptr`可以同时指向同一个对象，对象的生命周期会一直延续，直到最后一个指向它的`shared_ptr`被销毁。（内部使用**引用计数**来跟踪有多少个智能指针指向同一个对象）

```cpp
#include <memory>

int main()
{
	std::shared_ptr<Entity> ptr1;
	{
		std::shared_ptr<Entity> ptr2 = std::make_shared<Entity>();
		ptr1 = ptr2;   // 引用计数=2
	}
	// 这里引用计数=1，所以不会销毁对象
}
```

`weak_ptr`

`weak_ptr`是一种辅助`shared_ptr`的智能指针，它不会增加引用计数。它可以指向一个由`shared_ptr`管理的对象，但它本身不拥有该对象的所有权。

```cpp
#include <memory>

int main()
{
	std::weak_ptr<Entity> ptr1;
	{
		std::shared_ptr<Entity> ptr2 = std::make_shared<Entity>();
		ptr1 = ptr2;   // 引用计数=1
	}
	// 这里引用计数=0，对象被销毁
}
```


### 友元函数

类的友元函数定义在类的外部，有权访问类的私有成员和保护成员。要声明为类的友元函数，则需要在类中使用`friend`修饰该函数声明。

```cpp
class Entity
{
private:
	m_x;
public:
	Entity(int x) : m_x(x) {}
	friend void printEntity(Entity entity);
}

void printEntity(Entity entity)
{
	std::cout << entity.m_x << std::endl;
}
```


### 拷贝构造函数

用来创建一个新对象，这个新对象是另一个已有对象的副本。

```cpp
class Entity
{
private:
	m_x;
public:
	Entity(int x) : m_x(x) {}
	Entity(const Entity& other);  // 拷贝构造函数
}
```

调用的场景：
1. 对象初始化
2. 函数参数传递（按值传递）
3. 函数返回值

```cpp
Entity e1(6)
Entity e2 = e1;  // 调用拷贝构造函数

void func1(Entity e) { ... }
fun1(e1);   // 创建一个副本e，并调用拷贝构造函数

Entity func2() { Entity e; return e; }
Entity result = func2();
```

**浅拷贝**

简单地把源对象中的值复制到新对象的成员变量中。

如果成员变量包含指针，则同样会复制一份指针，但不会复制指针指向的内存。也就是说，这两个对象的指针变量是指向同一块内存的。一旦其中一个对象被销毁，其指向的内存被释放，就会出现悬空指针，导致程序错误。

**深拷贝**

不仅复制指针的值，还会为新对象的指针分配新的内存，并将源对象指针所指向的数据复制到新的内存中，避免了悬空指针的问题。

**默认拷贝构造函数**

如果一个类没有显式地定义拷贝构造函数，编译器会自动生成一个默认的拷贝构造函数。默认拷贝构造函数执行的通常是浅拷贝操作，它只会逐位地复制对象的成员变量。

所以遇到包含指针，动态分配内存的类，通常需要显示地定义拷贝构造函数来进行正确的拷贝操作。

*注*：:star:尽量使用`const`引用来传递参数，以减少拷贝


### 箭头操作符

1. 当需要使用指针访问成员变量时，就需要使用箭头操作符。

2. 除此之外，还可以用来计算偏移量
	- 当你把数据序列化成一串字节流时，想要计算某些东西的偏移量时，可以进行如下操作

```cpp
struct Vector
{
	float x, y, z;
}

int main()
{
	int offset = (int)&((Vector)nullptr)->x;
}
```


### vector的使用优化

工作原理：当创建一个`vector`对象时，它会在堆上分配一块连续的内存空间来存储元素。初始时，这块内存空间可能具有一个默认的大小，称为容量（`capacity`）。`vector`另外一个重要的属性是大小（`size`），它指当前`vector`中实际存储的元素个数。

自动扩容机制：当向`vector`中插入元素，使其大小超过当前容量时，`vector`会自动扩容。
- 先分配一块新的、更大的连续内存空间。新空间的大小通常是当前容量的一定倍数（不同的库实现有不同的扩容策略，常见的是扩容为原来的2倍）
- 将原来内存空间中的元素复制到新的内存空间中
- 释放原来的内存空间

示例代码：

```cpp
	std::vector<Vertex> vertex;
	vertex.push_back(Vertex({ 1, 2, 3 }));
	vertex.push_back(Vertex({ 4, 5, 6 }));
	vertex.push_back(Vertex({ 7, 8, 9 }));
	// 调用拷贝构造函数：6次
```

- 优化1：使用`reserve`来更改`vector`的容量，使其至少能容纳n个元素

```cpp
	std::vector<Vertex> vertex;
	vertex.reserve(3);
	vertex.push_back(Vertex({ 1, 2, 3 }));
	vertex.push_back(Vertex({ 4, 5, 6 }));
	vertex.push_back(Vertex({ 7, 8, 9 }));
	// 调用拷贝构造函数：3次
```

- 优化2：因为`push_back`方法会先在栈中创建一个新元素，然后将其拷贝或移动到`vector`中。所以，我们可以使用`emplace_back`方法，直接在实际的`vector`中构建对象，以减少拷贝次数

```cpp
	std::vector<Vertex> vertex;
	vertex.reserve(3);
	vertex.emplace_back(1, 2, 3);
	vertex.emplace_back(4, 5, 6);
	vertex.emplace_back(7, 8, 9);
	// 调用拷贝构造函数：0次
```

*说明*：`emplace_back`是C++11标准中引入的新特性，因此在使用较旧的编译器时可能需要使用`push_back`


### 导入和导出

- 导出：C++在构建动态链接库时，需要将函数或变量导出到DLL中，以便其他程序或模块可以调用这些函数。
- 导入：当其他程序或DLL需要使用一个已导出的函数或变量时，需要将这些函数或变量"导入"到自己的代码中

不同的编译器和平台有不同的导出导入方式，这里关注windows平台的MSVC编译器

- 导出：使用`__declspec(dllexport)` 关键字来导出函数
- 导入：使用 `__declspec(dllimport)` 关键字来导入函数

```cpp
// glfw3.h

/* GLFWAPI is used to declare public API functions for export
 * from the DLL / shared library / dynamic library.
 */
#if defined(_WIN32) && defined(_GLFW_BUILD_DLL)
 /* We are building GLFW as a Win32 DLL */
 #define GLFWAPI __declspec(dllexport)
#elif defined(_WIN32) && defined(GLFW_DLL)
 /* We are calling a GLFW Win32 DLL */
 #define GLFWAPI __declspec(dllimport)
#elif defined(__GNUC__) && defined(_GLFW_BUILD_DLL)
 /* We are building GLFW as a Unix shared library */
 #define GLFWAPI __attribute__((visibility("default")))
#else
 #define GLFWAPI
#endif
```

解释说明：
1. 当在windows平台编译时，而且正在将GLFW构建为一个windows平台下的DLL库时，把`GLFWAPI`从当前的DLL中导出，使得外部程序可以调用。
2. 当在windows平台编译时，而且正在调用GLFW的动态链接库时，`GLFWAPI`是从外部的动态链接库中导入的。


### 多返回值

1. 引用传参或指针传参
2. 使用数组作为返回值，可以使用`array`
3. 对于不同类型的返回值，可以使用`tuple`
4. 使用结构体


### 返回值优化

> Return Value Optimization，RVO

pass


### 模板

一种泛型编程的工具。它允许程序员编写能够处理多种数据类型的代码，而不需要为每种数据类型都重复编写相同的逻辑

**模板实例化**：当编译器遇到模板的使用时，会根据传递的实际参数类型或指定的模板参数来生成具体的代码。编译器会自动推导参数的实际类型，并将其替换，最后将新生成的函数或类存放在目标文件中。

**函数模板**

```cpp
template<typename T>
void Print(T value)
{
	std::cout << value << std::endl;
}

int main()
{
	Print(5);
	Print(5.5f);
	Print<int>(5);
	Print<float>(5.5f);
}
```

**类模板**

```cpp
template<typename T, int N>
class MyArray
{
private:
	T m_Array[N];
public:
	int GetSize() { return N; }
}

int main()
{
	MyArray<int, 10> array;
	std::cout << array.GetSize() << std::endl;
}
```

使用场景：
- 日志系统
	- 对不同类型的变量进行记录时，可以不使用函数重载，转而使用模板自动完成
- 材质系统
	- 对图像进行渲染时，拥有一个可以包含各种不同类型的统一缓冲区时，模板比较有用

*注*：模板不应该被完全禁止，也不应该疯狂地滥用（Cherno‘ s opinion）


### 宏定义

适用于不用configuration的输出。例如在Debug模式下，需要使用`LOG`打印一些消息，但是在Release模式下，需要取消打印日志，则可以使用宏来进行处理。

```cpp
#ifdef _DEBUG
#define LOG(x)  std::cout << x << std::endl;
#else
#define LOG(x)
#endif
```


```cpp
#ifdef _DEBUG == 1
#define LOG(x)  std::cout << x << std::endl;
#elif defined(NDEBUG)
#define LOG(x)
#endif
```

使用宏的时候，还可以利用反斜杠来转义换行符，使其经过预处理后在同一行

```cpp
#define MAIN int main() \
{\
	std::cout << "hello" << std::endl;\
}
```


### auto关键字

```cpp
std::string GetName()
{
	return "Cherno";
}

int main()
{
	auto name = GetName();

	int a = name.size();
}
```

好处
- 在使用API的时候，这个函数的返回值可能会改变，但是客户端不需要做任何的改动
坏处
- 不知道API的返回值已经改变，可能会破坏依赖于特定类型的代码

建议的使用场景：对于长类型的变量，可以使用`auto`关键字；但对于`int`，`float`这种就不需要使用了，会降低代码的可读性。

```cpp
	for (auto it = strs.begin(); it != strs.end(); it++)
	{
		std::cout << *it << std::endl;
	}
```

### array的使用

std::array是静态数组，存储在栈中

```cpp
	std::array<int ,5> data = { 4, 3, 6, 2, 5};
	data[0] = 9;
	
	std::cout << data.size();
```

优点
- 自动存储数组大小，传参的时候只需一个参数（和传统数组比较）
- 速度和传统数组差不多，没有性能成本
- 在调式模式下，可以防止数组越界
- 等等

### 函数指针

```cpp
void PrintValue(int a)
{
	std:::cout << "value: " << a << std::endl;
}

int main()
{
	void(*func1)(int) = PrintValue;
	auto func2 = PrintValue;    // 或者直接使用auto关键字
	
	func1(8);
	func2(8);
}
```

使用场景：将一个函数传递给一个api

```cpp
void ForEach(const std::vector<int>& values, void(*func)(int))
{
	for (int value : values)
		func(value);
}

int main()
{
	std::vector<int> values = { 4, 2, 5, 2};
	ForEach(values, PrintValue);
}
```


### 匿名函数lambda

只要有一个函数指针，就可以使用匿名函数
- 非捕获lambda可以隐式转化为函数指针
- 而捕获lambda不可以，需要使用`std::function`

```cpp
int main()
{
	std::vector<int> values = { 4, 2, 5, 2};
	auto lambda = [](int value) {std::cout << "value: " << value << std::endl; };
	ForEach(values, lambda);
}
```

```cpp
#include <algorithm>

int main()
{
	std::vector<int> values = { 4, 2, 5, 2};
	auto it = std::find_if(values.begin(), values.end(), [](int value) { return value > 3;})
	std::cout << *it << std::endl;
}
```


### using namspace std的使用讨论

**命名空间**：避免命名冲突，允许我们在不同的上下文中调用相同的符号。通常在构建一个代码库，或是在自己的项目中使用。

不使用`using namspace std;`的好处是我们能直接看出哪些函数是标准库的，这样很直观，很容易分辨不同命名空间下的函数，如`std::vector`和`eastl::vector`。在大型项目中，尽量不要使用，更不能写在头文件中，难以调试。但对于小项目，比如你自己写一个小游戏，但是没啥问题。尽量写在局部作用域之内，比如函数或`if-else`语句之内。


### 计时

使用`chrono`库，不依赖于平台

```cpp
int main()
{
	using namespace std::literals::chrono_literals;

	auto start = std::chrono::high_resolution_clock::now();
	std::this_thread::sleep_for(1s);
	auto end = std::chrono::high_resolution_clock::now();

	std::chrono::duration<float> duration = end - start;
	std::cout << duration.count() << "s" << std::endl;
}
```


### 多维数组的动态分配

```cpp
int main()
{
	// 二维数组
	int** a2d = new int*[5];
	for (int i = 0; i < 5; i++)
		a2d[i] = new int[5];

	// 三维数组
	int*** a3d = new int**[5];
	for (int i = 0; i < 5; i++)
	{
		a3d[i] = new int*[5];
		for (int j = 0; j < 5; j++)
		{
			a3d[i][j] = new int[5];
		}
	}
}
```


### 排序

```cpp
#include <vector>
#include <algorithm>
#include <functional>

int main()
{
	std::vector<int> values = { 3, 5, 2, 1, 4 };
	
	std::sort(values.begin(), values.end(), [](int a, int b){ return a > b; });

	std::sort(values.begin(), values.end(), std::greater<int>());
}
```


### 类型双关

绕过类型系统，简单地说，就是将同样的内存以不同的方式解读 

```cpp
int main()
{
	int a = 50;
	double value = *(double*)&a;
}
```

在这里，`value`获得了与`a`相同的内存内容，但是其中只有4个字节是合法的，剩余4个是不属于我们的内存，这是不安全的，仅用于演示

```cpp
struct Entity
{
	int x, y;
}

int main()
{
	Entity e = { 5, 8 };

	int* position = (int*)&e;
	std::cout << position[0] << ", " << position[1] << std::endl;

	int y = *(int*)((char*)&e + 4);
	std::cout << y << std::endl;
}
```

可以将这个结构体看成`int`数组，另外，也可以以`char`的形式访问


### 联合体

当需要使用多种方法来处理相同的数据时，可以使用联合体。这其实就是类型双关，只不过使用联合体更直观

```cpp
struct Vector2
{
	float x, y;
}

struct Vector4
{
	union
	{
		struct
		{
			float x, y, z, w;
		};
		struct
		{
			Vector2 a, b;
		};
	};
};

void PrintVector2(const Vector& vector)
{
	std::cout << vector.x << ", " << vector.y << std::endl;
}

int main()
{
	Vector4 vector = { 1.0f, 2.0f, 3.0f, 4.0f };
	PrintVector2(vector.a);
	PrintVector2(vector.b);
	vector.z = 500.0f;
	std::cout << "------------------" << std::endl;
	PrintVector2(vector.a);
	PrintVector2(vector.b);
}
```


### 虚析构函数

```cpp
class Base
{
public:
	Base() { ... }
	~Base() { ... }
}

class Derived : public Base
{
public:
	Derived() { m_array = new int[5]; ... }
	~Derived() { delete[] m_array; ... }
private:
	int* m_array;
}
```

```cpp
	Derived* derived = new Derived();
	delete derived;
```

当声明一个派生类实例，然后释放它，则基类和派生类的构造函数、析构函数都会被调用。

```cpp
	Base* poly = new Derived();
	delete poly;
```

而当使用多态时，这个多态实例只会调用基类和派生类的构造函数，还有基类的析构函数，不会调用派生类的析构函数，这很容易造成内存泄漏。

```cpp
class Base
{
public:
	Base() { ... }
	virtual ~Base() { ... }
}
```

所以需要将基类的析构函数声明为虚函数，这样编译器才会去调用派生类的析构函数，这样才能安全地扩展这个类。


### 类型转换

c语言风格的强类型转换

```cpp
	double a = 5.35;
	int b = (double)a;
```

c++语言风格的类型转换，使用c语言的强转都能做到，但是它多了一些检查，增加了一些安全机制，更容易查找和追溯问题

1. **静态转换**（`static_cast）

将一种数据类型的值强制转换为另一种数据类型的值，但不进行任何运行时类型检查

```cpp
	double value = 3.2;
	double s = static_cast<int>(value) + 5.4;
```

2. **动态转换**（`dynamic_cast`）

专门用于继承层次结构的指针转换，主要用在将基类指针或引用转化为派生类指针或引用的情况（或者相反），在运行时进行类型检查，如果不能转换则返回空指针或引发异常

```cpp
class Base
{
	...
}

class Derived : public Base
{
	...
}

class AnotherClass : public Base
{
	...
}

int main()
{
	Base* ptr = new Derived();
	AnotherClass* p = dynamic_cast<AnotherClass*>(ptr);
	if (p)
	{
		...
	}
}
```

这里`p`将得到一个`nullptr`，因为`ptr`是`Derived`类型的，不是`AnotherClass`类型的

动态转换之所以能够运行，是因为开启了运行时类型信息（runtime type information，RTTI），它存储我们的所有类型运行时的类型信息，会增加一定的开销

3. **常量转换**（`const_cast`）

用于将`const`类型的对象转换为非`const`类型的对象，但是不能改变对象的类型

```cpp
	const int i = 10;
	int& r = const_cast<int&>(i);
```


4. **重新解释转换**（`reinterpret_cast`）

将一个数据类型的值重新解释为另一个数据类型的值，但是不会进行任何类型检查

```cpp
	int num = 1234;
	float x = reinterpret_cast<float&>(i);
```


### 预编译头文件

> Precompiled Header，PCH

将常用的头文件通通放到一个头文件中，并将该头文件预先编译成二进制形式，提高编译效率

- 将每个源文件常用的头文件放到预编译头文件中，如`<vector>`、`<string>`、`<iostream>`等标准库头文件，以及项目的公共头文件
- 不要把频繁更改的文件放进预编译头文件中

visual studio默认将它命名为`stdafx.h`或`pch.h`

使用方法：（假设已有`pch.h`文件）
1. visual studio：创建一个`pch.cpp`文件，编写`#include "pch.h"`，并将该cpp文件的属性的"预编译头"设置为"创建"。然后将项目属性的"预编译头"设置为"使用"，并设置预编译头文件为"`pch.h`"即可。
2. g++：`g++ -std=c++11 pch.h`，然后生成`pch.h.gch`文件即可


### 基准测试

- 需要确保被测量的东西是实际上被编译的代码。因为编译器可以优化代码，甚至完全更改代码，可能你所测量的那部分代码已经被优化成其他形式，不是按照原有形式进行的。
- 在debug模式下，对测试性能不是很友好，因为有很多额外的安全措施。确保最终在release模式下进行测试

```cpp
class Timer
{
public:
	Timer()
	{
		m_StartTimepoit = std::chrono::high_resolution_clock::now();
	}
	~Timer()
	{
		Stop();
	}
	void Stop()
	{
		auto endTimepoint = std::chrono::high_resolution_clock::now();
		auto start = std::chrono::time_point_cast<std::chrono::microseconds>(m_StartTimepoit).time_since_epoch().count();
		auto end = std::chrono::time_point_cast<std::chrono::microseconds>(endTimepoint).time_since_epoch().count();

		auto duration = end - start;
		double ms = duration * 0.001;

		std::cout << duration << "us (" << ms << "ms)\n";
	}
private:
	std::chrono::time_point<std::chrono::high_resolution_clock> m_StartTimepoit;
};

int main()
{
	{
		Timer timer;
		// 需要测试的代码
		...
	}
}
```


### RAII

> Resource Acquisition Is Initialization，资源获取即初始化

它是c++的一种管理资源、避免资源泄漏的方法，利用栈对象自动销毁的特点来实现。通过构造函数来获取资源，析构函数来释放资源。像智能指针和互斥锁等就是这个原理。


### 结构化绑定

> Structured bindings（c++17特性）

在多返回值的情况下，可以快速简洁地获取变量。

```cpp
std::tuple<std::string, int> CreatePerson()
{
	return { "lreverse", 21 };
}

int main()
{
	// 方法1
	auto person = CreatePerson();
	std::string& name = std::get<0>(person);
	int age = std::get<1>(person);

	// 方法2
	std::string name;
	int age;
	std::tie(name, age) = CreatePerson();

	// 方法3：结构化绑定
	auto[name, age] = CreatePerson();
}
```


### std::optional的使用

> c++17特性

数据可能存在，也可能不存在，可以使用`optional`库来存储变量

```cpp
#include <iostream>
#include <fstream>
#include <optional>

std::optional<std::string> ReadFileAsString(const std::string& filepath)
{
	std::ifstream stream(filepath);
	if (stream)
	{
		std::string result;
		// read file
		stream.close();
		return result;
	}
	return {};
}

int main()
{
	std::optional<std:::string> data = ReadFileAsString("data.txt");
	std::string value = data.value_or("present"); // 可以设置默认值
	
	if (data)  // if (data.has_value())
	{
		std::cout << "File read successfully!\n";
	}
	else
	{
		std::cout << "File could not opened!\n";
	}
}
```


### std::variant的使用

> c++17特性

单一变量存放多种类型的数据

```cpp
#include <iostream>
#include <variant>

int main()
{
	std::variant<std::string, int> data;

	// 使用get
	data = "lreverse";
	std::cout << std::get<std::string>(data) << "\n";
	
	data = 2;
	std::cout << std::get<int>(data) << "\n";

	// 使用get_if，用来判断当下所使用的数据类型
	if (auto value = std::get_if<std::stiring>(&data))
	{
		std::string& v = *value;
	}
	else
	{
		// ...
	}
	
}
```

```cpp
enum class ErrorCode
{
	None = 0, NotFound = 1, NoAcess = 2
};

std::variant<std::string, ErrorCode>ReadFileAsString()
{
	//...
}
```

- `variant`变量是更加安全，不会造成未定义的行为，它会将所有可能的数据类型存储为单独的变量作为单独的成员，其大小是所有类型的总和
- 除非在做底层优化，或其他需要保持低内存等事情，否则尽量使用`variant`


### std::any的使用

> c++17

单个变量中存储任意类型的数据

```cpp
#include <iostream>
#include <any>

int main()
{
	std::any data;
	data = 2;
	data = "lreverse";
	data = std::string("lreverse");
}
```

对于小类型的变量来说，它的工作方式与variant类似；对于大类型的变量来说，它会动态分配内存

*注*：如果有需求，尽量使用variant，它是any的安全版本。


### std::async的使用

> c++11特性

`std::async`用于启动一个异步任务，简单地说，就是让一个函数在一个独立的线程中执行。调用`std::async`的线程可以继续执行其他任务，无需等待被调用函数执行完毕。
- 在`<future>`库中
- 有两种策略：
	- `std::launch::async`：函数必须在一个新的线程中异步执行
	- `std::launch::deferred`：函数的执行会被延迟，直到在`std::future`对象上调用`get()`或者`wait()`函数

`std::async`与`std::thread`的区别

1. `std::thread`用于创建一个线程，这个线程是一定会被创建的，如果系统资源紧张，强行创建可能会导致系统的崩溃；`std::async`则根据不同的策略创建异步任务
2. `std::async`返回一个`std::future`对象，可以获取异步任务的结果，该结果还可以存储任务执行过程中抛出的异常；但是`std::thread`不能返回执行结果
3. `std::thread`需要显示地调用`join`或`detach`来管理线程的生命周期；而`std::async`则是与相关联的`std::future`对象紧密相关
	1. `std::launch::async`：从调用者的角度来看，当通过`std::future`对象获取任务结果（调用`get()`函数）或者等待任务完成（调用`wait()`函数）时，调用者会阻塞，直到异步任务执行完毕
	2. `std::launch::deferred`：任务实际上并没有立即执行，而是在`std::future`对象的`get()`或`wait()`函数被调用时，任务在调用`get()`或`wait()`的线程（通常是主线程）中同步执行。

```cpp
void doSomething(std::string filepath) {}

int main()
{
	std::vector<std::future<void>> future;
	for (const auto& file : paths)
	{
		future.push_back(std::async(std::launch::async, doSomething, file));
	}
}
```

*注*：
- 线程函数的参数按值移动或复制，如果要将引用参数传递给线程函数，则必须要被包装，通常使用`std::ref`，例如：`std::ref(num)`，这样的话，传递给线程函数的`num`就是一个引用了
- 写异步需要考虑作用域结束的问题


### 让字符串更快

在处理字符串时，会在不经意间增加内存的分配。在处理实时运行的程序时，比如游戏，这是会对性能造成影响的，会损害帧率。
- 可以使用·`string_view`
- c++17特性

```cpp
#include <iostream>
#include <string>

static uint32_t s_AllocCount = 0;

// 重载new运算符，统计分配的次数
void* operator new(size_t size)
{
	s_AllocCount++;
	std::cout << "Allocating " << size << " bytes\n";
	return malloc(size);
}
```

1. 情况一：分配了1次

```cpp
void printName(const std::string& name)
{
	std::cout << name << std::endl;
};

int main()
{
	std::string name = "lreverse";

	printName(name);
	// printName("lreverse");
	std::cout << s_AllocCount << " allocations" << std::endl;
	std::cin.get();
}
```

在这种情况下，定义变量`name`的时候，`string`类会分配内存。直接传入字符串字面量也会隐式转换成`string`类，也会分配内存。

2. 情况二：分配了3次

```cpp
void printName(const std::string& name)
{
	std::cout << name << std::endl;
};

int main()
{
	std::string name = "www lreverse";

	std::string	firstName = name.substr(0, 3);
	std::string lastName = name.substr(4, 8);

	printName(lastName);
	std::cout << s_AllocCount << " allocations" << std::endl;
	std::cin.get();
}
```

道理同上。

3. 优化一：分配了1次

```cpp
void printName(std::string_view name)
{
	std::cout << name << std::endl;
};

int main()
{
	std::string name = "www lreverse";

	std::string_view firstName(name.c_str(), 3);
	std::string_view lastName(name.c_str() + 4, 8);

	printName(lastName);
	std::cout << s_AllocCount << " allocations" << std::endl;
	std::cin.get();
}
```

这一次分配只有`name`的分配，`string_view`实际上就是指向了原字符串

4. 优化二：分配了0次

```cpp
void printName(std::string_view name)
{
	std::cout << name << std::endl;
};

int main()
{
	const char* name = "www lreverse";

	std::string_view firstName(name, 3);
	std::string_view lastName(name + 4, 8);

	printName(lastName);
	std::cout << s_AllocCount << " allocations" << std::endl;
	std::cin.get();
}
```

总结：多使用`const char*`和`string_view`


### 可视化基准测试

使用谷歌自带的可视化工具[tracing](chrome://tracing)，现在似乎已经更新到[perfetto](https://ui.perfetto.dev/)，原有的tracing不在更新，但还可以用。

- cherno源码地址：[cherno's visual benchmark code](https://gist.github.com/TheCherno/31f135eea6ee729ab5f26a6908eb3a5e)

**插桩**（Instrumentation）是一种在程序运行时动态修改代码的技术。它可以用来监控、记录和分析程序的运行状态，包括方法调用、参数传递、返回值等信息

tracing要求的json文件示例：

```json
{
  "traceEvents": [
    {"name": "Asub", "cat": "PERF", "ph": "B", "pid": 22630, "tid": 22630, "ts": 829},
    {"name": "Asub", "cat": "PERF", "ph": "E", "pid": 22630, "tid": 22630, "ts": 833}
  ],
  "displayTimeUnit": "ns",
  "systemTraceEvents": "SystemTraceData",
  "otherData": {
    "version": "My Application v1.0"
  },
  "stackFrames": {...}
  "samples": [...],  
}
```

**traceEvents 数组**中的每个元素代表一个性能事件
- `name`：用于标识事件的名称
- `cat`（category）：这个属性用于定义事件的类别
- `ph`（phase）：表示事件的阶段。常见的值有 “B”（begin，表示事件开始）和 “E”（end，表示事件结束）。这可以用于计算事件的持续时间。
- `ts`（timestamp）：记录事件开始的时间戳，通常是从某个起始时间点开始计算的微秒或纳秒数，用于确定事件发生的时间顺序。
- `pid`和`tid`：用于区分不同的进程和线程。这有助于在多进程或多线程环境中定位事件发生的位置。
- `args`：这是一个对象，用于存储与事件相关的其他参数。

### 单例模式

> Singleton Pattern

单例模式是一种设计模式，它确保一个类只有一个实例，并提供一个全局访问点来访问这个实例。c++中的单例只是一种组织一堆全局变量和静态函数的方法，就像命名空间一样，本质上就是一个单一的命名空间

- 该类不能被公开创造
- 该类不能被复制

**懒汉式**

只有当需要使用到实例时，才回去创建实例

```cpp
class Singleton
{
public:
	Singleton(const Singleton& other) = delete;
	static Singleton& GetInstance()
	{
		static Singleton s_instance;
		return s_instance;
	}
	void DoSomething() {}
private:
	Singleton() {}
	~Singleton() {}
}
```

这里使用静态局部变量，只有在第一次调用`GetInstance`时，该实例才会被创建。在c++11下，静态局部变量是线程安全的。

**饿汉式**

系统一运行，就会初始化创建实例，需要时可直接调用

```cpp
class Singleton
{
public:
	Singleton(const Singleton& other) = delete;
	static Singleton& GetInstance()
	{
		return s_instance;
	}
	void DoSomething() {}
private:
	Singleton() {}
	~Singleton() {}
	static Singleton s_instance;
}

Singleton Singleton::s_instance;
```

该方法也是线程安全的。