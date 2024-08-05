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


