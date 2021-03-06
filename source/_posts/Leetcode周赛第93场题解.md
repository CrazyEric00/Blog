---
title: Leetcode周赛第93场题解
categories:
  - ACM
  - LeetCode
date: 2018-07-15 13:53:00
tags:
  - 竞赛
---

今天上午打了场leetcode周竞赛。终于进了两百名，灰常开心。 
![](http://39.107.233.145/wp-content/uploads/2018/07/AK-1024x479.png)
![](http://39.107.233.145/wp-content/uploads/2018/07/排名-1024x323.png)
以下是解题报告。 
附上传送门：https://leetcode-cn.com/contest/weekly-contest-93 
#### <font color=#ff0000> 1.二进制间距  </font>
首先第一题叫做二进制间距，就是判断一个数字变成二进制后两个1之间的距离，这题纯考细节。先找到一个1，然后用变量t记忆这个1的位置，然后找到下一个1的位置然后相减即可。
{% codeblock %}
class Solution {
public:
    int binaryGap(int N) {
        int move=0,t=0,ans=0,times=0;
        while(N>0){
            if(N&1){
                times++;
                if(times>1)
                    ans=max(ans,move-t);
                t=move;
            }
            N=N>>1;
            move++;
        }
        if(times<=1)
            ans=0;
        return ans;
    }
};
{% endcodeblock %}
#### <font color=#ff0000> 2.重排序得到2的幂  </font>
第二题我没有想到灵巧的解题方式，所以直接使用全排列，先将数字分解，然后使用全排列（注意开头是0的情况必须剔除掉），最后使用位运 算，使得这个数字的二进制表示只有一个1，如果出现了两个及以上，立即剪枝。 
{% codeblock %}
class Solution {
public:
    bool reorderedPowerOf2(int N) {
        int s[9],i=0;
        while(N){
            s[i++]=N%10;
            N/=10;
        }
        sort(s,s+i);
        long long t;
        int flag;
        do{
            if(s[0]==0)
                continue;
            int mul=1;
            t=0;
            for(int j=i-1;j>=0;j--){
                t+=s[j]*mul;
                mul*=10;
            }
            int times=0;
            flag=1;
            while(t){
                if(t&1)
                    times++;
                if(times>1){
                    flag=0;
                    break;
                }
                t=t>>1;
            }
            if(flag)
                break;
        }while(next_permutation(s,s+i));
        if(flag)
            return true;
        else
            return false;
    }
{% endcodeblock %}
#### <font color=#ff0000> 3.优势洗牌   </font>
这个问题非常类似于田忌赛马（这个比喻我觉得非常恰当），所以显而易见的我们必须对A数组与B数组进行排序，但是问题在于B数组的原来的 位置我们需要想办法保存下来，因为我们需要输出A数组，当然得与B数组的原来位置一一映射。我一开始使用了map保存了B数组的位置，但是数据量比较大，没想到居然爆内存了。转念过来一想其实直接用pair<int,int>就够了，所以改完之后内存就不爆了。 然后就是upper\_bound二分查找，将结果写入ans对应于B数组的原来位置，如果upper\_bound没成功，就把A数组最小值放入，类似于田忌赛马中拿下等马去糊弄对方上等马一样。
{% codeblock %}
class Solution {
public:
    vector<int> advantageCount(vector<int>& A, vector<int>& B) {
        vector<pair<int,int>> mp;
         vector<int> ans;
        for(int i=0;i<B.size();i++){
            mp.push_back(make_pair(B[i],i));
            ans.push_back(-1);
        }
        sort(mp.begin(),mp.end());
        sort(A.begin(),A.end());
        for(int i=0;i<B.size();i++){
            vector<int>::iterator it=upper_bound(A.begin(),A.end(),mp[i].first);
            if(it!=A.end()){
                ans[mp[i].second]=*it;
                *it=-1;
            }else{
                int j;
                for(j=0;j<A.size()&&A[j]==-1;j++);
                ans[mp[i].second]=A[j];
                A[j]=-1;
            }
        }
        return ans;
    }
};
{% endcodeblock %}
#### <font color=#ff0000> 4.最低加油次数  </font>
这道题的关键在于思想的转变，很多人会纠结于加油站的选择问题，其实我们可以换个角度思考。 我们可以让车一直跑，在车消耗掉所有的油之后，我们可以获得已经路过的加油站加油的权利，而不是每到一个加油站都去纠结一下到底加不加油。所以，每当车跑过一个加油站，我们就把这个加油站的油加入优先队列，当车没油时就拿出优先队列最上面的一份（一定是最多的一份）给车加油。相应地，如果车又没油了队列又空了，那么就说明这车跑不到了 
{% codeblock %}
class Solution {
public:
    int minRefuelStops(int target, int startFuel, vector<vector<int>>& stations) {
        vector<int> end;
        end.push_back(target);
        end.push_back(0);
        stations.push_back(end);
        priority_queue<int> que;
        int ans=0,pos=0;
        for(int i=0;i<stations.size();i++){
            int d=stations[i][0]-pos;
            while(startFuel-d<0){
                if(que.empty()){
                    return -1;
                }
                startFuel+=que.top();
                que.pop();
                ans++;
            }
            startFuel-=d;
            pos=stations[i][0];
            que.push(stations[i][1]);
        }
        return ans;
    }
};
{% endcodeblock %}
#### OK,这次leetcode周赛的解题报告就结束了，我们下周见！ PS：欢迎喜欢算法的同学一起来刷题讨论！