---
layout: post
title: "为什么在struct里定义宏"
date: 2014-10-22 22:18:21 +0800
comments: true
categories: C
---

有时会看到结构体里有宏定义，如下：
```c
struct rb_node  
{  
    unsigned long  rb_parent_color;  
#define RB_RED      0  
#define RB_BLACK    1  
    struct rb_node *rb_right;  
    struct rb_node *rb_left;  
} __attribute__((aligned(sizeof(long))));  
```
这是Linux内核中红黑树节点的定义，其中 rb_parent_color 的第0位用来存储节点的颜色（红/黑）。把两种颜色的宏定义放在结构体定义内，应该是为了增强代码可读性。  

因为对宏的处理是在预编译时完成的，而预处理器仅仅作简单的替换。宏从其定义处开始，至文件的尾部或取消宏(#undef)为止是可见的，struct的作用域对宏的定义没影响。  

可以用 gcc -E 命令来只做预处理而不编译，来查看宏处理后的程序源代码。
















