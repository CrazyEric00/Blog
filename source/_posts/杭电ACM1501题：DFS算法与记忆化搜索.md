---
title: 杭电ACM1501题 ：DFS算法与记忆化搜索
categories:
  - ACM
  - HDU
date: 2018-04-26 11:18:09
tags:
  - DFS
  - 搜索
  - 记忆化搜索
---

今天准备写一篇关于深搜的题，这是一道经典的深搜题http://acm.hdu.edu.cn/showproblem.php?pid=1501   

题目的大致意思就是有两个单词 tree（A） 和 cat（B） ，保持这两个单词的字母顺序，然后将这两个单词揉合成一个新的单词C，当这个新单词的符合前两个单词的字母顺序时就输出yes，如果新单词打乱了原来这两个单词的字母顺序，输出no。 刚开始做题比较难看出这题是深度搜索，所以一开始的思考方式是三个指针维护三个字符串，用C字符串往后迭代，遇到与A或B相同的字符就将A或B的指针往前移动。那么这个方法很快就会遇到问题。 

上面的例子很幸运地避开了纠结的情况，但是我们不可避免的会遇到i=j=k的情况，这样程序就出现了“分支”那么既然可能会产生如此多的子问题，那么自然而然又变成了深度搜索问题，通过一次一次的函数递归往下深入地搜索，解决多种情况，那么我一开始的计划就是定义一个bool类型返回值的dfs函数。

{% codeblock lang:cpp %}
bool dfs(int x,int y,int z){
    //x作为a字符串的标记，y作为字符串b的标记，z作为s字符串的标记
    if(z==s.length()){
        //给出一个正确答案的出口，只要有一条路通往z==s.length()
        //那么这条路一定是所有分支中最长的路，也是最后return的路
        //所以只要有一个正确答案，就会把之前return的false覆盖掉
        if(x==a.length()&&y==b.length())
            return true;
        else
            return false;
    }
    //当a和b都找不到这个s的字符时，这个答案必定是no，直接return
    if(s[z]!=a[x]&&s[z]!=b[y])
        return false;
    //当a和b两者都可以选择时，路径的向上返回的出口设置为二者的或运算
    //这一步很容易与下面的代码产生多余的搜索，这也是第一次的超时的原因
    if(s[z]==a[x]&&s[z]==b[y])
        return dfs(x+1,y,z+1)||dfs(x,y+1,z+1);
    //这两步就是正常的a，b标记向后移动
    if(s[z]==a[x])
        return dfs(x+1,y,z+1);
    if(s[z]==b[y])
        return dfs(x,y+1,z+1);
}
{% endcodeblock %}

下面是完整代码
{% codeblock lang:cpp %}
#include <iostream>
#include <string>
using namespace std;
 
string a,b,s;
 
bool dfs(int x,int y,int z){
    if(z==s.length()){
        if(x==a.length()&&y==b.length())
            return true;
        else
            return false;
    }
    if(s[z]!=a[x]&&s[z]!=b[y])
        return false;
    if(s[z]==a[x]&&s[z]==b[y])
        return dfs(x+1,y,z+1)||dfs(x,y+1,z+1);
    if(s[z]==a[x])
        return dfs(x+1,y,z+1);
    if(s[z]==b[y])
        return dfs(x,y+1,z+1);
}
 
int main()
{
    int cas;
    while(cin>>cas){
        for(int cot=0;cot<cas;cot++){ cin>>a>>b>>s;
            if(dfs(0,0,0))
                cout << "yes" <<endl;
            else
                cout << "no" << endl;
        }
    }
    return 0;
}
{% endcodeblock %}

但是别以为这题就完了，提交到杭电oj，马上就回我一个超时，想来也是，过多的return一定会导致复杂度呈指数型增长。 所以我们需要对深搜进行剪枝，那么就得使用记忆化搜索。 所以定义一个flag的标记数组，然后把返回值设置为void，返回值直接存入flag进行记忆，这样当深度搜索进入到相同的路径时，就可以使用flag数组及时阻止。 
以下就是AC的代码 

{% codeblock lang:cpp %}
#include <iostream>
#include <string>
#include <cstring>
using namespace std;
 
string a,b,s;
bool ans;
int flag[300][300];
 
void dfs(int x,int y,int z){
    if(z==s.length()&&x==a.length()&&y==b.length()){
            ans=true;
            return;
    }
    if(s[z]!=a[x]&&s[z]!=b[y])
        return;
    if(flag[x][y])
        return;
    flag[x][y]=true;
    if(s[z]==a[x])
        dfs(x+1,y,z+1);
    if(s[z]==b[y])
        dfs(x,y+1,z+1);
}
 
int main()
{
    int cas;
    while(cin>>cas){
        for(int cot=1;cot<=cas;cot++){ 
        	ans=false; 
            memset(flag,0,sizeof(flag)); 
            cin>>a>>b>>s;
            dfs(0,0,0);
            if(ans)
                cout <<"Data set "<<cot<<": "<< "yes" <<endl;
            else
                cout <<"Data set "<<cot<<": "<< "no" << endl;
        }
    }
    return 0;
}
{% endcodeblock %}

下次继续研究广搜与深搜，计划是杭电oj的1072题