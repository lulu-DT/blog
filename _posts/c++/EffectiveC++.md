# Effective C++

## **Item M1：指针与引用的区别** 

> 指针是一个变量，存储的是另一个变量的索引地址
>
> 引用是一个别称，是一个变量的另一种表达

基于以上的原理我们得出以下结论：

1. 指针可以只声明，不用初始化（因为它自己就是一个变量）
2. 指针可以重定向到另一个变量



## 5. **异常** 

### 5.0 c++异常处理机制

#### **异常抛出（Throwing an Exception）**

当程序执行过程中发生错误或不符合预期的情况时，可以使用 `throw` 关键字抛出异常。异常通常是一个对象，可以是基本数据类型、类对象或指针等

#### **栈展开（Stack Unwinding）**

当异常被抛出时，程序会在调用栈中查找匹配的 `catch` 块，栈上的局部对象会被销毁，这个过程称为栈展开。在销毁每个栈帧时，会调用相应对象的析构函数。

#### **异常传播（Exception Propagation）**

一旦异常被抛出，程序的控制流会立即跳转到最近的匹配的异常处理器，跳过正常的函数调用链

#### **匹配异常处理器（Matching Exception Handler）**

异常处理器是通过类型匹配来确定的。如果抛出的异常类型与某个 `catch` 块的类型匹配，则该 `catch` 块被执行。如果没有找到匹配的处理器，程序将终止

#### **异常终止处理（Termination Handling）**

如果没有找到匹配的 `catch` 块，程序将调用 `std::terminate` 函数，该函数默认会终止程序。你可以通过设置自定义的 `std::terminate` 处理函数来改变这种行为

#### **异常处理（Exception Handling）**

异常处理是通过 `try`、`catch` 和 `throw` 关键字来完成的。`try` 块包含可能引发异常的代码，而 `catch` 块用于捕获和处理异常。如果 `try` 块中的代码引发了异常，则控制流将转移到与异常匹配的 `catch` 块，如果没有找到匹配的 `catch` 块，程序将终止，并调用 `std::terminate` 函数

> 总的来说， 当一个异常被抛出来的时候，异常会向上进行传播（栈展开）直到找到一个最近的`catch`语句，如果找不到则直接调用`std::terminate`终止程序，这个过程会跳过正常的函数调用流程，已在栈上的局部变量会直接被析构（调用析构函数），异常后面的语句也不会在执行

### **5.1 Item M9：使用析构函数防止资源泄漏** 

> 在类的析构函数中释放资源，以确保资源的正确释放，避免资源泄漏

#### 解释

####  对于以下代码：

```c++
class ALA { 
	public: 
 	virtual void processAdoption() = 0; 
 	... 
};

void func(...) { 
 	while (...) { // 循环
 		ALA *pa = new ALA(); //创建资源 
 		pa->processAdoption(); //做一些操作 
 		delete pa; //释放资源 
 	} 
}
```

如果在第10行抛出了异常,第11行的代码实际上是不会执行的，这就会导致内存泄漏

正确的做法是使用智能指针对创建的资源进行管理，这样就算异常发生了也能正常被释放

#### 正确示例

```c++
#include <iostream>

class ResourceHolder {
public:
    // 构造函数，用于分配资源
    ResourceHolder() {
        resource = new int[10];  // 分配动态数组
        std::cout << "Resource allocated." << std::endl;
    }

    // 析构函数，用于释放资源
    ~ResourceHolder() {
        delete[] resource;  // 释放动态数组
        std::cout << "Resource released." << std::endl;
    }

private:
    int* resource;
};

int main() {
    ResourceHolder holder;  // 创建一个对象，分配资源

    // 在这里可以做一些其他事情,就算发生了异常，holder还是能正常调用析构

    // holder对象超出作用域，析构函数会被调用，释放资源
    return 0;
}

```

### **5.2 Item M10：在构造函数中防止资源泄漏**

> 使用构造函数获取资源的好处在于，如果在构造函数中发生了错误，对象的析构函数会被调用，从而确保资源的正确释放，避免资源泄漏

#### 解释

假设你正在构造函数里面分配资源

```c++
class ResourceHolder {
public:
    // 构造函数，用于获取资源
    ResourceHolder() {
        resource = new int[10];  // 分配动态数组
      	// 其它工作，可能会抛出异常
        doSomeWork()；
    }
}
private:
    int* resource;
};
```

如果在第7行发生了异常，我们设想一下会发生什么事情：

1. 程序会立马进行栈展开，找到最近的能处理异常的`catch`语句
2. 栈上面的变量会自动析构

所以如果发生了异常，那么会直接跳过`ResourceHolder`的构建工作进行异常处理， 此时`ResourceHolder`还没有完成构建，它的析构函数永远无法有效的调用，那么已经分配了的资源将没有机会被释放（第5行）

> C++仅仅能删除被完全构造的对象（fully contructed objects）, 只有一个对象的构造函数完全运行完毕，这个对象才被完全地构造
>
> C++拒绝为没有完成构造操作的对象调用析构函数是有一些原因的，而不是故意为你制造困难。
>
> 原因是：在很多情况下这么做是没有意义的，甚至是有害的。如果为没有完成构造操作的对象调用析构函数，析构函数如何去做呢？仅有的办法是在每个对象里加入一些字节来指示构造函数执行了多少步？然后让析构函数检测这些字节并判断该执行哪些操作。这样的记录会减慢析构函数的运行速度，并使得对象的尺寸变大。C++避免了这种开销，但是代价是不能自动地删除被部分构造的对象

#### 正确示例

```c++
#include <iostream>

class ResourceHolder {
public:
    // 构造函数，用于获取资源
    ResourceHolder() {
        resource = new int[10];  // 分配动态数组

        // 模拟构造过程中可能发生的错误
      	try() {
          someOtherWork();
				} catch(...) {
          delete[] resource;
          std::cout << "Resource allocated failed" << std::endl;
 					throw;
        }
    }

    // 析构函数，用于释放资源
    ~ResourceHolder() {
        delete[] resource;  // 释放动态数组
        std::cout << "Resource released." << std::endl;
    }

private:
    int* resource;
};

int main() {
    try {
        ResourceHolder holder;  // 创建一个对象，获取资源

        // 在这里可以做一些其他事情

        // holder对象超出作用域，析构函数会被调用，释放资源
    } catch (const std::exception& e) {
        std::cerr << "Exception caught: " << e.what() << std::endl;
        // 处理异常，确保资源被释放
    }

    return 0;
}

```

### **5.3 Item M11：禁止异常信息（exceptions）传递到析构函数外** 

> 如果析构函数抛出异常并且这个异常没有被捕获，那么程序会调用`std::terminate()`，导致程序终止。因此，通常建议在析构函数中捕获异常，而不要让异常传递到析构函数外部

#### 解释

在有两种情况下会调用析构函数：

1. 第一种是在正常情况下删除一个对象，例如对象超出了作用域或被显式地 delete
2. 第二种是异常传递的堆栈辗转开解（stack-unwinding）过程中，由异常处理系统删除一个对象

析构函数没法区别这两种情况，如果在异常处理过程中析构函数继续抛出异常，那将会导致c++直接调用`std::terminate`中止程序

#### 正确示例

```c++
#include <iostream>
#include <stdexcept>

class ResourceHolder {
public:
    // 构造函数，用于获取资源
    ResourceHolder() {
        resource = new int[10];  // 分配动态数组
        std::cout << "Resource allocated." << std::endl;
    }

    // 析构函数，用于释放资源
    ~ResourceHolder() noexcept {  // 使用 noexcept 来标记析构函数不抛出异常
        try {
            delete[] resource;  // 释放动态数组
            std::cout << "Resource released." << std::endl;
        } catch (const std::exception& e) {
            // 捕获异常并在这里处理
            std::cerr << "Exception caught in destructor: " << e.what() << std::endl;
        }
    }

private:
    int* resource;
};

int main() {
    try {
        ResourceHolder holder;  // 创建一个对象，获取资源

        // 在这里可以做一些其他事情

        // holder对象超出作用域，析构函数会被调用，释放资源
    } catch (const std::exception& e) {
        std::cerr << "Exception caught: " << e.what() << std::endl;
        // 处理异常，确保资源被释放
    }

    return 0;
}

```



### **Item**

#### 解释

#### 正确示例

### **Item**

#### 解释

#### 正确示例

### **Item**

#### 解释

#### 正确示例

### **Item**

#### 解释

#### 正确示例

### **Item**

#### 解释

#### 正确示例

### **Item**

#### 解释

#### 正确示例

### **Item**

#### 解释

#### 正确示例

### **Item**

#### 解释

#### 正确示例

### **Item**

#### 解释

#### 正确示例

### **Item**

#### 解释

#### 正确示例

### **Item**

#### 解释

#### 正确示例

### **Item**

#### 解释

#### 正确示例

### **Item**

#### 解释

#### 正确示例

### **Item**

#### 解释

#### 正确示例

### **Item**

#### 解释

#### 正确示例



