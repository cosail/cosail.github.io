---
layout: post
title: "用typeof获取表达式类型"
date: 2014-10-23 17:09:08 +0800
comments: true
categories: c
---

### typeof 关键字

`typeof`是 C 语言的一个新扩展，它告诉编译器你要使用某个表达式的类型（而不需要显示的指明该类型），如：

```c
typeof( x[0](1) )  var;
```
`上面定义了一个变量var，它的类型为：函数指针数组`

也可以用类型而不是表达式，如：

```c
typeof( int * )  var;
```
`变量 var 的类型为： int *。这个好像用处不大？`

一般情况下用`typeof`，如果要于ISO C兼容的话，用双下划线的形式`__typeof__`。  

你可以在能用 typedef 的所有地方使用 typeof，如：类型声明，类型转换，或 sizeof()，typeof()中。例如：  

```c
typeof (*x) y;      // y 的类型为：指针 x 指向的类型  
typeof(*x) y[4];  // 指针 x 指向的类型的数组类型  
typeof (typeof (char *)[4]) y;  // 字符指针数组类型
```

如果想把T定义成一个表达式的类型：

```c
typdef typeof(expr) T;
```

### 限制

typeof 中的类型名不能包含 extern 或 static，不过允许包含类型限定符 const 或 volatile。  

```c
typeof(extern int) a;       // 不可以
extern typeof(const int) b = 3;  // 可以
```

### 应用

gcc 编译器支持该扩展，在linux内核中应用非常广泛，如 linux-source-3.13.0/include/linux/kernel.h 文件中的两个宏：

下面定义一个安全的 max 宏，避免参数被多次展开的错误。  

```c
#define max(x, y) ({                         \
    typeof(x) _max1 = (x);                  \
    typeof(y) _max2 = (y);                  \
    (void) (&_max1 == &_max2);              \
    _max1 > _max2 ? _max1 : _max2; })
```

下面第2行获取结构体（类型名为 type）中的成员（成员变量名为 member）相对结构体  
起始地址的偏移量，第3行由成员的起始地址（ptr）减去其偏移量得到结构体的起始地址：

```c
#define container_of(ptr, type, member) ({			\
	const typeof( ((type *)0)->member ) *__mptr = (ptr);	\
	(type *)( (char *)__mptr - offsetof(type,member) );})
```

### 类似的__auto_type

可以在 gnu c 中使用`__auto_type`来声明一个变量的类型，如

```c
int a = 3;
__auto_type  var = a;   // 类似C++里的 auto
```

[参考](https://gcc.gnu.org/onlinedocs/gcc/Typeof.html)

