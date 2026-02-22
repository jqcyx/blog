---
title: "题解: P10206"
date: 2026-02-11T17:52:04+08:00
draft: false
tags: ["题解"]
categories: ["题解"]
---

# P10206 建设工程 2

### 思路

直接枚举显然不行。考虑简化问题。\
注意到从 $s$ 到 $t$ 的每一条路径都可以描述为 $s \to u \to t$，其中 $u$ 是一个中间途经点。这启发我们跑**单源最短路**。  
设 $s \to u$ 的最短路为 $dis_{s,u}$，$u \to t$ 的最短路为 $dis_{u,t}$，问题便简化成了 **找到所有二元组 $(u,v)$，使得 $dis_{s,u} + l +dis_{v,t} \le k$。**\
此时我们对 $dis_{u,t}$ 排序，二分可能的情况即可。时间复杂度 $O(m \log {n}+q \log {n})$。注意特判 $dis_{s,t} \le k$ 的情况，此时答案即为 $\dfrac{n(n+1)}{2}$。\
完结撒花……**吗？**\
主播主播，你的二分确实很强，但要是 $(u,v)$ 和 $(v,u)$ 都可以不就重复了吗？

### 证明

列不等式看一下：

$$
\begin{cases}
dis_{s,u}+l+dis_{v,t} \le k   \\  
dis_{s,v}+l+dis_{u,t} \le k
\end{cases}
$$

即

$$
\begin{cases}
2 \times dis_{s,t}+2 \times l \le 2 \times k  \\  
dis_{s,t}+l \le k
\end{cases}
$$

而我们又特判了 $dis_{s,t} \le k$ 的情况，所以有

$$
dis_{s,t} > k
$$

由于 $l$ 是正整数，显然不可能出现以上情况。

### Code

```cpp
#include<bits/stdc++.h>
using namespace std;
int n,m,s,t,l;
long long k;//不开 long long……
vector<pair<int,int>> road[200005];
long long dis1[200005],dis2[200005];
bool qd[200005];
void dij1(int from){ 
	memset(qd,0,sizeof(qd));
	memset(dis1,0x3f,sizeof(dis1));
	dis1[from]=0;
	qd[from]=1;
	priority_queue<pair<unsigned long long,int>,vector<pair<unsigned long long,int>>,greater<pair<unsigned long long,int>>> pq;
	for(pair<int,int> i:road[from]) pq.push(make_pair(i.second,i.first)),dis1[i.first]=i.second;
	for(int i=1;i<n;i++){
		pair<long long,int>t;
		do{
			if(pq.empty())return;
			t=pq.top();
			pq.pop();
		}while(qd[t.second]);
		qd[t.second]=1;
		for(pair<int,int>add:road[t.second]){
			if(!qd[add.first]){
				if(dis1[add.first]>dis1[t.second]+add.second){
					dis1[add.first]=dis1[t.second]+add.second;
					pq.push(make_pair(dis1[add.first],add.first));
				}
			}
		} 
	}
}
void dij2(int from){//这里写两个 Dijsktra 是因为数组传参比较麻烦
	memset(qd,0,sizeof(qd));
	memset(dis2,0x3f,sizeof(dis2));
	dis2[from]=0;
	qd[from]=1;
	priority_queue<pair<unsigned long long,int>,vector<pair<unsigned long long,int>>,greater<pair<unsigned long long,int>>> pq;
	for(pair<int,int> i:road[from]) pq.push(make_pair(i.second,i.first)),dis2[i.first]=i.second;
	for(int i=1;i<n;i++){
		pair<long long,int>t;
		do{
			if(pq.empty())return;
			t=pq.top();
			pq.pop();
		}while(qd[t.second]);
		qd[t.second]=1;
		for(pair<int,int>add:road[t.second]){
			if(!qd[add.first]){
				if(dis2[add.first]>dis2[t.second]+add.second){
					dis2[add.first]=dis2[t.second]+add.second;
					pq.push(make_pair(dis2[add.first],add.first));
				}
			}
		} 
	}
}
int main(){
	scanf("%d%d",&n,&m);
	scanf("%d%d%d%lld",&s,&t,&l,&k);
	for(int i=0;i<m;i++){
		int a,b,w;
		scanf("%d%d%d",&a,&b,&w);
		road[a].push_back(make_pair(b,w));
		road[b].push_back(make_pair(a,w));	//读入时终点在前权值在后，跑最短路时权值在前终点在后
	}
	dij1(s);
	if(dis1[t]<=k){
		printf("%lld",(long long)n*(n-1)/2);//特判
	}else{
		dij2(t);
		sort(dis2+1,dis2+n+1);
		unsigned long long ans=0;
		for(int i=1;i<=n;i++){
			ans+=(((upper_bound(dis2+1,dis2+n+1,(long long)k-l-dis1[i])-dis2))-1);//二分查答案
		}
		printf("%llu",ans);
	}
	return 0;
}
```

[AC 记录](https://www.luogu.com.cn/record/233495282)  ~~考场上胡的还能进最优解前 5 页~~\
真・完结撒花\~