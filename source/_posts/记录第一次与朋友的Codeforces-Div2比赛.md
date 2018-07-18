---
title: 记录第一次与朋友的Codeforces div2比赛
date: 2018-04-26 16:46:12
categories:
  - ACM
  - Codeforces
tags:
---
最近练习算法倍感无聊，所以与同学在鼎鼎大名的codeforces上练了练手。不得不说两小时紧张题目还是非常刺激的（特别还是在学校组织的上机时间内完成的），codeforces的题还是非常困难且考验脑力的，最后在争论下，以2次过A题以及幸运的一次过D题而结束。然而在我赛后的研究后，其实B，C二题并不是非常困难，之后我也把B，C两题给A了。今天写这篇文章就是来总结一下这四道题的各种解法以及观摩rating榜上大神的解法。

先来说说A题，其实A题说白了就是找到达数组和一半的数字。看似简单的一题往往会在测试数据上下套，所以这里用到一个上课不讲的技巧

{% codeblock lang:cpp %}
ios_base::sync_with_stdio(0);
    cin.tie(0);
{% endcodeblock %}

这两句话用于决定C++标准streams(cin,cout,cerr…)是否与相应的C标准程序库文件(stdin,stdout,stderr)同步，同步意味着带 来更多的负担，cin.tie(0)用于解除cin cout绑定。通俗来讲，就可以反复读取一大段一大段的输入，在codeforces这种几乎变态的测试 数据下尤为有效。然后就是开一个贼大的long long数组，一开始的错误就是因为没有处理好超大数据而导致了一次WA。下面附上我A题的AC 代码



{% codeblock lang:cpp A题 %}
#include <iostream>
#include <cstring>
#include <string>
 
 using namespace std;
 
 long long a[200005];
 int main(){
    ios_base::sync_with_stdio(0);
    cin.tie(0);
        int n;
        cin>>n;
         memset(a,0,sizeof(a));
         for(int i=1;i<=n;i++){
                cin>>a[i];
                a[i]+=a[i-1];
           }
         for(int i=1;i<=n;i++){
                if(a[i]>=(a[n]+1)/2){
                         cout<<i<<endl;
                         return 0;
                    }
            }
     return 0;
}
{% endcodeblock %}

是不是觉得除了一些细节，A题还是特别简单的，这可是codeforces的开胃菜而已！！ 接下来就是刺激的B题了(此题并不算难，可是如果不能做到及时化繁为简，很容易绕到圈子里)  首先这是一个排座位的题目，a个A类同学与b个B类同学上火车，火车上有一排座位，座位上放有以‘*’表示的墙（我把题的意思简化了， 本来的题意并没有这么无聊），然后限制条件是A和B不能相邻，但是AB类同学旁边都可以放‘*’。求最多能放的AB同学的总和。 这个题一上来非常令人害怕，特别是我涛哥（这里艾特下我涛哥）快炸了，其实后来细想并没有刚开始想的这么复杂，主要得抓住问题的 关键–尽可能的多放入A和B，还有一个关键点是*号的存在直接导致了思维难度的升高。这里为了理解起来更加的方便，我们先考虑没有* 的情况 看吧，是不是特别简单！只要考虑A和B足够的情况下交叉排放即可，所以在没有碰到*的时候，我 们的代码就及其地简单了。通过对前一个变量pre的维护，就可以做到当前一个为A时有B放B，前一个为B时有A放A

{% codeblock lang:cpp %}
if(pre==' '){
                if(a>b&&a>0){
                    a--;
                    pre='a';
                    sum++;
                }
                else{
                    b--;
                    pre='b';
                    sum++;
                }
{% endcodeblock %}

所以本题的代码的难度几乎都在如何处理‘*’上，那么这就要思考一个问题了，如果我们遇到*了，我们究竟该怎么在*的左右放AB才能使 AB的数量最大化的。这就要引入这道题的核心–平衡了，从无*的情况可以得知，A和B必须交替放置才可以使利益最大化，所以，我在无墙的 空地上必须尽可能地使AB的数量不要相差太多。那么，在遇到*时我们就知道该干什么了，这不赶紧把数量多的塞进*的左右两边，这样在后续 地放置中才可以更加平衡，也可以放更多的A和B，下面贴出AC代码

{% codeblock lang:cpp B题%}
#include <iostream>
#include <string>
#include <cstring>
#include <algorithm>
using namespace std;
 
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    long long n,a,b,sum=0;
    cin >> n >> a >> b;
    char *s=new char[n+10];
    for(int i=0;i<n;i++){
        cin>>s[i];
    }
    char pre=' ';
    for(int i=0;i<n;i++){
        if(s[i]=='.'){
            if(pre==' '){
                if(a>b&&a>0){
                    a--;
                    pre='a';
                    sum++;
                }
                else{
                    b--;
                    pre='b';
                    sum++;
                }
            }
            else if(pre=='a'&&b>0){
                b--;
                pre='b';
                sum++;
            }
            else if(pre=='b'&&a>0){
                a--;
                pre='a';
                sum++;
            }
            else
                pre=' ';
        }
        else{
            pre=' ';
        }
        if(a==0&&b==0)
            break;
    }
    cout << sum << endl;
    return 0;
}
{% endcodeblock %}

今天就先更新到B题，更加刺激精彩的C、D两题以后再写，各位晚安。