---
layout: post
title: "使用dot绘图"
date: 2014-11-02 23:20:28 +0800
comments: true
categories: tools
---

### 0 DOT语言简介

[DOT语言](http://cosail.github.io/blog/2014/10/31/dotyu-yan/)

### 1 dot命令

使用如下命令来将.dot文件翻译成.jpg文件：
```
$ dot -Tjpg hello.dot -o hello.jpg
```
`-Tjpg表示生成jpg文件,-Tps表示生成ps文件，还有其他输出格式`

```
digraph G {
	size ="4,4";
	main [shape=box];         //节点main的形状为box
	main -> parse [weight=8];  //把边的weight设为8，默认为1
	parse -> execute;
	main -> init [style=dotted];  //此边用虚线绘制
	main -> cleanup;
	execute -> { make_string; printf} //快捷描述两条边
	init -> make_string;
	edge [color=red];        //设置边的默认颜色为红色
	main -> printf [style=bold,label="100 times"]; //粗边，附上标签“100 times”
	make_string [label="make a\nstring"];  //多行可使用'\n'
	/*设置节点默认属性：形状为box，填充，且指定颜色*/
	node [shape=box,style=filled,color=".7 .3 1.0"]; 
	execute -> compare;
}
```

### 2 绘图属性

node, edge, graph都有许多属性可以设置，从而可产生各种各样的图形，下面介绍这些属性 。

##### 2.1 节点属性设置示例

先看结果图:  
![](http://cosail.github.io/images/2.1.node.jpg")  

在上图中，节点有默认属性，显式的在"[...]"内设置属性来覆盖默认属性：

```
digraph G {
  default [] //默认:shape=ellipse,width=.75,height=.5,label=节点名("default")
  doublecircle [ shape=doublecircle    	/*双层圈形状*/
				,style=dotted			/*用点线绘制*/
				,color=red]				/*绘制颜色为红色(边框颜色)*/
  box [  shape=box		/*方框*/
		,penwidth=4.0	/*线宽*/
		,fontname=courier,fontsize=20,fontcolor=red	/*字体名,大小,颜色*/
		,height=0.1,width=0.1		/*图形高宽(里面的文字可能会胀大它)*/
		,label=BOX]			/*图形标签为"BOX"*/
  box2 [ shape=box
		,penwidth=4.0
		,fontname=courier,fontsize=20,fontcolor=red
		,height=0.1,width=0.1
		,label=BOX,margin="0.2,0.1"]	/*图形标签,标签周围的空白*/
  egg [  shape=egg		/*卵形状*/
		,style=filled		/*填充*/
		,fillcolor=green]	/*填充颜色为绿*/
  Msquare [  shape=Msquare		/*带切角的正方形*/
			,orientation=45.0]	/*旋转45度角*/
  box3 [ shape=box	
		,image="chair.jpg"		/*框内显示图片*/
        ,label="treechair",labelloc=t	/*图形的标签,标签位于上部(top)*/
		,fixedsize=true		/*图形为固定大小*/
		,width=1.2,height=1.0]	/*图形大小*/
  plaintext [shape=plaintext]	/*裸文本*/
  polygon [  shape=polygon,sides=4	/*多边形,四条边*/
			,distortion="-0.3"		/*变形(负数使上边短下边长):梯形*/
			,peripheries=3]		/*边框层数为3层*/
  polygon2 [ shape=polygon,sides=4
			,skew=0.6]			/*偏斜(正数使向右斜):平行四边形*/

  doublecircle -> Msquare -> polygon; egg->box2; //可用';'来分隔多条语句
  doublecircle -> box -> box2
  doublecircle -> egg -> box3
  doublecircle -> plaintext -> polygon2
}
```

##### 2.2 节点形状

下面是主要的基于多边形的形状列表:  
![](http://cosail.github.io/images/2.2-1.jpg")  
[更多](http://www.graphviz.org/doc/info/shapes.html)

还有一种基于记录的节点也非常重要，画数据结构的时候经常要用，包括**record**，**Mrecord**，后者和前者的唯一区别在于有圆角。还是先看图：  
![](http://cosail.github.io/images/2.2-2.jpg")  

**代码如下：**  
`注解：`**“端口”**表示边和节点的连接位置。  
```
digraph structs { 
	node [shape=record]; //节点的默认形状设为“record”

	/* 下面"<>"中为节点的端口,两字段的端口名分别为"f0","f1" */
	struct2 [label="<f0> one | <f1> two"];
	/* 形状为圆角record，'\ '为空格 */
	struct1 [shape=Mrecord, label="<f0> left | <f1> mid\ dle | <f2> right"]; 
	/* 用'\n'给标签文本换行，<here>是一个能和边相连的端口 */
	struct3 [label="hello\nworld | { b |{c|<here> d|e}| f} | g | h"]; 

	struct1:f1 -> struct2:f0; 
	/* 从节点struct1的端口f2,连接到节点struct3的端口here */
	struct1:f2 -> struct3:here; 
} 
```


