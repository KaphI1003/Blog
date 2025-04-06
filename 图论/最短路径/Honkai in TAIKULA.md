# [Honkai in TAIKULA](https://codeforces.com/gym/104396/problem/B)

### 简要题意
$n$个点$m$条边的有向有负边图，每条边有一个权值$w_i$，问每个点可不可以经过一条路径回到自己，并且路径的权值和是奇数。如果没有，输出“Battle with the crazy Honkai”，如果有，输出符合条件权值和最小的路径的权值和，如果最小权值和为负无穷，输出“Haha, stupid Honkai”。

$1 \le n \le 1000 , 1 \le m \le 10000 , |w_i|\le 10^7$
### 问题分析

我们把每个点拆成两个点($u,u'$)，代表经过的路径的奇偶性，如果边权为奇数，交叉连边，如果边权为偶数，平行连边。这样问题就变成了找从$u$到$u'$的最短路径。

负环只能在其存在的强连通分块内作用，所以我们每次单独找一个强连通分块处理。
用SPFA找图中是否存在负环，如果有就bfs找每对$u$和$u'$是否存在路径。如果没有，就用Johnson求多源最短路。

注意清空
##### 时间复杂度 $O(nm\log m)$

##### code 
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=2010;
const long long inf=1e18;
int n,m,el,H[N],h[N],iq[N],v[N],dfn,df[N],lw[N],is[N],st[N],tp,vs,iv[N];
long long w[N],dis[N][N],ans[N];
queue<int>Q;
vector<int>V;
struct P{
	int x;
	long long w;
	bool operator < (const P&t)const{
		return w>t.w;
	}
};
struct Heap{
	P tr[30005];
	int num;
	bool empty(){return !num;}
	void clear(){num=0;}
	P top(){return tr[1];}
	void push(P t){
		tr[++num]=t;
		int p=num;
		while((p>>1)&&tr[p>>1].w>tr[p].w){
			swap(tr[p>>1],tr[p]);
			p>>=1;
		}
	}
	void pop(){
		swap(tr[1],tr[num]);
		num--;
		int p=1,q=2;
		while(q<=num){
			if(q<num&&tr[q|1].w<tr[q].w)q|=1;
			if(tr[p].w<=tr[q].w)break;
			swap(tr[p],tr[q]);
			p=q;q=p<<1;
		}
	}
}pq;
struct egde{
	int to,l,w;
}e[10005],E[30005];
void eAd(int x,int y,int w){
	++el;
	E[el].to=y;E[el].l=H[x];H[x]=el;E[el].w=w;
}
bool SPFA(){
	while(!Q.empty())Q.pop();
	for(auto x:V)iq[x]=iq[x+n]=0;
	iq[2004]=1;
	Q.push(2004);
	while(!Q.empty()){
		int x=Q.front();
		Q.pop();
		iq[x]=0;
		for(int i=H[x];i;i=E[i].l){
			int to=E[i].to;
			if(w[to]>w[x]+E[i].w){
				w[to]=w[x]+E[i].w;
				v[to]=v[x]+1;
				if(v[to]>vs)return 0;
				if(!iq[to]){
					iq[to]=1;
					Q.push(to);
				}
			}
		}
	}
	return 1;
}
bool bfs(int rt){
	while(!Q.empty())Q.pop();
	for(auto x:V)iq[x]=iq[x+n]=0;
	Q.push(rt);
	iq[rt]=1;
	while(!Q.empty()){
		int x=Q.front();
		Q.pop();
		for(int i=H[x];i;i=E[i].l){
			int to=E[i].to;
			if(!iq[to]){
				if(to==rt+n)return 1;
				iq[to]=1;
				Q.push(to);
			}
		}
	}
	return 0;
}
void dij(int rt){
	pq.clear();
	for(auto x:V)dis[rt][x]=dis[rt][x+n]=inf;
	dis[rt][rt]=0;
	pq.push((P){rt,0});
	while(!pq.empty()){
		P t=pq.top();
		pq.pop();
		if(t.x==rt+n)break;
		if(t.w!=dis[rt][t.x])continue;
		for(int i=H[t.x];i;i=E[i].l){
			int to=E[i].to;
			if(dis[rt][to]>t.w+E[i].w){
				pq.push((P){to,dis[rt][to]=t.w+E[i].w});
			}
		}
	}
	if(dis[rt][rt+n]<inf)ans[rt]=dis[rt][rt+n]-w[rt]+w[rt+n];
	else ans[rt]=inf;
}
void slv(){
	H[2004]=v[2004]=w[2004]=0;
	el=0;
	vs<<=1;
	for(auto x:V){
		for(int i=h[x];i;i=e[i].l){
			int to=e[i].to;
			if(!iv[to])continue;
			if(e[i].w%2==0){
				eAd(x,to,e[i].w);
				eAd(x+n,to+n,e[i].w);
			}else {
				eAd(x,to+n,e[i].w);
				eAd(x+n,to,e[i].w);
			}
		}
		eAd(2004,x,0);
		eAd(2004,x+n,0);
		w[x]=w[x+n]=inf;
		v[x]=v[x+n]=0;
	}
	for(auto x:V)iv[x]=0;
	if(!SPFA()){
		bool fl=bfs(V[0]);
		for(auto x:V){
			if(fl)ans[x]=inf+1;
			else ans[x]=inf;
		}
		return;
	}
	for(auto x:V){
		for(int i=H[x];i;i=E[i].l)E[i].w+=w[x]-w[E[i].to];
		for(int i=H[x+n];i;i=E[i].l)E[i].w+=w[x+n]-w[E[i].to];
	}
	for(auto x:V)dij(x);
}
void dfs(int x){
	df[x]=lw[x]=++dfn;
	is[x]=1;
	st[++tp]=x;
	for(int i=h[x];i;i=e[i].l){
		int to=e[i].to;
		if(!df[to]){
			dfs(to);
			lw[x]=min(lw[x],lw[to]);
		}else if(is[to])lw[x]=min(lw[x],df[to]);
	}
	if(df[x]==lw[x]){
		int y;
		vs=0;
		do{
			y=st[tp--];
			is[y]=0;
			V.push_back(y);
			iv[y]=1;
			vs++;
		}while(y!=x);
		slv();
		V.clear();
	}
}
int main(){
	scanf("%d %d",&n,&m);
	for(int i=1;i<=m;++i){
		int x,y,z;
		scanf("%d %d %d",&x,&y,&z);
		e[i].to=y;e[i].l=h[x];h[x]=i;e[i].w=z;
	}
	for(int i=0;i<n;++i)if(!df[i])dfs(i);
	for(int i=0;i<n;++i){
		if(ans[i]>inf)puts("Haha, stupid Honkai");
		else if(ans[i]==inf)puts("Battle with the crazy Honkai");
		else printf("%lld\n",ans[i]);
	}
	return 0;
}
```