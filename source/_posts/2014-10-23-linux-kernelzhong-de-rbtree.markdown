---
layout: post
title: "Linux kernel中的红黑树"
date: 2014-10-23 15:18:34 +0800
comments: true
categories: code
---

> 参考自Linux内核源码：  
> linux-source-3.13.0/Documentation/rbtree.txt  
> linux-source-3.13.0/include/linux/rbtree.h  
> linux-source-3.13.0/lib/rbtree.c

### 1. rbtree.txt中对红黑树的介绍

`红黑树`是一种自平衡的二叉搜索树，用来存储可排序的 key / value 对。
  
红黑树和 AVL 树相似，但提供更为实时的插入，删除（分别用最多两次，三次旋转操作完成平衡），有 O（log n）的查找时间复杂度。  

`Linux红黑树`为了优化速度，不是在红黑树节点中用指针指向被组织的数据结构，而是将红黑树节点嵌入到它组织的数据结构中（这样可以减少一次间接的地址引用，也有更好的缓存局部性）。  

使用者要自己写二叉树的查找，插入函数（使用那些已经实现的红黑树函数）。

### 2. Linux红黑树的使用及说明

##### 2.1 创建一个新的红黑树

红黑树中的数据节点就是一个包含 struct rb_node 成员的结构体：

```c
struct mytype {
    struct rb_node node;
    char *keystring;
};
```

那如何由红黑树上的节点 rb_node 去访问相应的数据呢？在头文件< linux/kernel.h > 中定义了一个宏 container_of，可用来从红黑树维护的节点得到包含着节点的结构体，然后就能访问该结构体的所有成员了：

```c
/**
 * container_of - 由指向结构体成员变量的指针转换为该结构体本身的指针(得到包含它的容器)。
 * @ptr:	指向 member 的指针
 * @type:   包含 member 的结构体的类型
 * @member:	成员变量名
 */
#define container_of(ptr, type, member) ({			\
	const typeof( ((type *)0)->member ) *__mptr = (ptr);	\
	(type *)( (char *)__mptr - offsetof(type,member) );})
```

`上面第 8 行获取结构体（类型名为 type）中的成员（成员变量名为 member）相对结构体起始地址的偏移量；第 9 行由成员的起始地址（ptr）减去其偏移量得到结构体的起始地址。`  
关于第 8 行的`typeof()`参看[这里](http://localhost:4000/blog/2014/10/23/typeofhuo-qu-biao-da-shi-lei-xing/)。

我们也可以用 rb_entry 宏，它只是换了个更友好的名字：

```c
#define	rb_entry(ptr, type, member)  container_of(ptr, type, member)
```

红黑树的根是一个 struct rb_root，看代码：

```c
struct rb_root {
	struct rb_node *rb_node;
};
#define RB_ROOT	(struct rb_root) { NULL, }

struct rb_root mytree = RB_ROOT;   // 初始化红黑树的根
```

##### 2.2 在红黑树中查找一个值

红黑树的查找很简单，就是二叉树的查找。前面说过，你需要写自己的查找函数，像这样：

```c
struct mytype *my_search(struct rb_root *root, char *string)
{
	struct rb_node *node = root->rb_node;

	while (node) {
		struct mytype *data = container_of(node, struct mytype, node);
        	int result;
        
        	result = strcmp(string, data->keystring);
        
        	if (result < 0)
        			node = node->rb_left;
        	else if (result > 0)
        			node = node->rb_right;
        	else
        			return data;
    }
            
    return NULL;
}
```

##### 2.3 在红黑树中插入一个值

先查找得到 data 应插入的位置，然后插入相应的新节点并调整树使其平衡。

```c
int my_insert(struct rb_root *root, struct mytype *data)
{
	struct rb_node **new = &(root->rb_node), *parent = NULL;

	/* 查找应插入的位置 */
	while (*new) {
		struct mytype *this = container_of(*new, struct mytype, node);
		int result = strcmp(data->keystring, this->keystring);

	    parent = *new;
		if (result < 0)
			new = &((*new)->rb_left);
		else if (result > 0)
			new = &((*new)->rb_right);
		else
			return FALSE; // 该值已存在！不用再插入
	}

	/* 插入新节点并调整树使其平衡 */
	rb_link_node(&data->node, parent, new);
	rb_insert_color(&data->node, root);

    return TRUE;
}
```

##### 2.4 在红黑树中删除或替换一个值

要删除一个已有节点： 

```c
/* 删除节点函数原型 */
void rb_erase(struct rb_node *victim, struct rb_root *tree);

/* 删除节点示例代码 */
struct mytype *data = mysearch(&mytree, "walrus");  // 查找节点位置

if (data) {
	rb_erase(&data->node, &mytree);  // 从红黑树删除节点
	myfree(data); // 释放结构体内存空间
}
```

替换一个已有节点： 

```c
void rb_replace_node(struct rb_node *old, struct rb_node *new, struct rb_root *tree);
```
`注意：替换节点不会重新排序，若替换后键值改变了，红黑树就可能不正确了。`

##### 2.5 按序遍历红黑树

提供了 4 个函数用来遍历红黑树（也可以是其它二叉树）：

```c
/* 函数原型 */
struct rb_node *rb_first(struct rb_root *tree);
struct rb_node *rb_last(struct rb_root *tree);
struct rb_node *rb_next(struct rb_node *node);
struct rb_node *rb_prev(struct rb_node *node);

/* 遍历红黑树示例代码 */
struct rb_node *node;
for (node = rb_first(&mytree); node; node = rb_next(node))
    printk("key=%s\n", rb_entry(node, struct mytype, node)->keystring);
```

### 3. 扩展的红黑树

扩展的红黑树在节点中存储了额外的数据，其值根据以该节点为根的子树的所有节点的内容计算得到。这额外的数据可以用来扩展红黑树的功能。红黑树扩展被实现为一个可选的特性，基于基础的红黑树。

##### 3.1 使用扩展红黑树

使用扩展红黑树时不直接包含<linux/rbtree.h>，而是包含头文件<linux/rbtree_augmented.h>(已经包含了前者)。

在执行插入节点操作时，我们必须更新到插入节点的路径上的额外数据（因为当红黑树为了平衡而进行结构调整后，路径上的额外数据就可能失效了）。怎么更新额外数据呢？可以用rb_augment_inserted()代替rb_insert_color()，若调整了树则会调用回调函数（由我们提供）来更新受影响的子树上的额外数据。

在执行删除节点操作时，用rb_erase_augmented()代替rb_erase()。也会调用回调函数来更新受影响的子树上的额外数据。


##### 3.2 回调函数

这里的回调函数有我们使用者提供，处理对象是红黑树上的额外数据。

回调函数可通过设置struct rb_augment_callbacks来提供，包括三个回调函数：  
- 传递函数：更新某节点和其祖先的额外数据，直到另一指定节点或根（当指定节点为NULL时）。
- 复制函数：拷贝一个节点上的额外数据到第二个节点。  
- 交替函数：拷贝一个节点上的额外数据到第二个节点，并更新以第二个节点为根的子树上的额外数据。

##### 3.3 应用扩展红黑树构造区间树

用红黑树可以实现**区间树**。一般情况下，红黑树节点中存放着 key/value 对。那怎么表示区间[lo, hi]？我们可以在节点中增加一个***额外信息***。如在红黑树节点中存放整型值 lo 作为下界(也是key), 额外的 hi 来表示上界。另外在节点中增加一个***额外信息*** —— 在每个节点中保存该节点的所有后代中最大的 hi(记为__subtree_last)。

```c
struct interval_tree_node *
interval_tree_first_match(struct rb_root *root,
			  unsigned long start, unsigned long last)
{
	struct interval_tree_node *node;

	if (!root->rb_node)
		return NULL;
	node = rb_entry(root->rb_node, struct interval_tree_node, rb);

	while (true) {
		if (node->rb.rb_left) {
			struct interval_tree_node *left =
				rb_entry(node->rb.rb_left, struct interval_tree_node, rb);
			if (left->__subtree_last >= start) {
				/*
				 * Some nodes in left subtree satisfy Cond2.
				 * Iterate to find the leftmost such node N.
				 * If it also satisfies Cond1, that's the match
				 * we are looking for. Otherwise, there is no
				 * matching interval as nodes to the right of N
				 * can't satisfy Cond1 either.
				 */
				node = left;
				continue;
			}
		}
		if (node->start <= last) {		/* Cond1 */
			if (node->last >= start)	/* Cond2 */
				return node;	/* node is leftmost match */
			if (node->rb.rb_right) {
				node = rb_entry(node->rb.rb_right,
					struct interval_tree_node, rb);
				if (node->__subtree_last >= start)
					continue;
			}
		}
		return NULL;	/* No match */
	}
}

Insertion/removal are defined using the following augmented callbacks:

static inline unsigned long
compute_subtree_last(struct interval_tree_node *node)
{
	unsigned long max = node->last, subtree_last;
	if (node->rb.rb_left) {
		subtree_last = rb_entry(node->rb.rb_left,
			struct interval_tree_node, rb)->__subtree_last;
		if (max < subtree_last)
			max = subtree_last;
	}
	if (node->rb.rb_right) {
		subtree_last = rb_entry(node->rb.rb_right,
			struct interval_tree_node, rb)->__subtree_last;
		if (max < subtree_last)
			max = subtree_last;
	}
	return max;
}

static void augment_propagate(struct rb_node *rb, struct rb_node *stop)
{
	while (rb != stop) {
		struct interval_tree_node *node =
			rb_entry(rb, struct interval_tree_node, rb);
		unsigned long subtree_last = compute_subtree_last(node);
		if (node->__subtree_last == subtree_last)
			break;
		node->__subtree_last = subtree_last;
		rb = rb_parent(&node->rb);
	}
}

static void augment_copy(struct rb_node *rb_old, stru，ct rb_node *rb_new)
{
	struct interval_tree_node *old =
		rb_entry(rb_old, struct interval_tree_node, rb);
	struct interval_tree_node *new =
		rb_entry(rb_new, struct interval_tree_node, rb);

	new->__subtree_last = old->__subtree_last;
}

static void augment_rotate(struct rb_node *rb_old, struct rb_node *rb_new)
{
	struct interval_tree_node *old =
		rb_entry(rb_old, struct interval_tree_node, rb);
	struct interval_tree_node *new =
		rb_entry(rb_new, struct interval_tree_node, rb);

	new->__subtree_last = old->__subtree_last;
	old->__subtree_last = compute_subtree_last(old);
}

static const struct rb_augment_callbacks augment_callbacks = {
	augment_propagate, augment_copy, augment_rotate
};

void interval_tree_insert(struct interval_tree_node *node,
			  struct rb_root *root)
{
	struct rb_node **link = &root->rb_node, *rb_parent = NULL;
	unsigned long start = node->start, last = node->last;
	struct interval_tree_node *parent;

	while (*link) {
		rb_parent = *link;
		parent = rb_entry(rb_parent, struct interval_tree_node, rb);
		if (parent->__subtree_last < last)
			parent->__subtree_last = last;
		if (start < parent->start)
			link = &parent->rb.rb_left;
		else
			link = &parent->rb.rb_right;
	}

	node->__subtree_last = last;
	rb_link_node(&node->rb, rb_parent, link);
	rb_insert_augmented(&node->rb, root, &augment_callbacks);
}

void interval_tree_remove(struct interval_tree_node *node,
			  struct rb_root *root)
{
	rb_erase_augmented(&node->rb, root, &augment_callbacks);
}
```

