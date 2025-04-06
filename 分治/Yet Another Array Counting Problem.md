# [Yet Another Array Counting Problem](https://codeforces.com/contest/1748/problem/E)

### 简要题意：

有一个长为 n 数列 a ，$W_a(l,r)$为区间 \[l,r] 间最小的 i 满足 $a_i$ 为区间 \[l,r] 上最大的数。
求有多少个数列 b 满足以下两个条件：
1.$1\leq b_i \leq m,i \in [1,n]$
2.$W_a(l,r)=W_b(l,r)$
$1\le n,m \le 2 \times 10^5, n*m \le 10^6$
模 $10^9+7$

### 问题分析：

首先最大值的位置可以确定，左边的数都要严格小于最大值，右边的数要小于等于最大值，然后就可以分治两个区间。最后统计得到答案。

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=2e5+5,M=1e9+7;
int n,m,a[N],ls[N],rs[N],q[N],ql,st[N],tp;
vector<int>V[N];
void ck(int &x){(x>=M)&&(x-=M);}
void clc(int l,int r,int x){
	if(ls[x])clc(l,x-1,ls[x]);
	if(rs[x])clc(x+1,r,rs[x]);
	for(int i=1;i<=m;++i){
		int w=1;
		if(ls[x])w=1ll*w*V[ls[x]][i-1]%M;
		if(rs[x])w=1ll*w*V[rs[x]][i]%M;
		ck(w+=V[x][i-1]);
		V[x].push_back(w);
	}
}
void slv(){
	scanf("%d %d",&n,&m);
	ql=0;
	for(int i=1;i<=n;++i){
		ls[i]=rs[i]=0;
		scanf("%d",&a[i]);
		while(ql&&a[q[ql]]<a[i])st[++tp]=q[ql--];
		for(int o=1;o<tp;++o)rs[st[o+1]]=st[o];
		if(tp)ls[i]=st[tp];
		tp=0;
		q[++ql]=i;
		V[i].clear();
		V[i].push_back(0);
	}
	for(int i=1;i<ql;++i)rs[q[i]]=q[i+1];
	clc(1,n,q[1]);
	printf("%d\n",V[q[1]][m]);
}
int main(){
	int T;
	scanf("%d",&T);
	while(T--)slv();
	return 0;
}
```

