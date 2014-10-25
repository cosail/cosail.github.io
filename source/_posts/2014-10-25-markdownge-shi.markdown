---
layout: post
title: "markdown格式"
date: 2014-10-25 21:23:16 +0800
comments: true
categories: Text-Edit
---


Markdown是一个Web上使用的文本到HTML的转换工具，可以通过简单、易读易写的文本格式生成结构化的HTML文档。  
Markdown的语法简洁明了、学习容易，而且功能比纯文本更强，因此很多人用它写博客。

下面为简单使用介绍:

## 1. 标题

使用#，可表示1-6级标题。

# 一级标题
###### 六级标题

## 2. 段落

段落的前后要有空行，所谓的空行是指没有文字内容.
若想在段内强制换行的方式是使用两个以上空格加上回车（引用中换行省略回车）。

## 3. 区块引用

在段落的每行或者只在第一行使用符号>,还可使用多个嵌套引用，如：
>区块引用
>>嵌套引用

## 4. 代码区块

代码区块的建立是在每行加上4个空格或者一个制表符（如同写代码一样）。如 普通段落：
普通
void main()
{
printf("Hello, Markdown.");
}

代码区块：

    代码
    void main()
    {
      printf("Hei, you.");
    }
注意:顶端需要和普通段落之间存在空行。

## 5. 强调

在强调内容两侧分别加上*或者_，如:  

*斜体*，_斜体_  
**粗体**，__粗体__  
***粗斜体***，___粗斜体___

## 6. 列表

使用-、+、或*标记无序列表，如：
- 1.1
- 1.2
+ 1.1
+ 1.2
* 1.1
* 1.2  
注意：标记后面最少有一个空格或制表符。

有序列表的标记方式是将上述的符号换成数字,并辅以.，如:

1. 貌似和纯文本没区别
2. 但复制一下就知道还是有区别的  
注意:顶端需要和普通段落之间存在空行。

## 7. 分割线

分割线最常使用就是三个或以上*，还可以使用-和_。
***
一区1:
******
一区2
***

------------------
二区1:
---
二区2
------------------

## 8. 链接

链接可以由两种形式生成：行内式和参考式。 行内式：  
    [百度](http://www.baidu.com "Markdown")。


参考式：  
[百度][1]  
[百度][2]
[1]:http://www.baidu.com "Markdown"
[2]:http://www.baidu.com "Markdown"  
注意：上述的[1]:http://www.baidu.com "Markdown"不要出现在区块中。

## 9. 图片

添加图片的形式和链接相似，只需在链接的基础上前方加一个！。

![](https://github.com/cosail/Algorithms/blob/master/chairTree.jpg)

## 10. 符号'`', '\'

'`'起到标记作用。如：

`ctrl+a`

'\'相当于反转义作用。使符号成为普通符号。  
\*普通\*

## 参考:
[https://github.com/younghz/Markdown](https://github.com/younghz/Markdown)
## 链接:
[在线Markdown编辑](https://www.zybuluo.com/mdeditor)  
[CommonMark](http://commonmark.org/): A strongly specified, highly compatible implementation of Markdown.  
[The official specification for CommonMark. ](http://jgm.github.io/stmd/spec.html)
