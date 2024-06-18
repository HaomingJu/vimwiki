`SFINAE: Substitution Failure Is Not An Error`的缩写, 意思是 “替换失败并不是错误”.

就是说，匹配重载的函数/类时如果没有匹配上，编译器并不会报错;

相应的，这个函数/或类就不会作为候选.这是一个 C++11 的新特性，也是`enable_if`最核心的原理。

SFANAE 是 C++ 语言标准中固有的一部分，是模板编程中的一个核心特性。

SFINAE 不是可以选择开启或关闭的功能， 而是编译器在处理模板时自然遵循的原则。


# 什么是替换 (What is Substitution)

```
template<typename T>
void func(T t) {}

func(42); // 调用func, 编译器会将T替换为int, 这个替换过程是成功的
```

# 什么是替换失败(What is Substitution Failure)

SFANEA的关键在于,如果替换过程中发生了失败，这种失败并不会**立即**导致编译错误.

相反,这个特定的模板实例会被编译器忽略，编译器会继续寻找其他可能的模板匹配。

失败的例子可能包括：

- 尝试访问不存在的类型成员
- 尝试执行不支持的操作，如对非数值类型执行数学运算
- 用错误的类型作为模板参数

```
template<typename T>
typename T::type func(T t) {}

struct X {};

int main() {
    X x;
    func(x);    // 这里发生替换失败，因为X没有名为type的成员
    return 0;
}

```

# 例子

```
class person{};
class dog{};

// 泛型模板
template <typename T>
struct is_person {
    static const bool value = false;
}

// 模板特化
template <>
struct is_person<person>{
    static const bool value = true;
}


void func(int a){
    std::cout << "int type" << std::endl;
}

// 当T为person时， 被调用
template <typename T, std::enable_if_t<is_person<T>::value, T>* = nullptr>
void func(T t) {
    std::cout << "is person class" << std::endl;
}

// 当T不是person时， 被调用
template <typename T, std::enable_if_t<!is_person<T>::value, T>* = nullptr>
void func(T t){
    std::cout << "is not person class" << std::endl;
}


int main() {
}


```


# 参考

[CSDN博文参考](https://blog.csdn.net/baiyu33/article/details/135908424?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-5-135908424-blog-135781243.235^v43^control&spm=1001.2101.3001.4242.4&utm_relevant_index=8)























