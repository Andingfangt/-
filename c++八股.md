#### 1. c++多态与虚指针

目的：允许基类的指针或引用调用继承类中定义的虚函数方法, 同时指名可以override的成员函数（提高程序的可读性）

example：
```c++
class Base {
public:
    virtual void display() {
        cout << "Base display" << endl;
    }
};
 
class Derived : public Base {
public:
    virtual void display() override {
        cout << "Derived display" << endl;
    }
};
 
int main() {
    Base *ptr = new Derived();
    ptr->display();  // 输出 "Derived display"
    return 0;
}
```

* `虚函数`：即被virtual修饰的类成员函数
* `隐藏(重定义)`与`覆盖(重写)`的区别：
  * 隐藏的基类函数不需要用 virtual 修饰，而覆盖的基类函数必须要有 virtual 修饰
  * 隐藏（重定义）的直接效果：如果用一个父类的指针（或引用）指向（或引用）子类对象，那么这个父类的指针或引用将调用父类的同名函数；而不是多态下期望的子类方法。
* `override` 和 `final`: C++11 增加的两个继承控制关键字：
  * override 类似 `@override` in java, 保证在派生类中声明的重载函数，与基类的虚函数有相同的签名
  * final：在类名后加上 final 关键字，阻止类被进一步继承；在虚函数后加上 final 关键字，组织虚函数被进一步重写
* `纯虚函数`: 是一个没有实现的虚函数，它在基类中声明为virtual void func() = 0;。含有纯虚函数的类不能实例化，只能作为抽象基类使用，规范了派生类必须重写。


#### 2. C++中的友元

* `友元函数`是在一个类中声明的一个非成员函数，但在类的内部声明该函数为友元。这意味着该函数可以访问该类的私有成员和保护成员，包括私有变量和私有函数。
* `友元函数`的声明通常位于类的声明中，但其实现则位于类外部。
* `友元类`的声明使得一个类可以访问另一个类的私有和受保护成员。‌但友元关系是单向的，不具有交换性
* 友元关系不能传递
* 当需要重载类的操作符（如<<、>>、+、-等）时，友元函数可以访问私有成员，实现合适的操作。

友元函数example:

```c++
class People {
    string name;
    int age;
    // 友元函数，用于改变私有数据成员的值
    friend void grow(People& p);
public:
    // 构造函数
    People(string name, int age): name(std::move(name)), age(age) {}
    // 获取年龄
    int getAge() const {
        return age;
    }
};
 
// 实现友元函数，用于改变私有数据成员的值
void grow(People& p) {
    ++p.age;
}
 
int main() {
    People p{"Asuna", 17};
    std::cout << "origin age: " << p.getAge() << std::endl; // 输出原始年龄 17
    // 使用友元函数修改
    grow(p);
    // 输出修改后的年龄
    std::cout << "now age: " << p.getAge() << std::endl; //输出改变后的年龄 18
    return 0;
}
```

友元类example:

```c++
class Student {
    // 声明 StudentCaculate 类是 Student 类的友元类
    // 在 StudentCaculate 类中可以访问 Student 类中的私有成员
    friend class StudentCaculate;
private:
    int score; // 假设有一个私有成员变量 score
};

class StudentCaculate {
public:
    void calculateScore(Student s) {
        // 在这里可以访问 Student 类的私有成员变量 score
        s.score = 100; // 这是一个友元操作，‌因为 StudentCaculate 类是 Student 类的友元
    }
};
```

#### 3. C++建议 const enum inline去替代宏

C++中`inline`来替代C中的`宏函数`;
`const`、`enum`替代C中的`宏常量`

##### 宏函数的优缺点：

* 优点（同inline）：
  * 宏（内联）函数在被调用处进行代码展开，省去了参数压栈、栈帧开辟与回收，结果返回等，从而提高程序运行速度。
  * 当频繁调用一个函数的时候（比如快排算法中函数多次被调用），此时消耗栈帧的比较大（不断地调用栈帧销毁栈帧）。此时使用内联或宏函数会带来优化。
* 缺点：
  * 1、不能调试（因为预编译阶段进行了替换）
  * 2、没有类型安全的检查
  * 3、有些场景下非常复杂，容易出错，不容易掌握:
    * 不能加分号$\{\}$, 宏是把东西都替换掉，如果定义带了分号，那么替换后的语句将会有两个分号
    * 返回值需要有外部括号，防止优先级错误
    * 输入值需要有内部括号，防止优先级错误

    例子：正确的ADD宏函数：

    ```c++
    // 宏函数
    #define ADD(x, y) ((x) + (y));
    ```

##### 内联函数替代宏函数:

在函数返回值前加一个inline即可， 那么函数就会在调用位置直接展开

```c++
// 内联函数
inline int Add(int a,int b)
{
    return a + b;
}
```

优点：

* 内联函数相比宏函数来说，在代码展开时，会做安全检查或自动类型转换（同普通函数），而宏定义则不会。
* 内联函数在运行时可调试
* 在类中声明同时定义的成员函数，自动转化为内联函数，因此内联函数可以访问类的成员变量

```c++
// 类内定义，隐式内联
class A {
    int doA() { return 0; }         // 隐式内联
}

// 类外定义，需要显式内联
class A {
    int doA();
}
inline int A::doA() { return 0; }   // 需要显式内联
```

缺点：

* 可执行文件大小增大。
（inline是经典的空间换时间，在使用内联函数时，是将代码展开，而不是到内存栈中寻找函数的位置，容易造成代码膨胀）（例如N次使用函数时，且函数为K行代码，普通函数使用时用了N+K行，而内联函数使用时用了N*K行）
* 内联函数最适合用于小型函数，例如提供数据成员访问权限的函数。 短函数对函数调用的开销很敏感。 较长的函数在调用和返回序列方面花费的时间可成比例地减少，而从内联的获益也会减少。

##### const代替宏常量

```c++
// 使用宏定义常量
#define PI 3.14159265358979323846
// 使用const定义常量
const double PI = 3.14159265358979323846
```

使用const代替宏，使得常量具有明确的数据类型，有助于编译器进行类型检查；同时有利于发生错误时的DEBUG，当使用宏常量但得一个编译错误信息时，有可能会带来困惑，因为这个错误会提到 3.14159265358979323846 而不是 PI。

##### 枚举enum

* **特点**：
  * 枚举可以一次定义大量相关的常量，枚举可以自增，这样不用每一个值都定义
  * 枚举是一个集合，代表一类值，方便使用

* 枚举类型的基础类型必须是整型，C++11之后可以是 `int`、`unsigned int`、`char` 或者其他整型变体。默认情况下，如果不指定基础类型，枚举会使用 int 作为其底层类型。


```c++
enum COLOR : unsigned int
{
	RED, // 0
	ORANGE, // 1
	YELLOW, // 2
	GREEN = 10,
	CYAN, // 11
	BLUE, // 12
	PURPLE, // 13
};
```

可以使用类中的枚举常量来建立在整个类中都恒定的常量：

```c++
class Person{
public:
    using SexType = enum : long long{
        BOY = 0,
        GIRL // 显性 longlong 所以 sizeof(Person::BOY) == 8.
    };
};
//访问的时候通过，Person::BOY或者Person::GIRL来进行访问。
```
