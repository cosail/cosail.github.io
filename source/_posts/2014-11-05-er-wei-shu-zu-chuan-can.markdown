---
layout: post
title: "二维数组如何作为参数传给函数？"
date: 2014-11-05 20:07:14 +0800
comments: true
categories: c
---

### 二维数组传参

有一个二维数组:
```c
  int array[3][3];
```
想把它传给函数 func() 当参数，容易习惯性的写出下面这两种错误的定义：
```c
  void func(int **array) {}
  void func(int array[][]) {}
```
参数array在前一个定义中类型为`int *(*)`；而后一个array类型是错误的，连编译器也不认识。

正确的定义应该如下面这样：  
```c
  void func(int array[][3]) {}
  void func(int (*array)[3]) {}
```
也就是说，上面的两个array参数表示的是同一种类型`int (*)[3]`。

二维数组其实只是一个**指向一维数组的指针**，而二级指针是**指向指针的指针**，二者并不是相容的类型。

### 三维或更高维

对于三维或更高维的数组如何作为参数传递呢?除了第一维，其它维度都需要显式指定。如4维数组传参：  
```
  void func(int array[][3][3][3]) {}
```

### 数组和指针的差别

数组和指针的差别在下面例子中有所体现：  
```c
  int arr[3]; 
  int *p = arr;
  int lenp = sizeof(p);     //lenp值为4
  int lenarr = sizeof(arr);     //lenarr值为12
```

