---
title: "ABC455 代码公示&做题记录"
date: 2026-04-25T21:40:004+08:00
draft: false
---

注意本文的发布时间为 `2026-04-25T21:40:004+08:00`，并没有违反 AtCoder 的规则。

## A 455

水题。

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
	int a,b,c;
	scanf("%d%d%d",&a,&b,&c);
	if(a!=b&&b==c)puts("Yes");
	else puts("No"); 
	return 0;
}
```

## B Spiral Galaxy

$ 1 \le H,W \le 10$？

六重循环启动！！！

```cpp
#include<bits/stdc++.h>
using namespace std;
int g[15][15];
int main(){
	int h,w;
	scanf("%d%d\n",&h,&w);
	for(int i=1;i<=h;i++){
		for(int j=1;j<=w;j++){
			char c=getchar();
			if(c=='.')g[i][j]=0;
			else g[i][j]=1;
		}
		getchar();
	}
	int ans=0;
	for(int h1=1;h1<=h;h1++){
		for(int h2=h1;h2<=h;h2++){
			for(int w1=1;w1<=w;w1++){
				for(int w2=w1;w2<=w;w2++){
					for(int i=h1;i<=h2;i++){
						for(int j=w1;j<=w2;j++){
							if(g[i][j]!=g[h1+h2-i][w1+w2-j])goto ctn;
						}
					}
					ans++;
					ctn:;
				}
			}
		}
	}
	printf("%d",ans);
	return 0;
}
```

## C Vanish

贪心。

每次选择 出现次数 $\times$ 数字大小 最大的消除。

```cpp
#include<bits/stdc++.h>
using namespace std;
long long val[300005];
int a[300005];
long long sum;
bool cmp(long long a,long long b){
	return a>b;
}
int main(){
	int n,m;
	scanf("%d%d",&n,&m);
	for(int i=1;i<=n;i++){
		scanf("%d",a+i);
		sum+=a[i];
	}
	sort(a+1,a+n+1);
	int nv=0,cnt=0,idx=0;
	for(int i=1;i<=n;i++){
		if(a[i]!=nv){
			val[idx++]=1ll*nv*cnt;
			nv=a[i];
			cnt=1;
		}else{
			cnt++;
		}
	}
	val[idx]=1ll*nv*cnt;
	sort(val+1,val+idx+1,cmp);
	for(int i=1;i<=min(m,idx);i++){
		sum-=val[i];
	}
	printf("%lld",sum);
	return 0;
}
```

## D Card Pile Query

一眼链表。感觉只能评橙了？

```cpp
#include<bits/stdc++.h>
using namespace std;
int nxt[300005],lst[300005];
int query(int x){
	if(nxt[x]==-1)return 1;
	else return 1+query(nxt[x]);
}
int main(){
	int n,q;
	scanf("%d%d",&n,&q);
	for(int i=1;i<=n;i++)nxt[i]=lst[i]=-1;
	while(q--){
		int c,p;
		scanf("%d%d",&c,&p);
		nxt[lst[c]]=-1;
		nxt[p]=c;
		lst[c]=p;
	}
	for(int i=1;i<=n;i++){
		if(lst[i]!=-1)printf("%d ",0);
		else printf("%d ",query(i));
	}
	return 0;
}
```

## E Unbalanced ABC Substrings

前缀和维护各种差值，因为原条件可以改写为 $a-b \ne 0$ 且 $b-c \ne 0$ 且 $c-a \ne 0$。然后容斥一下。好像我做过类似的题来着。

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
int main(){
	int n;
	string s;
	cin>>n>>s;
	ll tot=1ll*n*(n+1)/2;
	ll mul=2*n+1;
	map<ll,ll>ab,bc,ca,pr;
	ll a=0,b=0,c=0,dab,dbc;
	ab[0]++,bc[0]++,ca[0]++,pr[0]++;
	for(char ch:s){
		if(ch=='A')a++;
		if(ch=='B')b++;
		if(ch=='C')c++;
		dab=a-b,dbc=b-c;
		ab[dab]++,bc[dbc]++,ca[-dab-dbc]++;
		long long key=(dab)*mul+dbc;
		pr[key]++;
	}
	ll eqab=0,eqbc=0,eqca=0,eqpr=0;
	for(auto p:ab){
		ll c=p.second;
		eqab+=c*(c-1)/2;
	}
	for(auto p:bc){
		ll c=p.second;
		eqbc+=c*(c-1)/2;
	}
	for(auto p:ca){
		ll c=p.second;
		eqca+=c*(c-1)/2;
	}
	for(auto p:pr){
		ll c=p.second;
		eqpr+=c*(c-1)/2;
	}
	cout<<tot-eqab-eqbc-eqca+eqpr*2;
	return 0;
}
```

## F Merge Slimes 2

经过惊人的注意力的观察，发现这东西和操作顺序半毛钱关系都没有，就是 $(\sum_{i=1}^{m} \sum_{j=1}^{m} B_{i} \times B_{j}) - \sum_{i=1}^{m} i^2$。

仔细一想，诶，不是刚学过完全平方公式吗，那这个不就是 

$$\frac{(\sum_{i=1}^{m}b)^2-\sum_{i=1}^{m} b^2}{2}$$

吗？

我直接一个维护区间和、区间平方和、区间加的线段树不就好了吗？？？

线段树！！！

```cpp
#include<bits/stdc++.h>
using namespace std;
//啊好想养一只香香软软的线段树啊 
typedef long long ll;
constexpr int MOD=998244353;
constexpr int inv2=(MOD+1)/2;
constexpr int MAXN=100005;
struct Nd{
	ll sum,sumsq,lzy;
}tree[MAXN<<2];
void build(int id,int l,int r){
	tree[id]={0,0,0};
	if(l==r)return;
	int m=(l+r)>>1;
	build(id<<1,l,m);
	build((id<<1)|1,m+1,r);
}
void mktag(int id,int l,int r,ll v){
	int len=r-l+1;
	v%=MOD;
	tree[id].sumsq=(tree[id].sumsq+2*v%MOD*tree[id].sum%MOD+len*v%MOD*v%MOD)%MOD;
	tree[id].sum=(tree[id].sum+len*v)%MOD;
	tree[id].lzy=(tree[id].lzy+v)%MOD;
}
void pushdown(int id,int l,int r){
	if(tree[id].lzy){
		int m=(l+r)>>1;
		mktag(id<<1,l,m,tree[id].lzy);
		mktag((id<<1)|1,m+1,r,tree[id].lzy);
		tree[id].lzy=0;
	}
}
void pushup(int id){
	tree[id].sum=(tree[id<<1].sum+tree[(id<<1)|1].sum)%MOD;
	tree[id].sumsq=(tree[id<<1].sumsq+tree[(id<<1)|1].sumsq)%MOD;
}
void update(int id,int l,int r,int ql,int qr,ll v){
	if(ql<=l&&r<=qr){
		mktag(id,l,r,v);
		return;
	}
	pushdown(id,l,r);
	int m=(l+r)>>1;
	if(ql<=m)update(id<<1,l,m,ql,qr,v);
	if(qr>m)update((id<<1)|1,m+1,r,ql,qr,v);
	pushup(id);
}
pair<ll,ll> query(int id,int l,int r,int ql,int qr){
	if(ql<=l&&r<=qr)return {tree[id].sum,tree[id].sumsq};
	pushdown(id,l,r);
	int m=(l+r)>>1;
	if(qr<=m)return query(id<<1,l,m,ql,qr);
	if(ql>m)return query((id<<1)|1,m+1,r,ql,qr);
	auto lft=query(id<<1,l,m,ql,qr);
	auto rgt=query((id<<1)|1,m+1,r,ql,qr);
	return {(lft.first+rgt.first)%MOD,(lft.second+rgt.second)%MOD};
}
int main(){
	int n,q;
	scanf("%d%d",&n,&q);
	build(1,1,n);
	while(q--){
		int l,r;
		ll a;
		scanf("%d%d%lld",&l,&r,&a);
		update(1,1,n,l,r,a);
		auto pr=query(1,1,n,l,r);
		ll s=pr.first,sq=pr.second;
		printf("%lld\n",((s*s%MOD-sq+MOD)%MOD)*inv2%MOD);
	}
	return 0;
}
```

## G Balanced Subarrays

我不会。长大后再来学习吧。