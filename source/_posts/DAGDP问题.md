---
title: DAGDP问题
date: 2018-08-23 14:25:38
categories:
  - ACM
tags:
  - DP
---
终于到了激动人心的DP问题，今天我们先来介绍一些简单的DP，首先我们需要向没有接触过这个东西的同学（说明你们脑细胞还没有大面积死亡啊哈哈）科普一下。
# 动态规划（DP）
动态规划(dynamic programming)是运筹学的一个分支，是求解决策过程(decision process)最优化的数学方法。20世纪50年代初美国数学家R.E.Bellman等人在研究多阶段决策过程(multistep decision process)的优化问题时，提出了著名的最优化原理(principle of optimality)，把多阶段过程转化为一系列单阶段问题，利用各阶段之间的关系，逐个求解，创立了解决这类过程优化问题的新方法——动态规划。1957年出版了他的名著《Dynamic Programming》，这是该领域的第一本著作。

如果dp基础不行或者压根没听说过的可以看一看这一篇漫画，我这里不做解释了
https://mp.weixin.qq.com/s?__biz=MzIxMjE5MTE1Nw==&mid=2653190796&idx=1&sn=2bf42e5783f3efd03bfb0ecd3cbbc380&chksm=8c990856bbee8140055c3429f59c8f46dc05be20b859f00fe8168efe1e6a954fdc5cfc7246b0&scene=21#wechat_redirect

我们今天介绍的dp虽然简单，但也并不能算最基础的，如果想要入门练练手的建议百度“数字三角形dp问题”练一练，我这里就不写了。我们赶快进入今天的正题，今天我介绍的是DAG（有向无环图），DAG有一个特质就是适合dfs，可以往回一直深搜下去，所以，有一些问题我们就可以将其归纳为在有向无环图上dp。

# 嵌套矩形问题
第一题是NYOJ的一道题目，我先放一下传送门：http://acm.nyist.edu.cn/JudgeOnline/problem.php?pid=16

首先题目意思还是非常清楚的，我们怎么对其进行正确的搜索才可以找到正确的解呢，答案就是建图。我们将每一个矩形用对组保存起来，然后我们可以编写一个函数判断相互的包含关系，只要包含，我们就将这两个矩形的序号连接一条边，这样我们可以建立一张图，更好的消息是，由于包含关系（假设a包含b，b包含c，c不可能包含a），所以我们得到的图一定是有向且无环的，这正是dp的大好时机啊，所以我们可以立刻得出下面的代码
```cpp
#include <iostream>
#include <cstring>
using namespace std;
typedef pair<int,int> P;

P a[1010];
int d[1010];
int map[1010][1010];
int n;


bool judge(P a,P b){
    if((a.first<b.first&&a.second<b.second)||(a.second<b.first&&a.first<b.second))
        return true;
    else
        return false;
}

int dp(int i){
    if(d[i]>0)//采用记忆化数组，防止重复计算
        return d[i];
    d[i]=1;//d[i]初始为1
    for(int j=0;j<n;j++)
        if(map[i][j])
            d[i]=max(d[i],dp(j)+1);
    return d[i];
}

int main(){
    ios::sync_with_stdio(false);
    int T;
    cin>>T;
    while(T--) {
        while (cin >> n) {
            memset(a, 0, sizeof(a));
            memset(d, 0, sizeof(d));
            memset(map, 0, sizeof(map));
            for (int i = 0; i < n; i++)
                cin >> a[i].first >> a[i].second;
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    if (judge(a[i], a[j]))
                        map[i][j] = 1;
                }
            }
            for (int i = 0; i < n; i++)
                dp(i);
            int mx = d[0];
            for (int i = 0; i < n; i++)
                if (d[i] > mx)
                    mx = d[i];
            cout << mx << endl;
        }
    }
    return 0;
}
```
上面的代码使用了记忆化搜索，避免了递归很多次算同一个答案。
# 硬币问题
第二题也挺有意思的，首先我们有n种硬币，面值各不相同v[0],v[1],v[2]......，每种数量都无限，我们给定一个非负整数s，问可以选用多少个硬币，使得面值刚好为s，输出硬币数量的最大值和最小值，当然我们假定总有一种情况成立。

首先我们发现这道题跟上一道有点像但是又有一点不一样。首先我们有了起点和终点，就是s和0，那么我们只要算出起点到终点的路径即可，所以这道题的关键还是在于建图和找到状态转移方程（上一道的状态转移比较简单直观）。

我们将问题分解为一些子问题，首先我们把问题变成“每一个节点i到0的最短/最长距离”，然后每一个子问题的最优解归纳到s自然是最优解，那么我们可以推出一个状态转移方程了：** d[s]=max(d[s],dp(s-v[i])+1) **

然后我们就可以得到这么一个代码
```cpp
#include <iostream>
#include <cstring>
using namespace std;

int n,s;
int v[1000];
int d[10000];

int dpmax(int s){
    if(d[s]!=-1)
        return d[s];
    d[s]=-1000;//为了和没访问过的-1值区分开，取一个尽量小的值
    for(int i=0;i<n;i++)
        if(s>=v[i])
            d[s]=max(d[s],dpmax(s-v[i])+1);
    return d[s];
}

int dpmin(int s){
    if(d[s]!=-1)
        return d[s];
    d[s]=1000;//为了和没访问过的-1值区分开，取一个尽量大的值
    for(int i=0;i<n;i++)
        if(s>=v[i])
            d[s]=min(d[s],dpmin(s-v[i])+1);
    return d[s];
}

int main(){
    ios::sync_with_stdio(false);
    while(cin>>n){
        memset(v,0,sizeof(v));
        memset(d,-1,sizeof(d));//初始化成-1，因为路径长度有可能为0
        for(int i=0;i<n;i++)
            cin>>v[i];
        cin>>s;
        d[0]=0;//不要忘记把终点设为0
        cout << "最大为" << dpmax(s) << endl;
        memset(d,-1, sizeof(d));
        d[0]=0;
        cout << "最小为" << dpmin(s) << endl;
    }
    return 0;
}
```

当然我们会发现使用d数组又标记答案又标记是否访问会非常麻烦，所以我们倒不如大方一点，再开一个数组来判断是否访问，这样虽然牺牲了空间，但是代码更加通俗易懂
```cpp
#include <iostream>
#include <cstring>
using namespace std;

int n,s;
int v[1000];
int d[10000];
int vis[10000];

int dpmax(int s){
    if(vis[s])
        return d[s];
    vis[s]=1;
    d[s]=-1000;//为了和没访问过的-1值区分开，取一个尽量小的值
    for(int i=0;i<n;i++)
        if(s>=v[i])
            d[s]=max(d[s],dpmax(s-v[i])+1);
    return d[s];
}

int dpmin(int s){
    if(vis[s])
        return d[s];
    vis[s]=1;
    d[s]=1000;//为了和没访问过的-1值区分开，取一个尽量大的值
    for(int i=0;i<n;i++)
        if(s>=v[i])
            d[s]=min(d[s],dpmin(s-v[i])+1);
    return d[s];
}

int main(){
    ios::sync_with_stdio(false);
    while(cin>>n){
        memset(v,0,sizeof(v));
        memset(vis,0,sizeof(vis));
        memset(d,-1,sizeof(d));//初始化成-1，因为路径长度有可能为0
        for(int i=0;i<n;i++)
            cin>>v[i];
        cin>>s;
        d[0]=0;//不要忘记把终点设为0
        cout << "最大为" << dpmax(s) << endl;
        memset(d,-1, sizeof(d));
        memset(vis,0,sizeof(vis));
        d[0]=0;
        cout << "最小为" << dpmin(s) << endl;
    }
    return 0;
}
```
让我们更进一步，直接放弃递归，其实观察敏锐的同学就可以看出来端倪，我们完全可以把方向反过来进行递推升级，我们可以把代码变得更加简洁和高效
```cpp
#include <iostream>
#include <cstring>
using namespace std;
const int INF=1e8;

int main(){
    int n,s;
    ios::sync_with_stdio(false);
    while(cin>>n){
        int *v=new int[n+10];
        for(int i=0;i<n;i++)
            cin>>v[i];
        cin>>s;
        int *maxv=new int[s+10];
        int *minv=new int[s+10];
        maxv[0]=minv[0]=0;//设置好终点
        for(int i=1;i<=s;i++){
            maxv[i]=-INF;
            minv[i]=INF;
        }//初始化数值
        for(int i=1;i<=s;i++){
            for(int j=0;j<n;j++){
                if(i>=v[j]){
                    minv[i]=min(minv[i],minv[i-v[j]]+1);
                    maxv[i]=max(maxv[i],maxv[i-v[j]]+1);
                }
            }
        }
        cout << "最大为" << maxv[s] << endl;
        cout << "最小为" << minv[s] << endl;
    }
    return 0;
}
```
这样我们就完成了整个动态规划的升级了，是不是看到这里感觉非常烧脑呢？

dp问题就是这样，灵活多变，非常虚无缥缈，但是它确实是解决问题的好办法，希望在不断地坚持中你我的DP解题实力都可以有所提高。下一篇博客再见！