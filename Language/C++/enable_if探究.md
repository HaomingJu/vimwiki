# 定义

Ref: https://en.cppreference.com/w/cpp/types/enable_if

```
template< bool B, class T = void > struct enable_if; // since C++11
```




```
std::cout << std::is_same<int, int>::value << std::endl;  // 输出：true
std::cout << std::is_same<int, double>::value << std::endl;  // 输出：false
std::cout << std::is_integral<int>::value << std::endl;  // 输出：true
std::cout << std::is_integral<double>::value << std::endl;  // 输出：false
std::cout << std::is_pointer<int*>::value << std::endl;  // 输出：true
std::cout << std::is_pointer<int>::value << std::endl;  // 输出：false
```
