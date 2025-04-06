[贴个板子题](https://www.luogu.com.cn/problem/P5905)

Johnson 算法是个多元最短路算法，只要图中没有负环就可以使用，可以在$O(nm\log m)$的时间复杂度内求任意两个点之间的最短路径。

### 算法流程:
1.建立一个超级源点 0 ，向图中每一个点连一条权值为 $0$ 的有向边。
2.用SPFA求出超级源点 0 到每一个点的最短路 $w_i$，如果有负环直接退出。
3.将原图中的每一条边（$u->v$）权值加上 $w_u-w_v$
4. 在原图的每一个点跑一遍dij。
5. 把 $dis[u][v]$加上$w_v-w_u$还原。

### 正确性证明
首先在原图中任意一条从$u$到$v$的边权为 $ew$的边，由于最短路的性质，我们有 $w_u+ew \ge w_v$，所以$ew + w_u -w_v \ge 0$，所以新图中的每一条边都是非负的，满足 dij 的条件。

而如何证明它是最短路呢？

设原图中从$u$到$v$的任意一条路径为${u,a_1,a_2,..,a_k,v}$，权值和为$tot=\sum_0^k{ew_i}$，那么在新图中这条路线的权值和就是
$$
tot'=(ew_0+w_u-w_{a_1})+(ew_1+w_{a_1}-w_{a_2})+···+(ew_k+w_{a_k}-{w_v})
$$
$$
tot'=\sum_0^k{ew_i}+(w_u-w_{a_1})+(w_{a_1}-w_{a_2})+···+(w_{a_k}-{w_v})
$$
$$
tot'=tot + w_u-w_v
$$
两点间的任意路径的差值恒定为$w_u-w_v$，所以可以得到新图的最短路径就是原图的最短路径，将新图最短路径长度减去差值就可以的到原图的最短路径长度。
### code
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=3005,inf=1e9;
struct E{
	int l,to,w;
}e[N<<2];
int n,m,h[N],iq[N],v[N],w[N];
long long dis[N][N];
queue<int>Q;
bool SPFA(){
	for(int i=1;i<=n;++i)w[i]=inf;
	Q.push(n+1);
	iq[n+1]=1;
	while(!Q.empty()){
		int x=Q.front();
		Q.pop();
		iq[x]=0;
		for(int i=h[x];i;i=e[i].l){
			int to=e[i].to;
			if(w[x]+e[i].w<w[to]){
				w[to]=w[x]+e[i].w;
				if(!iq[to]){
					v[to]++;
					if(v[to]>n)return 0;
					Q.push(to),iq[to]=1;
				}
			}
		}
	}
	return 1;
}
struct P{
	int x,w;
	bool operator < (const P&t)const{
		return w>t.w;
	}
};
priority_queue<P>pq;
void dij(int rt){
	for(int i=1;i<=n;++i)dis[rt][i]=inf;
	dis[rt][rt]=0;
	pq.push((P){rt,0});
	while(!pq.empty()){
		P t=pq.top();
		pq.pop();
		if(t.w!=dis[rt][t.x])continue;
		for(int i=h[t.x];i;i=e[i].l){
			int to=e[i].to;
			if(dis[rt][to]>t.w+e[i].w){
				pq.push((P){to,dis[rt][to]=t.w+e[i].w});
			}
		}
	}
}
int main(){
	scanf("%d %d",&n,&m);
	for(int i=1;i<=m;++i){
		int x,y,w;
		scanf("%d %d %d",&x,&y,&w);
		e[i].to=y;e[i].l=h[x];h[x]=i;
		e[i].w=w;
	}
	for(int i=1;i<=n;++i){
		e[m+i].to=i;e[m+i].l=h[n+1];h[n+1]=m+i;
	}
	if(!SPFA()){puts("-1");return 0;}
	for(int i=1;i<=n;++i)
		for(int j=h[i];j;j=e[j].l)
			e[j].w+=w[i]-w[e[j].to];
	for(int i=1;i<=n;++i){
		long long ans=0;
		dij(i);
		for(int j=1;j<=n;++j){
			if(dis[i][j]!=inf)dis[i][j]-=w[i]-w[j];
			ans+=1ll*j*dis[i][j];
		}
		printf("%lld\n",ans);
	}
	return 0;
}
```