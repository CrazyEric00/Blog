---
title: UVA816题（ACM/ICPC 2000年World Final题目）
url: 63.html
id: 63
categories:
  - ACM
  - UVA
tags:
---

#### 今天这道题，高难度预警！！！！！！！！！！！！！！！！！！！！！！！！！！

就跟题目写的一样，这是2000年ACM ICPC （国际大学生程序设计竞赛）总决赛的一道题目，被收录在刘汝佳的《算法竞赛入门经典》中。由于刘汝佳的风格（只给一部分核心算法），所以，我花了几乎一天时间来啃这一道难题。首先让我们来看看题目：![](http://39.107.233.145/wp-content/uploads/2018/05/1.png)![](http://39.107.233.145/wp-content/uploads/2018/05/2.png)![](http://39.107.233.145/wp-content/uploads/2018/05/3.png) 估计大家都是一脸懵逼。。。所以下面给出有道翻译的中文翻译（不保证正确，不过玩ACM的还是多看看英语为好）：   **         1999年世界总决赛的比赛中有一个基于骰子迷宫的问题。当时的问题写完后，评委们无法找到骰子迷宫的原始来源。不久然而，在比赛之后，罗伯特·阿博特先生，无数迷宫的创造者和作家主题，与比赛评委联系，并确认自己是骰子迷宫的发起者。我们很遗憾我们不相信阿博特先生在去年的问题陈述中提出的最初概念。但是我们是很高兴地告诉大家，阿博特先生已经将他的专业知识与他的原创作品相结合未发表的演练箭头迷宫。和大多数迷宫一样，一个穿越箭头迷宫是通过从一个十字路口移动到另一个十字路口来完成的直到到达目标十字路口。当每个交点从一个给定的方向接近时，在十字路口入口附近有一个标志，指示出十字路口的方向。这些方向总是向左，向前或向右，或者是它们的任何组合。** **         图1展示了一个穿越箭头迷宫。交叉部分被标识为(行、列)对，左上角为(1,1)图1的入口交叉口为(3,1)，目标为十字路口是(3、3)。你从(3,1)向北移动开始迷宫。当你从(3,1)到(2,1)在(2,1)处的标志表明，当你从南方(向北旅行)接近(2,1)时，你可以继续前进只有向前去。继续往前走，你会走向(1,1)当你从(1,1)开始时的标志南部表示你只能通过右转退出(1,1)。现在你转向东方从(1,1)走向(1,2)到目前为止还没有选择。这也是你继续从(1,2)到(2,2)到(2,3)到(1,3)。然而，当你从(1,3)向西移动时朝向(1,2)，你可以选择继续直走或向左拐。继续直接将你朝(1,1)方向走，向左拐就会向南走(2,2)。实际的(唯一的)解决方案这个迷宫的交叉口顺序如下:(3,1)(1,1)(1,2)(2,2)(2,3)(1,3)(1,2)(1,1)(1,1)(2,1) (2,2) (1,2) (1,3) (2,3) (3,3)您必须编写一个程序来解决有效的遍历箭头迷宫。解决一个迷宫意味着可能)找到一条穿过迷宫的路线，使入口在规定的方向上，并且以我们的目标。当然，这条路线不应该超过必要的长度。**

### **Input：**

**        输入文件将由一个或多个箭头迷宫组成。每个迷宫描述的第一行包含****迷宫的名字，它是一个不超过20个字符的字母数字字符串。下一行按以下顺序包含起始行、起始列、起始方向和目标行，最后是目标列。所有的元素都由一个空格分隔。这个问题迷宫的最大尺寸是9×9，所以所有的行号和列号都是1到9的个位数。起始方向为N、S、E或W，表示北、南、东、西。** **        迷宫的所有其他输入行都有以下格式:两个整数，一个或多个字符组，还有一个前哨星号，同样都用一个空格分隔。这些整数表示行和列，分别是一个迷宫的交叉口。每个字符组在那个交叉点代表一个符号。组中的第一个字符是“N”、“S”、“E”或“W”，表示符号的移动方向。例如，' S '表示这是在向南方旅行时看到的标志(这是在十字路口的北入口张贴告示）。跟随第一个方向的字符是三个箭头。它们可以是“L”、“F”或“R”，分别表示左、前、右。交点的列表由第一列中包含单个零的行结束。下一个输入线开始下一个迷宫，等等。输入的末尾是一行中的“end”**

### **Output：**

**      对于每个迷宫，输出文件应该包含一个带有迷宫名称的行，后面跟着一个或多个迷宫迷宫走不出去输出“‘No Solution Possible”。迷宫名称应该从第1列开始，所有其他行都应该从第3列开始，即。缩进两个空格。解决方案是否应该以格式(R,C)中的交集列表的形式输出从目标开始，应该用一个单独的空格来分隔，并且除了最后一行之外，所有的解决方案都应该如此正好包含10个十字路口**

### **Note：**

**罗伯特·阿博特演练箭头**

**迷宫实际上是为大规模设计的建设,而不是纸。尽管他的迷宫没有出版，有些已经出版实际上是建立。其中之一正在展出在亚特兰大一家博物馆。其他人由美国迷宫建造。公司在过去的两个夏天。作为他们的名字表明这些迷宫是有意的。** **对于喜欢冒险的人，请参见图2 Robert Abbotts亚特兰大迷宫图。解决它是相当困难的，即使是在你对整个迷宫有一个大致的了解。想象一下试着解决这个问题在迷宫中行走，只能看见一次一个标志!罗伯特·阿伯特自己表示迷宫太复杂大多数人在结束前就放弃了。在那些没有的人中放弃的是Donald Knuth，它带走了他大约30分钟就能解开迷宫。**

### 输入输出见上面的英文原题

首先，这道题的规则极其麻烦（所以他是世界总决赛的题。。。），让我们冷静地分析一下题目的主要条件。