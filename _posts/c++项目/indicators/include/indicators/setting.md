## 代码阅读



```c++
template <bool condition> struct if_else;
```

首先这是一个结构体的声明

`template <bool condition>`表示这是一个模板结构体

根据`condition`的不同可以生成不同的结构体



```c++
template <> struct if_else<true> { using type = std::true_type; };

template <> struct if_else<false> { using type = std::false_type; };
```

将上述的if_else模板结构体进行[特化](#模板特化)，根据条件 `true` 和 `false` 提供了不同的实现。在这个特化中，使用了 `std::true_type` 和 `std::false_type`，这是 C++ 标准库中提供的用于表示编译期常量 `true` 和 `false` 的类型

## 知识点

### <a name="模板特化">类模板特化</a>

模板特化是 C++ 中一种用于为特定模板参数提供定制实现的机制。通过模板特化，你可以为特定类型或值的模板参数提供定制的模板实现，从而覆盖通用模板的默认实现。

有两种主要类型的模板特化：类模板特化和函数模板特化。

#### 类模板特化

```c++
// 通用类模板
template <typename T>
struct MyTemplate {
    void print() {
        std::cout << "Generic Template" << std::endl;
    }
};

// 类模板的特化版本（针对特定类型的定制实现）
template <>
struct MyTemplate<int> {
    void print() {
        std::cout << "Specialized Template for int" << std::endl;
    }
};

```

在上面的例子中，`MyTemplate` 是一个通用的类模板，但对于 `int` 类型，有一个特化版本，提供了定制的 `print` 实现。当你使用 `MyTemplate` 时，如果模板参数是 `int`，将使用特化版本的实现

#### 函数模板特化

```c++
// 通用函数模板
template <typename T>
void myFunction(T value) {
    std::cout << "Generic Template: " << value << std::endl;
}

// 函数模板的特化版本（针对特定类型的定制实现）
template <>
void myFunction<int>(int value) {
    std::cout << "Specialized Template for int: " << value << std::endl;
}

```

在这个例子中，`myFunction` 是一个通用的函数模板，但对于 `int` 类型，有一个特化版本，提供了定制的实现。当你调用 `myFunction` 时，如果传递的参数是 `int`，将使用特化版本的实现

### c++编译器常量

