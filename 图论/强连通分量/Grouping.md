# Grouping

#### 题目描述

[题目链接](https://cn.vjudge.net/contest/293750#problem/F)

给定n个人，m条信息，每条信息表示为a的年龄不比b的年龄小。现在要把这些人分组，满足组内任意两个人的年龄是不能比较的。

#### 题目分析：

对于每个强连通分量，分量中的年龄都是一样的，所以我们就可以把这个分量的大小看成点值，而为了满足组内每一个点都不能到达对方，我们可以所点后进行记搜，从出度为0的点开始，取DP值最大的子节点的DP值，再加上自身的大小，就是自己的DP值。

代码如下：

```cpp
#include<bits/stdc++.h>
using namespace std;
struct P{
	int l,to;
}e[300005],E[300005];
int n,m,d[100005],l[100005],h[100005],t,tp,nu,sz[100005],b[100005],sk[100005],y,a,T,dp[100005],ans,H[100005],L;
bool is[100005],v[100005];
void se(int x){
	d[x]=l[x]=++t;sk[++tp]=x;is[x]=1;
	for(int i=h[x];i;i=e[i].l){
		if(!d[e[i].to]){
			se(e[i].to);
			l[x]=min(l[x],l[e[i].to]);
		}
		else if(is[e[i].to])l[x]=min(l[x],l[e[i].to]);
	}
	if(l[x]==d[x]){
		nu++;
		do{
			sz[nu]++;
			y=sk[tp--];
			b[y]=nu;
			is[y]=0;
		}while(x!=y);
	}
}
void dfs(int x){
	dp[x]=sz[x];v[x]=1;
	for(int i=H[x];i;i=E[i].l){
		if(!v[E[i].to])dfs(E[i].to);
		dp[x]=max(dp[x],dp[E[i].to]+sz[x]);
	}
}
int main(){
	while(scanf("%d %d",&n,&m)!=EOF){
	memset(d,0,sizeof(d));
	memset(h,0,sizeof(h));
	memset(sz,0,sizeof(sz));
	memset(H,0,sizeof(H));
	memset(v,0,sizeof(v));
	ans=t=nu=L=0;
	for(int i=1;i<=m;++i){
		scanf("%d %d",&a,&e[i].to);
		e[i].l=h[a];h[a]=i;
	}
	for(int i=1;i<=n;++i){
		if(!d[i])se(i);
	}
	for(int i=1;i<=n;++i){
		a=b[i];
		for(int o=h[i];o;o=e[o].l){
			y=b[e[o].to];
			if(a!=y){
				E[++L].to=y;
				E[L].l=H[a];
				H[a]=L;
			}
		}
	}
	for(int i=1;i<=n;++i){
		if(!v[i]){
			dfs(i);
			ans=max(ans,dp[i]);
		}
	}
	printf("%d\n",ans);
}}
```

