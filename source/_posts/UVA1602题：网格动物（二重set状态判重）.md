---
title: UVA1602题：网格动物（二重set状态判重）
date: 2018-07-27 11:14:57
categories:
  - ACM
  - UVA
tags:
  - 数据结构
  - 集合
---
# 题目
经过了一系列蛋疼的气人的事件之后，终于可以稳定地继续日更博客了。

今天更新的题目非常难，我也是看了别人的博客好久才AC的，确实很抽象。首先还是给出传送门：https://vjudge.net/problem/UVA-1602

这道题的意思是给你三个数字，n，w，h，让你判断在w x n的网格中能放下多少样式的n连块。比如下面的例子：
![](/img/网格动物1.png)
有一个特定的规则，如果在翻转或者从旋转之后同一个样式的n连块算作同一种。

当然，看到这个题目我们第一眼想到的就是如何判重，我看了很多大佬的你博客，发现其实代码量最少的逻辑最清晰的反而是借用STL的set进行判重，既然我们又要生成连块又要判重，那么我们为了既不让n连块的每一块重合，有要让n连块之间不重合，所以我们就需要双重的set类解决这个问题。

而且，我们对每一个重新生成的n连块，需要进行四次旋转判重，再进行一次翻转判重，再进行四次旋转判重才可以完全取消他的嫌疑，将其添加至新的n连块类型之中。

一些具体的细节可以去大佬博客中学习，我这里只是补充了一些说明：https://www.cnblogs.com/Rubbishes/p/7206869.html?utm_source=itdadao&utm_medium=referral

# 代码
{% codeblock lang:cpp %}
#include <iostream>
#include <algorithm>
#include <set>
using namespace std;

struct Cell{
    int x,y;
    Cell(int x=0,int y=0):x(x),y(y){}
    bool operator < (const Cell &A) const{ //重载比较运算符
        if(x==A.x)
            return y<A.y;
        else
            return x<A.x;
    }
};

int dx[]={0,0,-1,1};
int dy[]={-1,1,0,0};
const int maxn=11;
set<set<Cell>> poly[maxn];//每个输入相对应的状态
int ans[maxn][maxn][maxn];//每个输入相应的答案

inline set<Cell> standard(const set<Cell> & temp){
    int mx=temp.begin()->x;
    int my=temp.begin()->y;
    set<Cell> res;
    for(set<Cell>::iterator it=temp.begin();it!=temp.end();it++){
        mx=min(mx,it->x);
        my=min(my,it->y);
    }
    for(set<Cell>::iterator it=temp.begin();it!=temp.end();it++){
        res.insert(Cell(it->x-mx,it->y-my));
    }
    return res;
}

inline set<Cell> rotate(const set<Cell>& temp){
    set<Cell> res;
    for(set<Cell>::iterator it=temp.begin();it!=temp.end();it++){
        res.insert(Cell(it->y,-it->x));
    }
    return standard(res);
}

inline set<Cell> flip(const set<Cell>& temp){
    set<Cell> res;
    for(set<Cell>::iterator it=temp.begin();it!=temp.end();it++){
        res.insert(Cell(it->x,-it->y));
    }
    return standard(res);
}

void add(const set<Cell>& s,const Cell& cell){
    set<Cell> t=s;
    t.insert(cell);
    t=standard(t);
    int n=t.size();
    for(int i=0;i<4;i++){
        if(poly[n].count(t))
            return;
        t=rotate(t);
    }
    t=flip(t);
    for(int i=0;i<4;i++){
        if(poly[n].count(t))
            return;
        t=rotate(t);
    }
    poly[n].insert(t);
}

void sol(){
    set<Cell> start;
    start.insert(Cell(0,0));
    poly[1].insert(start);
    for(int i=2;i<=10;i++){
        for(set<set<Cell>>::iterator j=poly[i-1].begin();j!=poly[i-1].end();j++){
            for(set<Cell>::iterator k=(*j).begin();k!=(*j).end();k++){
                for(int l=0;l<4;l++){
                    int nx=k->x+dx[l];
                    int ny=k->y+dy[l];
                    Cell t(nx,ny);
                    if(!j->count(t))
                        add(*j,t);
                }
            }
        }
    }
    for(int i=1;i<maxn;i++){
        for(int w=1;w<maxn;w++){
            for(int h=1;h<maxn;h++){
                int cnt=0;
                for(set<set<Cell>>::iterator it=poly[i].begin();it!=poly[i].end();it++){
                    int mx=0,my=0;
                    for(set<Cell>::iterator it2=it->begin();it2!=it->end();it2++){
                        mx=max(mx,it2->x);
                        my=max(my,it2->y);
                    }
                    if(min(mx,my)<min(h,w)&&max(mx,my)<max(h,w))
                        cnt++;
                }
                ans[i][w][h]=cnt;
            }
        }
    }
}

int main(){
    ios::sync_with_stdio(false);
    cin.tie(0);
    sol();
    int h,w,n;
    while(cin>>n>>w>>h){
        if(n==1) {
            cout << 1 << endl;
            continue;
        }
        cout << ans[n][w][h] << endl;
    }
    return 0;
}
{% endcodeblock %}
好，下午继续更新，破坏正方形！