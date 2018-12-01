---
title: UVA818题：切断圆环链（暴力+DFS判环）
date: 2018-07-29 13:05:14
categories:
  - ACM
  - UVA
tags:
  - 暴力求解
  - DFS
  - 搜索
---
今天的这道题目是一道比较有意思的题目，里面有一些关于图的概念，话不多说，马上来看题目吧！
# 题目
首先先放上传送门：https://vjudge.net/problem/UVA-818

首先这道题目稀里哗啦说了一大堆，我来结合《算法竞赛入门经典》里的话来翻译一下：首先有n个圆环，其中有一些扣在了一起，现在需要打开尽量少的圆环，使所有的圆环可以组成一条链（所有打开的圆环需要再次闭合）。例如，有5个圆环，1-2,2-3,4-5，则需要打开一个圆环4，然后让圆环4去链接3和5，这样就可以组成一条链1-2-3-4-5（此题并不用排序的，任何顺序都可以，这个例子是凑巧了） 。题目中的意思就是让你算出最少要打开多少个圆环去重新连接。
![](/img/切断圆环链1.png)
我们来看看上图的输入，题目首先给了一个n代表1-n总共有n个圆环，然后便是不停地输入i，j这一对数字，代表i和j之间是连接的，输入没有上限，直到输入到i为-1，j为-1的时候停止，这样初始的条件就全部给出了，接下来就是我们的计算过程了。

首先我们需要枚举需要打开的环数，然后还要枚举哪些环需要被打开，然后每遇到一种情况就去判断一下可不可以形成一条链，如果可以就更新最小值，不可以就继续枚举下一种。

既然要枚举，那我们观察一下n的最大值，发现n最大不过15，发现这题我们可以使用位运算了。我们只要用一个15位的数字进行枚举，就可以枚举出所有的状态。那么枚举的问题就解决了。

下一个问题是怎么在当前枚举的情况下判断是不是可以形成一条链呢？我们通过观察，需要两个条件：1.所有节点连接的边绝对不可以超过两条。2.图中绝对不可以有环（环指的就是形成了回路）。从一条链的特性可以看出，如果一个节点连接了3条边及以上，那么绝对不可能构成链了，有环也是一样，链式结构都是一条路走到黑的，哪有还能回去的！

所以，我们只要统计出当前状态下没被选中的节点连接的边数，再通过dfs判定是否有环就可以得出答案了。

还有一点也是需要注意的，我们需要在dfs的时候顺便统计出这个图中有多少个分离的集合，因为我们既然要将n都串在一起，那么我们要使用n-1条边，那么这题也是一样的，我们在dfs的时候可以顺手统计出有多少个相互分离的集合，记为sum，那么如果我们的答案小于sum了，那么我们必须舍弃这个答案。这样我们才可以AC。
# 代码
{% codeblock lang:cpp %}
#include <iostream>
#include <cstring>
using namespace std;

int n,sum;
int map[20][20],vis[20];

int cal(int state){
    return state==0?0:cal(state>>1)+(state&1);
}

bool dfs(int state, int u, int par){
    vis[u] = 1;
    for(int v = 0; v < n; v++){
        if((state>>v)&1 || !map[u][v] || v == par)
            continue;
        if(vis[v] || !dfs(state, v, u))
            return false;
    }
    return true;
}

bool circle(int state){
    for(int i = 0; i < n; i++) {
        if (vis[i] || (state >> i) & 1)
            continue;
        sum++;
        if(!dfs(state,i,-1))
            return true;
    }
    return false;
}

bool degree(int state) {
    for (int i = 0; i < n; i++) {
        if ((state >> i) & 1)
            continue;
        int cnt = 0;
        for (int j = 0; j < n; j++) {
            if (i == j || (state >> j) & 1)
                continue;
            if (map[i][j])
                cnt++;
        }
        if (cnt > 2)
            return true;
    }
    return false;
}

int sol(){
    int ans=n-1;
    for(int state=0;state<(1<<n);state++){
        memset(vis,0,sizeof(vis));
        sum=0;
        if(circle(state)||degree(state))
            continue;
        int s=cal(state);
        if(s>=sum-1) {
            ans = min(ans, s);
        }
    }
    return ans;
}

int main(){
    for(int cas=1;cin>>n&&n;cas++){
        cout << "Set "<<cas;
        memset(map,0,sizeof(map));
        int a,b;
        while(true){
            cin>>a>>b;
            if(a==-1&&b==-1)
                break;
            map[a-1][b-1]=map[b-1][a-1]=1;
        }
        cout << ": Minimum links to open is " << sol() << endl;
    }
    return 0;
}
{% endcodeblock %}