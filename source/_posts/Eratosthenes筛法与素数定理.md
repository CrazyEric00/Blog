---
title: Eratosthenes筛法与素数定理
date: 2018-09-09 09:45:58
categories:
  - ACM
tags:
  - 数论
---
今天的主题是一个古希腊算法——埃拉托色尼筛法。

# 埃氏筛法
埃拉托色尼筛选法(the Sieve of Eratosthenes)简称埃氏筛法，是古希腊数学家埃拉托色尼(Eratosthenes 274B.C.～194B.C.)提出的一种筛选法。 是针对自然数列中的自然数而实施的，用于求一定范围内的质数，它的容斥原理之完备性条件是p=H~。

其实就是求素数的一种筛法，利用质数的倍数去排除合数，筛选出剩下的质数

# 埃氏筛法步骤
*  （1）先把1删除（现今数学界1既不是质数也不是合数）
*  （2）读取队列中当前最小的数2，然后把2的倍数删去
*  （3）读取队列中当前最小的数3，然后把3的倍数删去
*  （4）读取队列中当前最小的数5，然后把5的倍数删去
*  （5）读取队列中当前最小的数7，然后把7的倍数删去
*  （6）如上所述直到需求的范围内所有的数均删除或读取

# 素数定理
素数定理可以给出第n个素数p(n)的渐近估计： 它也给出从整数中抽到素数的概率。从不大于n的自然数随机选一个，它是素数的概率大约是1/ln n。
# 筛选无平方因子数
无平方因子数问题：给出正整数n和m，区间[n,m]内的“无平方因子”的数有多少个（就是假定有一个数p，p可以等于某一个数字k的平方的倍数，即2*k^2,3*k^2，那么这个数字p就有平方因子）。1<=n<=m<=10^12,m-n<=10^7。

首先我们可以看到这道题的数据量范围大的令人发指，我们肯定不能去一一枚举，那样肯定会超时，这时候我们就需要使用埃氏筛法来解决问题了，可是埃氏筛法不是去筛选素数的吗？那我们可以先筛选出小于根号m的所有素数，然后再去找n到m的所有数字中有没有包含这些素数的平方的数字，这样我们的枚举范围就缩小到n到m了，这样就不用担心超时的问题了
# 代码
```cpp
# include <iostream>
# include <cstring>
# include <cmath>
using namespace std;
const int maxn=10000000;

bool valid[maxn];
int ans[maxn];

void getprime(int n,int &tot){
    memset(valid,true,sizeof(valid));
    for(int i=2;i<=n;i++){
        if(valid[i]){
            tot++;
            ans[tot]=i;
        }
        for(int j=1;(j<=tot)&&(i*ans[j]<=n);j++){
            valid[i*ans[j]]= false;
            if(i%ans[j]==0)
                break;
        }
    }
}

bool judge(int n,int tot){
    for(int i=1;i<=tot;i++)
        if(n%(ans[i]*ans[i])==0)
            return false;
    return true;
}

int main(){
    int n,m;
    memset(ans,0, sizeof(ans));
    cin>>n>>m;
    int tot=0,res=0;
    getprime((int)sqrt(m),tot);
    for(int i=n;i<=m;i++)
        if(judge(i,tot))
            res++;
    cout << res << endl;
    return 0;
}

```