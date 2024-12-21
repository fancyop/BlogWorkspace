# `const char *`、`char const *`、`char * const`、`const char * const`的区别

验证工具及环境
```
Apple clang version 14.0.3 (clang-1403.0.22.14.1)
Target: x86_64-apple-darwin22.6.0
```

这些声明涉及到指针和 `const` 修饰符的不同组合，以下是它们之间的区别：

## `const char *` 和 `char const *`

- 两者是等价的，都表示指向常量字符的指针。
- 指针本身是可变的，但不能通过指针修改所指向地址上的字符值。

```cpp
const char *ptr1;
char const *ptr2;

// 合法，可以修改指针
ptr1 = "Hello";
ptr2 = "Hello";

// 错误，不能修改所指向地址上的字符值，编译时错误为 error: read-only variable is not assignable
// ptr1[0] = 'X';
// ptr2[0] = 'X'; 
```

## `char * const`

- 表示一个常量指针，指针本身是不可变的，但可以通过指针修改所指向地址上的字符值。

```cpp
char greeting[] = "Hello";
char * const ptr3 = greeting;

// 合法，通过指针修改所指向地址上的字符值
ptr3[0] = 'X';

// 错误，不能修改指针的地址，编译时错误为 error: cannot assign to variable 'ptr3' with const-qualified type 'char *const'
// ptr3 = "World";
```

## `const char * const`

- 表示一个指向常量字符的常量指针，指针本身和所指向地址上的字符值都是不可变的。

```cpp
const char greeting[] = "Hello";
const char * const ptr4 = greeting;

// 错误，不能通过指针修改所指向地址上的字符值，编译时错误为 error: cannot assign to variable 'ptr4' with const-qualified type 'const char *const'
// ptr4[0] = 'X';

// 错误，不能修改指针的地址，编译时错误为 error: cannot assign to variable 'ptr4' with const-qualified type 'const char *const'
// ptr4 = "World";
```
