---
title: UVA1601题：同步推箱子问题（双向BFS）
date: 2018-07-20 18:15:18
categories:
  - ACM
  - UVA
tags:
  - BFS
  - 双向BFS
---
高产的我又来了，今天讲的是一个老盆友——BFS。

##### 算法介绍

BFS（广度优先搜索）是一种由近及远，步步为营的算法，但是，在某些分支十分庞大的途中，可能我们遍历了绝大部分的节点才找到答案。那么对于某些要求比较高的场景，你可能就爆炸了。

幸运的是，几乎很多BFS都可以该写成双向BFS，这么提高效率的算法，是时候来了解一下了！

我们以前所用的单向BFS，都是用一个队列进行访问，那么我们举个例子，单向的BFS相当于一个人一直走，走到终点，而双向BFS，相当于有两个人，一个人从起点开始走，一个而你从终点开始走，直到两个人碰到为止。

下图是双向BFS节省效率的证明：
![](/img/同步推箱子1.jpg)
![](/img/同步推箱子2.jpg)
##### 题目

说了这么多我们来看看今天的题目，UVA1601题，传送门在此：https://vjudge.net/problem/UVA-1601

这个题首先给了一张图，并不是昨天的隐式图问题。题目中给了你一些字母，有小写有大写，你的任务便是要将所有小写字母通过移动的方式与所有大写字母重合，并且是大家一起动算一步（可以有不动甚至全部不动，移动方向任意，不是所有字母一个方向），题目中还告诉了一个重要的信息，图中每2x2个方格中必定有一个障碍物（即'#'），这是一个非常关键的信息，这说明了障碍物非常多，所以我们如果用他给的图来BFS，肯定会把很多时间都浪费在判断障碍物上，所以，其实可以将障碍物“抽掉”，将起点终点与空地专门用数字标记关系，然后做成一张没有障碍物存在的新图，这样就不用担心BFS的时候耗费时间了！
题目中的举例：
![](/img/同步推箱子3.png)
这个题目中状态非常之多，所以题目中给了12000ms，这道题其实用单向BFS也是没有问题的，但是如果遇到比较严格的题目还是使用双向BFS优化为好。

另外，有一个小注意点，我们在使用双向BFS的正确做法是交替逐层搜索，检查完该层所有节点，得到最优解，因为如果我们每次只访问一个节点就移交控制权，不可避免地会出现搜索情况不全面出现问题（具体证明与反例可以自行百度）。

##### 单向BFS
{% codeblock lang:cpp %}
#include <iostream>
#include <cstring>
#include <vector>
#include <cstdio>
#include <queue>
using namespace std;
typedef pair<int,int> P;
const int dx[]={0,-1,1,0,0};
const int dy[]={0,0,0,-1,1};
int w,h,n;
char map[20][20];
int step[200];
int st[3],ed[3];
vector<int> G[200];
int dist[200][200][200];//该状态的步数

inline int ID(int a,int b,int c){
    return (a << 16)|(b << 8)|c; //使用每个状态占8个二进制位的技术编码
}

inline bool conflict(int a,int b,int na,int nb){
    return (na==nb||(a==nb&&b==na)); //当两个移动的目标相同或两两换位时冲突
}

int bfs(){
    queue<int> que;//只使用一个队列
    que.push(ID(st[0],st[1],st[2]));//使用二进制将状态压缩成一个数字
    dist[st[0]][st[1]][st[2]]=0;//将起点设置成0
    while(!que.empty()){
        int t=que.front();que.pop();
        //通过右移运算符来解码
        int a=(t>>16) & 0xff,b=(t>>8) & 0xff,c=t & 0xff;
        //如果到了终点
        if(a==ed[0]&&b==ed[1]&&c==ed[2])
            return dist[a][b][c];
        for(int i=0;i<step[a];i++){
        //通过前面做的新图G来得知下一个位置
            int na=G[a][i];
            for(int j=0;j<step[b];j++){
                int nb=G[b][j];
                //排除冲突
                if(conflict(a,b,na,nb))
                    continue;
                for(int k=0;k<step[c];k++){
                    int nc=G[c][k];
                    if(conflict(a,c,na,nc)||conflict(b,c,nb,nc))
                        continue;
                    if(dist[na][nb][nc]==-1){
                        dist[na][nb][nc]=dist[a][b][c]+1;
                        que.push(ID(na,nb,nc));
                    }
                }
            }
        }
    }
    return -1;
}

int main(){
    //freopen("/home/eric/桌面/ACM/UVA/input.txt","r",stdin);
    while(~scanf("%d%d%d\n",&w,&h,&n)&&n){
        for(int i=0;i<h;i++)
            fgets(map[i],20,stdin);
        int k=0;P loc[200];
        int re[20][20];
        for(int i=0;i<h;i++){
            for(int j=0;j<w;j++){
                if(map[i][j]!='#'){//将障碍物排除在新图之外
                    loc[k].first=i;
                    loc[k].second=j;//标记原图的x，y
                    re[i][j]=k;//给原图中的每个位置用数字标记
                    //判断出起点和终点
                    if(islower(map[i][j]))
                        st[map[i][j]-'a']=k;
                    else if(isupper(map[i][j]))
                        ed[map[i][j]-'A']=k;
                    //用来标记的数字增加，避免重复
                    k++;
                }
            }
        }
        for(int i=0;i<k;i++){
            //这里我由于忘记把vector每次清空错了很多次
            G[i].clear();
            //step数组表示一个位置有几个方向是可以走的
            step[i]=0;
            for(int j=0;j<5;j++){
                int nx=loc[i].first+dx[j];
                int ny=loc[i].second+dy[j];
                if(map[nx][ny]!='#'){
                //构造新图G，用来快速得知下一个位置
                    G[i].push_back(re[nx][ny]);
                    step[i]++;
                }
            }
        }
        if(n<=2){
        //这是只有两对字母的情况，要将第三对字母的起点和终点设成相同的，以避开判断
            G[k].clear();
            step[k]=1;//只可原地不动
            G[k].push_back(k);
            st[2]=ed[2]=k++;
        }
        if(n<=1){
        //这是只有一对字母的情况，要再将第二对字母的起点和终点设成相同的，以避开判断
            G[k].clear();
            step[k]=1;//只可原地不动
            G[k].push_back(k);
            st[1]=ed[1]=k++;
        }
        //将标记步数的dist数组初始化成-1，因为会有0步的情况（题中提及）
        memset(dist,-1, sizeof(dist));
        printf("%d\n",bfs());
    }
    return 0;
}
{% endcodeblock %}
##### 双向BFS
{% codeblock lang:cpp %}
#include <iostream>
#include <cstring>
#include <vector>
#include <cstdio>
#include <queue>
using namespace std;
typedef pair<int,int> P;
const int dx[]={0,-1,1,0,0};
const int dy[]={0,0,0,-1,1};
int w,h,n;
char map[20][20];
int step[200];
int st[3],ed[3];
vector<int> G[200];
int dist[200][200][200];//该状态的步数
int color[200][200][200];

inline int ID(int a,int b,int c){
    return (a << 16)|(b << 8)|c; //使用每个状态占8个二进制位的技术编码
}

inline bool conflict(int a,int b,int na,int nb){
    return (na==nb||(a==nb&&b==na)); //当两个移动的目标相同或两两换位时冲突
}

int bfs(){
    //使用两个队列
    queue<int> q1;
    queue<int> q2;
    q1.push(ID(st[0],st[1],st[2]));
    q2.push(ID(ed[0],ed[1],ed[2]));
    dist[st[0]][st[1]][st[2]]=0;
    dist[ed[0]][ed[1]][ed[2]]=1;
    //设置起点颜色与终点颜色不同，分别进行涂色
    color[st[0]][st[1]][st[2]]=0;
    color[ed[0]][ed[1]][ed[2]]=1;
    //两个队列交替进行
    while(!q1.empty()||!q2.empty()){
    //注意，这里取出size的原因是由于BFS是交替逐层进行，所以得取出该层的节点数
        int dir1=q1.size(),dir2=q2.size();
        //一直搜索直到搜完整一层
        while(dir1--){
            int t = q1.front();
            q1.pop();
            //通过右移运算符来解码
            int a = (t >> 16) & 0xff, b = (t >> 8) & 0xff, c = t & 0xff;
            for (int i = 0; i < step[a]; i++) {
                int na = G[a][i];
                for (int j = 0; j < step[b]; j++) {
                    int nb = G[b][j];
                    if (conflict(a, b, na, nb))
                        continue;
                    for (int k = 0; k < step[c]; k++) {
                        int nc = G[c][k];
                        if (conflict(a, c, na, nc) || conflict(b, c, nb, nc))
                            continue;
                        if (color[na][nb][nc] == -1) {
                            color[na][nb][nc]=0;
                            dist[na][nb][nc] = dist[a][b][c] + 1;
                            q1.push(ID(na, nb, nc));
                        }
                        //这里的出口就是两个方向的BFS在同一个位置遇到
                        else if(color[na][nb][nc] == 1)
                        //直接输出双方的步数总和
                            return dist[a][b][c]+dist[na][nb][nc];
                    }
                }
            }
        }
        //一直搜索直到搜完整一层
        while(dir2--){
            int t = q2.front();
            q2.pop();
            int a = (t >> 16) & 0xff, b = (t >> 8) & 0xff, c = t & 0xff;
            for (int i = 0; i < step[a]; i++) {
                int na = G[a][i];
                for (int j = 0; j < step[b]; j++) {
                    int nb = G[b][j];
                    if (conflict(a, b, na, nb))
                        continue;
                    for (int k = 0; k < step[c]; k++) {
                        int nc = G[c][k];
                        if (conflict(a, c, na, nc) || conflict(b, c, nb, nc))
                            continue;
                        if (color[na][nb][nc] == -1) {
                            color[na][nb][nc]=1;
                            dist[na][nb][nc] = dist[a][b][c] + 1;
                            q2.push(ID(na, nb, nc));
                        }
                        //这里的出口就是两个方向的BFS在同一个位置遇到
                        else if(color[na][nb][nc] == 0)
                        //直接输出双方的步数总和
                            return dist[a][b][c]+dist[na][nb][nc];
                    }
                }
            }
        }
    }
    return -1;
}

int main(){
    //freopen("/home/eric/桌面/ACM/UVA/input.txt","r",stdin);
    while(~scanf("%d%d%d\n",&w,&h,&n)&&n){
        for(int i=0;i<h;i++)
            fgets(map[i],20,stdin);
        int k=0;P loc[200];
        int re[20][20];
        for(int i=0;i<h;i++){
            for(int j=0;j<w;j++){
                if(map[i][j]!='#'){//将障碍物排除在新图之外
                    loc[k].first=i;
                    loc[k].second=j;//标记原图的x，y
                    re[i][j]=k;//给原图中的每个位置用数字标记
                    //判断出起点和终点
                    if(islower(map[i][j]))
                        st[map[i][j]-'a']=k;
                    else if(isupper(map[i][j]))
                        ed[map[i][j]-'A']=k;
                        //用来标记的数字增加，避免重复
                    k++;
                }
            }
        }
        for(int i=0;i<k;i++){
            //这里我由于忘记把vector每次清空错了很多次
            G[i].clear();
            //step数组表示一个位置有几个方向是可以走的
            step[i]=0;
            for(int j=0;j<5;j++){
                int nx=loc[i].first+dx[j];
                int ny=loc[i].second+dy[j];
                if(map[nx][ny]!='#'){
                 //构造新图G，用来快速得知下一个位置
                    G[i].push_back(re[nx][ny]);
                    step[i]++;
                }
            }
        }
        if(n<=2){
        //这是只有两对字母的情况，要将第三对字母的起点和终点设成相同的，以避开判断
            G[k].clear();
            step[k]=1;//只可原地不动
            G[k].push_back(k);
            st[2]=ed[2]=k++;
        }
        if(n<=1){
        //这是只有一对字母的情况，要再将第二对字母的起点和终点设成相同的，以避开判断
            G[k].clear();
            step[k]=1;//只可原地不动
            G[k].push_back(k);
            st[1]=ed[1]=k++;
        }
        //将标记步数的dist数组初始化成-1，因为会有0步的情况（题中提及）
        memset(dist,-1, sizeof(dist));
        //增加了color数组，当双向BFS时填0和1两个数字，可以快速检测是否重合了
        memset(color,-1,sizeof(color));
        printf("%d\n",bfs());
    }
    return 0;

{% endcodeblock %}
好的，双向BFS也弄完了，开始迭代加深搜索！