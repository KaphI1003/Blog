# Hotel

[题目链接](https://darkbzoj.cc/problem/4543)

#### 题目描述

有一个树形结构的宾馆，n个房间，n-1条无向边，每条边的长度相同，任意两个房间可以相互到达。吉丽要给他的三个妹子各开（一个）房（间）。三个妹子住的房间要互不相同（否则要打起来了），为了让吉丽满意，你需要让三个房间两两距离相同。 有多少种方案能让吉丽满意？

#### 输入

第一行一个数n（n≤5000）。

接下来n-1行，每行两个数x,y，表示x和y之间有一条边相连。

#### 输出

让吉丽满意的方案数。

##### 样例输入

7

1 2

5 7

2 5

2 3

5 6

4 5

##### 样例输出

5

### 分析：

题目中三点的距离需要相同，也就意味着图中有一个点使他们到该点的距离都相同。那么我们就可以枚举这个点，以该点为根建树，接下来就是寻找三个深度相同且不在同一颗子树点的组合个数。

时间复杂度：$O(n^2)$

#### code：

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=5005;
int n,h[N],k[N],f[N],s[N],S[N];
long long t[N],ans;
struct P{
    int l,to;
}e[N<<1];
void dfs(int x,int fa,int d){
	k[d]++;
	for(int i=h[x];i;i=e[i].l){
		if(e[i].to!=fa)dfs(e[i].to,x,d+1);
	}
}
void so(int x){
	for(int i=1;i<=n;++i){if(!f[i])break;t[i]=s[i]=f[i]=0;}
	for(int i=h[x];i;i=e[i].l){
		for(int o=1;o<=n;++o){if(!k[o])break;k[o]=0;}
		dfs(e[i].to,x,1);
		for(int o=1;o<=n;++o){
			if(!k[o])break;
			t[o]+=1ll*s[o]*k[o];
			s[o]+=f[o]*k[o];
			f[o]+=k[o];
		}
	}
	for(int i=1;i<=n;++i){if(!t[i])break;ans+=t[i];}
}
int main(){
	int x,y;
	scanf("%d",&n);
	for(int i=1;i<n;++i){
		scanf("%d %d",&x,&y);
		e[i<<1].to=x;e[i<<1].l=h[y];h[y]=i<<1;
		e[i<<1|1].to=y;e[i<<1|1].l=h[x];h[x]=i<<1|1;
		S[x]++;S[y]++;
	}
	for(int i=1;i<=n;++i){
		if(S[i]>2)so(i);
	}
	printf("%lld",ans);
}
```

考虑如何更快的通过该题，我们考虑树形DP。令$f[x][i]$表示$x$子树内与$x$距离为$i$的点的个数，$g[x][i]$为两个点离它们的 lca 距离都为 $d$ ，且它们的 lca 在 $x$子树内与$x$距离为$d-j$的个数。开始时让f(x,0)为1，其它为 0 。

考虑合并一个$x$的儿子$y$，首先计算它们对 ans 产生的贡献。我们可以计算得到答案为
$$
ans+=\sum_i g[x][i+1]*f[y][i]+f[x][i]*g[y][i+1]
$$
然后再考虑$f,g$数组如何更新，可以得到
$$
g[x][i]+=g[y][i+1]+f[x][i]*f[y][i-1] 
$$
$$
f[x][i]+=f[y][i-1]
$$

但是直接 DP 的复杂度仍然是 $O(n^2)$，考虑如何优化，可以发现合并第一个儿子时几乎就是继承了儿子的状态，我们考虑使用长链剖分，每次直接继承长儿子的 DP 状态，这样我们就把复杂度优化到了$O(n)$

##### 时间复杂度 $O(n)$
##### code
```cpp
#include<bits/stdc++.h>
typedef  long long LL;
using namespace std;
const int N=1e6+5;
int n,dep[N],mx[N],sn[N],top[N],h[N];
LL *f[N],*g[N],ans;
struct E{
	int l,to;
}e[N<<1];
void dfs1(int x,int f){
	mx[x]=dep[x];
	for(int i=h[x];i;i=e[i].l){
		int to=e[i].to;
		if(to==f)continue;
		dep[to]=dep[x]+1;
		dfs1(to,x);
		if(mx[to]>mx[x]){
			mx[x]=mx[to];
			sn[x]=to;
		}
	}
}
LL &F(int x,int y){
	return f[top[x]][y+dep[x]-dep[top[x]]];
}
LL &G(int x,int y){
	return g[top[x]][y+mx[x]-dep[x]];
}
void dfs2(int x,int fa){
	top[x]=(sn[fa]==x)? top[fa]:x;
	int u=top[x];
	if(u==x){
		f[u]=(LL*)malloc((mx[x]-dep[x]+5)*sizeof(LL));
		g[u]=(LL*)malloc(2*(mx[x]-dep[x]+5)*sizeof(LL));
		memset(f[u],0,(mx[x]-dep[x]+5)*sizeof(LL));
		memset(g[u],0,2*(mx[x]-dep[x]+5)*sizeof(LL));
	}
	if(sn[x])dfs2(sn[x],x);
	F(x,0)=1;
	ans+=G(x,0);
	for(int i=h[x];i;i=e[i].l){
		int y=e[i].to;
		if(y==fa||y==sn[x])continue;
		dfs2(y,x);
		for(int j=mx[y]-dep[y];~j;--j){
			ans+=G(x,j+1)*F(y,j);
			if(j)ans+=G(y,j)*F(x,j-1);
		}
		for(int j=mx[y]-dep[y];~j;--j){
			if(j)G(x,j-1)+=G(y,j);
			G(x,j+1)+=F(x,j+1)*F(y,j);
			F(x,j+1)+=F(y,j);
		}
	}
}
int main(){
	scanf("%d",&n);
	for(int i=1;i<n;++i){
		int x,y;
		scanf("%d %d",&x,&y);
		e[i<<1].to=x;e[i<<1].l=h[y];h[y]=i<<1;
		e[i<<1|1].to=y;e[i<<1|1].l=h[x];h[x]=i<<1|1;
	}
	dfs1(1,0);
	dfs2(1,0);
	printf("%lld\n",ans);
	return 0;
}
```