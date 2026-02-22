---
title: "题解：B4458"
date: 2026-02-11T17:52:04+08:00
draft: false
tags: ["题解"]
categories: ["题解"]
---

注意到 $n \le 2000$，这启示我们使用暴力。  
开两个 set 来记录当前已有的奇恒星与偶恒星，然后计数。记得对答案 chkmax。  
code:
```cpp
#include<bits/stdc++.h>
using namespace std;
int s[2005];
int n;
int ans=0;
int main(){
    scanf("%d",&n);
    for(int i=1;i<=n;i++){
        scanf("%d",&s[i]);
    }
    for(int i=1;i<=n;i++){
        set<int>stareven,starodd;
        for(int j=i;j<=n;j++){
            if(s[j]%2)stareven.insert(s[j]);
            else starodd.insert(s[j]);
            if(stareven.size()==starodd.size())ans=max(ans,(int)stareven.size()+(int)starodd.size());
        }
    }
    printf("%d",ans);
}
```