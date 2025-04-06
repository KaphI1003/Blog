# 最大流
在一个网络流上，问汇到汇点的最大的流量是多少。

## dinic 算法：
#### 步骤：

设一条边的残量 = 容量 - 当前流量。

对于每一条边，建立一条反向边，初始残量为0。

从源点bfs当前网络流，只允许走残量大于0 的边，记录每个点的深度。

然后从源点dfs，每次只走到深度正好比当前点大1的点，找到一条到汇点的流量大于0的路径，加入答案，将路上所有边的残量减去该流量，所有边的反向边加上该流量。

重复直到不能增加流量。

记得加当前弧优化(&号)


```cpp

#include<bits/stdc++.h>
using namespace std;
const int N=205,M=10005,inf=2147483647;
int n,m,s,t,q[N],ql,h[N],cu[N],dp[N];
struct E{
	int l,to,w;
}e[M];
long long ans;
bool bfs(){
	q[ql=1]=s;
	for(int i=1;i<=n;++i)dp[i]=0;
	dp[s]=1;
	for(int i=1;i<=ql;++i){
		int x=q[i];
		for(int o=h[x];o;o=e[o].l)if(e[o].w){
			int to=e[o].to;
			if(!dp[to])dp[q[++ql]=to]=dp[x]+1;
		}
	}
	return dp[t];
}
int dfs(int x,int fw){
	if(x==t)return fw;
	for(int &i=cu[x];i;i=e[i].l)if(e[i].w){//&号不能少!
		int to=e[i].to;
		if(dp[to]!=dp[x]+1)continue;
		int w=dfs(to,min(fw,e[i].w));
		if(w){
			e[i].w-=w;e[i^1].w+=w;
			fw-=w;
			return w;
		}
	}
	return 0;
}
int main(){
	int x,y;
	scanf("%d %d %d %d",&n,&m,&s,&t);
	for(int i=1;i<=m;++i){
		scanf("%d %d %d",&x,&y,&e[i<<1|1].w);
		e[i<<1].to=x;e[i<<1].l=h[y];h[y]=i<<1;
		e[i<<1|1].to=y;e[i<<1|1].l=h[x];h[x]=i<<1|1;
	}
	while(bfs()){
		for(int i=1;i<=n;++i)cu[i]=h[i];
		while(int rs=dfs(s,inf))ans+=rs;
	}
	printf("%lld\n",ans);
}

```
