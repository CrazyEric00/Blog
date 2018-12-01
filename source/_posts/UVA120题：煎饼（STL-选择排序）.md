---
title: UVA120题：煎饼（STL+选择排序）
date: 2018-08-10 22:15:42
categories:
  - ACM
  - UVA
tags:
  - 数据结构
  - 排序
  - 构造
---
# 视频
这次的视频在这里：
https://www.bilibili.com/video/av29015925/
# 代码
下面是完整的代码
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main(){
    vector<int> v,p,ans;
    int n;string s;
    while(getline(cin,s)){
        n=0;v.clear();p.clear();ans.clear();
        int sum=0;
        for(int i=0;i<s.length();i++){
            if(s[i]==' '){
                if(sum){
                    p.push_back(sum);
                    v.push_back(sum);
                    sum=0;
                    n++;
                }
                continue;
            } else
                sum=sum*10+s[i]-'0';
        }
        if(sum){
            p.push_back(sum);
            v.push_back(sum);
            sum=0;
            n++;
        }
        sort(p.begin(),p.end());
        for(int i=n-1;i>=0;i--){
            if(p[i]==v[i])
                continue;
            else{
                int j;
                for(j=0;j<n&&v[j]!=p[i];j++);
                if(j==0){
                    reverse(v.begin(),v.begin()+i+1);
                    ans.push_back(n-i);
                }
                else{
                    reverse(v.begin(),v.begin()+j+1);
                    ans.push_back(n-j);
                    reverse(v.begin(),v.begin()+i+1);
                    ans.push_back(n-i);
                }
            }
        }
        ans.push_back(0);
        cout << s << endl;
        for(int i=0;i<ans.size();i++){
            if(i)
                cout << ' ';
            cout << ans[i];
        }
        cout << endl;
    }
    return 0;
}
```
