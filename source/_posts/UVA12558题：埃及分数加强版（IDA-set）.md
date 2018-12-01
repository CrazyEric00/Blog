---
title: UVA12558题：埃及分数加强版（IDA*+set）
date: 2018-08-01 16:54:26
categories:
  - ACM
  - UVA
tags:
  - IDA*
  - 搜索
  - DFS
  - 数据结构
---
今天这道题目是埃及分数的加强版，与其说是加强版，还不如说只是加了几行代码而已，所以并不会比埃及分数难多少。如果不了解埃及分数请在我的博客中查看我以前对埃及分数特别介绍的一篇文章http://crazyeric.top/2018/07/21/%E5%9F%83%E5%8F%8A%E5%88%86%E6%95%B0%E9%97%AE%E9%A2%98%EF%BC%88%E8%BF%AD%E4%BB%A3%E5%8A%A0%E6%B7%B1%E6%90%9C%E7%B4%A2%EF%BC%89/
# 题目
传送门：https://vjudge.net/problem/UVA-12558

这道题目对比埃及分数，只是多了一个条件，就是有一些题中给出的分母不能成为答案，也就是我们在枚举的时候要排除掉这些数字，那么其实就很简单了，只要我们使用一个set，每次枚举的售后过一遍这个set，排除掉题目要求的数字就可以了，是不是非常简单呢？下面给上AC代码。
# 代码
{% codeblock lang:cpp %}
#include <iostream>
#include <cstring>
#include <set>
using namespace std;
const int N=1200;
typedef long long ll;

int maxd;
ll ans[N],v[N];
int ok;
set<ll> s;

ll gcd(ll a,ll b){
    return a==0?b:gcd(b%a,a);
}

ll get_first(ll a,ll b){
    return (b/a)+1;
}

bool better(int d){
    for(int i=d;i>=0;i--){
        if(v[i]!=ans[i])
            return ans[i]==-1||v[i]<ans[i];
    }
}

bool dfs(int d,ll from,ll a,ll b){
    if(d==maxd){
        if(b%a)
            return false;
        if(s.count(b/a))
            return false;
        v[d]=b/a;
        if(better(d))
            memcpy(ans,v, sizeof(ll)*(d+1));
        return true;
    }
    bool flag=false;
    from=max(from,get_first(a,b));
    for(int i=from;;i++){
        if(s.count(i))
            continue;
        if(a*i>=b*(maxd-d+1))
            break;
        v[d]=i;
        ll na=a*i-b;
        ll nb=b*i;
        ll g=gcd(a,b);
        if(dfs(d+1,i+1,na/g,nb/g))
            flag=true;
    }
    return flag;
}

int main(){
    int k,in;
    ll a,b;
    int T;
    cin>>T;
    for(int cas=1;cas<=T;cas++){
        cin>>a>>b>>k;
        cout << "Case "<<cas << ": ";
        s.clear();
        while(k--) {
            cin>>in;
            s.insert(in);
        }
        ok=0;
        for(maxd=1;;maxd++){
            memset(ans,-1,sizeof(ans));
            if(dfs(0,get_first(a,b),a,b)){
                ok=1;
                break;
            }
        }
        cout << a << '/' << b<<'=';
        if(ok) {
            for (int i = 0; i < maxd; i++) {
                cout << "1/"<<ans[i]<<'+';
            }
            cout << "1/" << ans[maxd]<<endl;
        }
    }
    return 0;
}
{% endcodeblock %}
好的，那从下一篇起我就要进入新的一个章节了：二分与贪心等等。