# Dash(es) Speed
#### 题目描述
比特山是比特镇的飙车圣地。在比特山上一共有 n 个广场，编号依次为 1 到 n，这些广场之间通过 n − 1 条双向车道直接或间接地连接在一起，形成了一棵树的结构。

因为每条车道的修建时间以及建筑材料都不尽相同，所以可以用两个数字 li, ri 量化地表示一条车道 的承受区间，只有当汽车以不小于 li 且不大于 ri 的速度经过这条车道时，才不会对路面造成伤害。

Byteasar 最近新买了一辆跑车，他想在比特山飙一次车。Byteasar 计划选择两个不同的点 S, T ，然 后在它们树上的最短路径上行驶，且不对上面任意一条车道造成伤害。

Byteasar 不喜欢改变速度，所以他会告诉你他的车速。为了挑选出最合适的车速，Byteasar 一共会 向你询问 m 次。请帮助他找到一条合法的道路，使得路径上经过的车道数尽可能多。


#### 输入
第一行包含两个正整数 n, m，表示广场的总数和询问的总数。

接下来 n − 1 行，每行四个正整数 ui, vi, li, ri，表示一条连接 ui 和 vi 的双向车道，且承受区间为[li, ri]。

接下来 m 行，每行一个正整数 qi，分别表示每个询问的车速。


### 输出
输出 m 行，每行一个整数，其中第 i 行输出车速为 qi 时的最长路径的长度，如果找不到合法的路 径则输出 0。

#### 样例输入
5 3	

3 2 2 4

1 5 2 5

4 5 2 2

1 2 3 5

1

2

3
#### 样例输出
0

2

3
#### 提示

### 样例解释：
当车速为 1 时，不存在合法的路径。

当车速为 2 时，可以选择 1-5-4 这条路径，长度为 2。

当车速为 3 时，可以选择 3-2-1-5 这条路径，长度为 3


对于100% 的数据，1  ≤ ui , vi , qi  ≤ n ; 1  ≤ li  ≤ ri  ≤ n。

### 分析：

关于链的情况就是单点修改求最大连续线段，套个线段树就好了。

关于l=1的点就是离线之后就是只有加边操作，那么问题就转变为如何快速求把两棵树连起来之后求直径。那么新的树的直径就应该是原来两颗树中直径两端的4个端点中的一对组合。因为把两颗树连起来之后的直径只有可能是原来的两颗树的直径或者是被连起来的两个点在原树上能拉出来的最长的两条链通过新加的边拼在一起的链。那么在通过树上一点能拉出的最长链的另一端一定是直径上一点的性质，我们就可以得出这个结论。那么我们通过并查集维护就可以了。

关于整道题，我们就需要有删边操作。如果删边的顺序与加边的顺序正好是相反的（就好比一个栈，先加的边后删）。那么删边操作就会变得非常简单（只需并查集不使用路径压缩用按秩合并，直接把数组还原就可以了）。但是我们直接按照时间顺序来就不满足这个性质。那么我们就需要一个新的顺序。

通过观察，我们发现大区间的边在被大区间包含的小区间里一定也可以使用。那么这里就是题目中隐藏(~~明显~~)的单调性。那么我们就可以考虑分治。

关于区间[l,r],我们在这是把完全包含这个区间并且未使用的边全部加上，然后再考虑分治[l,mid]和[mid+1,r]。回溯时就直接修改数组就可以了。

关于怎么快速加边，我们只需要一个像线段树询问的操作，把每一条线段的区间分成$\log n$小区间，然后在小区间下面挂上这条边即可。

##### 时间复杂度 $O(n \log^2n)$
##### code:
```
#include<bits/stdc++.h>
using namespace std;
const int N=70005;
int n,m,l[N],r[N],le[N],ll,rr,u[N],v[N],s[N],I,f[N],sz[N],sn[N],fa[N],tp[N],dp[N],dep[N],h[N],top,rs,ans[N];
struct E{
	int l,to;
}e[N<<1];
struct rec{
	int fa,sn,l,r,le,dp;
}rc[N];
vector<int>V[N],sg[N<<2];
void Ad(int z,int y,int c){//分解边区间 
	if(z>rr||y<ll)return;
	if(z>=ll&&y<=rr){
		sg[c].push_back(I);
		return;
	}
	int mid=z+y>>1;
	if(ll<=mid)Ad(z,mid,c<<1);
	if(rr>mid)Ad(mid+1,y,c<<1|1);
}
int fi(int x){return x==f[x]? x:fi(f[x]);}
void se(int x){
	sz[x]=1;
	for(int i=h[x];i;i=e[i].l){
		int to=e[i].to;
		if(to==fa[x])continue;
		fa[to]=x;dp[to]=dp[x]+1;
		se(to);
		sz[x]+=sz[to];
		if(sz[to]>sz[sn[x]])sn[x]=to;
	}
}
void dfs(int x,int Tp){
	tp[x]=Tp;
	if(sn[x])dfs(sn[x],Tp);
	for(int i=h[x];i;i=e[i].l){
		int to=e[i].to;
		if(to==fa[x]||to==sn[x])continue;
		dfs(to,to);
	}
}
int len(int x,int y){
	int ll=dp[x]+dp[y];
	while(tp[x]!=tp[y]){
		if(dp[tp[x]]<dp[tp[y]])y=fa[tp[y]];
		else x=fa[tp[x]];
	}
	return ll-((dp[x]<dp[y]? dp[x]:dp[y])<<1);
}
void nw(int x,int y){//加边 
	x=fi(x);y=fi(y);
	int LE=max(le[x],le[y]),L=(LE==le[x])? l[x]:l[y],R=(LE==le[x])? r[x]:r[y],G;
	G=len(l[x],l[y]);
	if(G>LE){LE=G;L=l[x],R=l[y];}
	G=len(l[x],r[y]);
	if(G>LE){LE=G;L=l[x],R=r[y];}
	G=len(r[x],l[y]);
	if(G>LE){LE=G;L=r[x],R=l[y];}
	G=len(r[x],r[y]);
	if(G>LE){LE=G;L=r[x],R=r[y];}
	if(dep[x]>dep[y]){
		rc[++top]=(rec){x,y,l[x],r[x],le[x],dep[x]};
		f[y]=x;
		le[x]=LE;l[x]=L;r[x]=R;
	}else {
		rc[++top]=(rec){y,x,l[y],r[y],le[y],dep[y]};
		f[x]=y;
		(dep[y]==dep[x]&&(dep[y]++));
		le[y]=LE;l[y]=L;r[y]=R;
	}
	rs=max(rs,LE);
}
void re(int nl){//删边 
	f[rc[nl].sn]=rc[nl].sn;
	l[rc[nl].fa]=rc[nl].l;
	r[rc[nl].fa]=rc[nl].r;
	le[rc[nl].fa]=rc[nl].le;
	dep[rc[nl].fa]=rc[nl].dp;
}
void so(int z,int y,int c){//分治区间 
	int sz=sg[c].size();
	for(int i=0;i<sz;++i){
		nw(u[sg[c][i]],v[sg[c][i]]);
	}
	if(z==y){
		int sl=V[z].size();
		for(int i=0;i<sl;++i){ans[V[z][i]]=rs;}
		return;
	}
	int ltp=top,lrs=rs,mid=z+y>>1;
	if(s[mid]-s[z-1]){
		so(z,mid,c<<1);
		while(top!=ltp)re(top--);
		rs=lrs;
	}
	if(s[y]-s[mid]){
		so(mid+1,y,c<<1|1);
		while(top!=ltp)re(top--);
		rs=lrs;
	}
}
int main(){
	int x;
	scanf("%d %d",&n,&m);
	for(I=1;I<n;++I){
		scanf("%d %d %d %d",&u[I],&v[I],&ll,&rr);
		e[I<<1].to=u[I];e[I<<1].l=h[v[I]];h[v[I]]=I<<1;
		e[I<<1|1].to=v[I];e[I<<1|1].l=h[u[I]];h[u[I]]=I<<1|1;
		Ad(1,n,1);
	}
	se(1);dfs(1,1);
	for(int o=1;o<=m;++o){
		scanf("%d",&x);
		V[x].push_back(o);
		s[x]=1;
	}
	for(int i=1;i<=n;++i){f[i]=l[i]=r[i]=i;s[i]+=s[i-1];}
	so(1,n,1);
	for(int i=1;i<=m;++i)printf("%d\n",ans[i]);
}
```