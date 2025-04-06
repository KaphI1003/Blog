# [Link with Railway Company](https://ac.nowcoder.com/acm/contest/57356/B)
### 简要题面
有$n$个城市，$n-1$条道路把城市连通。但是道路现在都已经荒废，修复第$i$条道路花费$c_i$。有$m$个运输计划，第$i$个计划要从$a_i$送货到$b_i$，利润为$x_i-y_i$，要完成计划要保证$a_i$到$b_i$的所有道路都要被修复。问可以获得的最大利润。
### 问题分析
我们先把$x_i-y_i \le 0$的计划去掉。在每条道路上都设一个点，点权为$-c_i$;每个计划也设一个点，点权为$x_i-y_i$，然后向所有它覆盖到的边都连一条边，那么问题就转化成求该图的**最大权闭合子图**。

但是直接连边得到的边数是 $O(nm)$的，所以我们使用树剖线段树优化建图然后再跑最大流即可。

##### code
```
#include<bits/stdc++.h>
using namespace std;
const int N=10005,inf=1e9+7;
int n,m,h[N],H[N*5],dp[N],tp[N],sn[N],sz[N],f[N],p[N],pos[N<<2],df[N],dfn,el,s,t,vcnt,ans;
struct edge{
	int l,to,w;
}e[N<<1],E[N*256];
void AddE(int x,int y,int w){
//	printf("                    %d %d %d %d\n",x,y,w,el);
	E[++el].to=y;E[el].l=H[x];H[x]=el;E[el].w=w;
	E[++el].to=x;E[el].l=H[y];H[y]=el;E[el].w=0;
}
void dfs1(int x){
	sz[x]=1;
	for(int i=h[x];i;i=e[i].l){
		int to=e[i].to;
		if(to==f[x])continue;
		dp[to]=dp[x]+1,f[to]=x;
		AddE(to,t,e[i].w);
		dfs1(to);
		sz[x]+=sz[to];
		if(sz[to]>sz[sn[x]])sn[x]=to;
	}
}
void dfs2(int x){
	p[df[x]=++dfn]=x;
	tp[x]= (sn[f[x]]==x)? tp[f[x]]:x;
	if(sn[x])dfs2(sn[x]);
	for(int i=h[x];i;i=e[i].l){
		int to=e[i].to;
		if(to==f[x]||to==sn[x])continue;
		dfs2(to);
	}
}
void Js(int z,int y,int c){
	if(z==y){
		pos[c]=p[z];
		return;
	}
	pos[c]=++vcnt;
	int mid=(z+y)>>1;
	Js(z,mid,c<<1),Js(mid+1,y,c<<1|1);
	AddE(pos[c],pos[c<<1],inf);
	AddE(pos[c],pos[c<<1|1],inf);
}
void Add(int p,int z,int y,int c,int l,int r){
	if(l<=z&&y<=r){
		AddE(p,pos[c],inf);
		return;
	}
	int mid=(z+y)>>1;
	if(l<=mid)Add(p,z,mid,c<<1,l,r);
	if(r>mid)Add(p,mid+1,y,c<<1|1,l,r);
}
int q[N*5],ql,dep[N*5],cu[N*5];
bool bfs(){
	for(int i=1;i<=vcnt;++i)dep[i]=0;
	q[ql=1]=s,dep[s]=1;
	for(int i=1;i<=ql;++i){
		int x=q[i];
		for(int j=H[x];j;j=E[j].l)if(E[j].w){
			int to=E[j].to;
			if(!dep[to]){
				dep[to]=dep[x]+1;
				q[++ql]=to;
			}
		}
	}
	return dep[t];
}
int dfs(int x,int fw){
	if(x==t)return fw;
	for(int &i=cu[x];i;i=E[i].l)if(E[i].w){//&号不能少!
		int to=E[i].to;
		if(dep[to]!=dep[x]+1)continue;
		int w=dfs(to,min(fw,E[i].w));
		if(w){
			E[i].w-=w;E[i^1].w+=w;
			fw-=w;
			return w;
		}
	}
	return 0;
}
void Dinic(){
	while(bfs()){
//		puts("??????????????????");
		for(int i=1;i<=vcnt;++i)cu[i]=H[i];
		while(int rs=dfs(s,inf))ans-=rs;
	}
}
int main(){
	scanf("%d %d",&n,&m);
	for(int i=1;i<n;++i){
		int x,y,w;
		scanf("%d %d %d",&x,&y,&w);
		e[i<<1].to=x;e[i<<1].l=h[y];h[y]=i<<1;
		e[i<<1|1].to=y;e[i<<1|1].l=h[x];h[x]=i<<1|1;
		e[i<<1].w=e[i<<1|1].w=w;
	}
	vcnt=n,el=1;
	s=++vcnt,t=++vcnt;
	dfs1(1);
	dfs2(1);
	Js(2,n,1);
	for(int i=1;i<=m;++i){
		int x,y,w,w2;
		scanf("%d %d %d %d",&x,&y,&w,&w2);
		w-=w2;
		if(w<=0)continue;
		AddE(s,++vcnt,w);
		while(tp[x]!=tp[y]){
			if(dp[tp[x]]<dp[tp[y]])swap(x,y);
			Add(vcnt,2,n,1,df[tp[x]],df[x]);
			x=f[tp[x]];
		}
		if(dp[x]>dp[y])swap(x,y);
		if(x!=y)Add(vcnt,2,n,1,df[x]+1,df[y]);
		ans+=w;
	}
	Dinic();
	printf("%d\n",ans);
}
```