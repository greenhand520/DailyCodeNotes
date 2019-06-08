## 析构函数

每当出现一次关于一个类的实例化对象的调用时，当这个对象是局部的还是全局的，对象使用结束后，变回自动调用下类的的析构函数。

对于局部：

如果一个函数的参数中有类的对象，那么这个函数调用结束完，会调用一次这个类的析构函数。

如果这个函数中有类的对象，与上同理。

对于全局：

主函数运行结束后，会调用类的析构函数。

例子：

```c++
#include <iostream>
#include <ctime>
#include <cstring>

using namespace std;

class Line {

private:
    int length;
    int *lengthPtr;
public:
    int getLength() const;
    void setLength(int length);
    Line();
    Line(int length);
    Line(int *lengthPtr);
    Line(int length, int *lengthPtr);
    int *getLengthPtr() const;
    void setLengthPtr(int *lengthPtr);
    virtual ~Line();
};

int Line::getLength() const {
    return length;
}

Line::Line(int length, int *lengthPtr) : length(length), lengthPtr(lengthPtr) {}

Line::Line(int length) : length(length) {
   cout << "Line::Line(int length) : length(length)" <<endl;
}

Line::Line(int *lengthPtr) : lengthPtr(lengthPtr) {
    cout << "Line::Line(int *lengthPtr) : lengthPtr(lengthPtr)" <<endl;
    cout << *lengthPtr <<endl;
}

Line::Line() {}

void Line::setLength(int length) {
    Line::length = length;
}

int *Line::getLengthPtr() const {
    return lengthPtr;
}

void Line::setLengthPtr(int *lengthPtr) {
    Line::lengthPtr = lengthPtr;
}

Line::~Line() {
    cout << "free memory" <<endl;
}

void display(Line obj){
    cout << "Length of line : " << obj.getLength() <<endl;
}

Line line3(30);

int main() {

    Line line = Line();
    line.setLength(10);
    display(line);
    display(line3);
    return 0;
}
```

运行结果：

> Line::Line(int length) : length(length)
> Length of line : 10
> free memory
> Length of line : 30
> free memory
> free memory
> free memory

第一个free memory是“   display(line)”调用结束后，调用的；

第二个free memory是“   display(line3)”调用结束后，调用的；

随后两个，一个是释放line的内存调用的，一个是释放line的内存调用的。

## 复制构造函数

使用复制构造函数的情况：

类的对象需要拷贝时，拷贝构造函数将会被调用。以下情况都会调用拷贝构造函数：
（1）一个对象以值传递的方式传入函数体 
（2）一个对象以值传递的方式从函数返回 
（3）一个对象需要通过另外一个对象进行初始化。

