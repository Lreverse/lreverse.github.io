---
title: CppSeries学习
date: 2024-12-05 16:58:23 +0800
categories: [Coding, c++]
tag: [c++]
math: true
mermaid: true
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


### 单例模式


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

使用普通指针的话，需要手动分配内存，并在使用完后释放内存。如果忘记释放内存，就会导致内存泄露；而如果对同一块内存进行多次释放，可能会产生错误。所以使用智能指针可以帮助我们更方便、更安全地管理动态分配的内存。
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


