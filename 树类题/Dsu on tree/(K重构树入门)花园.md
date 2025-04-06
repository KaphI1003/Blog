# (Kruskal 重构树入门)花园
### 题目描述
Panda先生有很多花园，每个花园只有一朵花颜色为C_i
 的花。在花园之间，有很多道路连接着它们，每条路都都要花费一定的时间。

Panda先生喜欢在花园之间闲逛，采摘尽可能多的相同颜色的花。但是他不喜欢在路上花太多的时间，如果一条道路太长了，他就不愿走。

现在问题来了，如果 Panda 先生从编号为x的花园出发，只经过花费时间不超过w的道路，最多可以得到多少颜色一样的花朵。输出编号，如果有多种，输出编号最小的。

#### 输入格式

第一行三个整数n,m,type。其中n表示花园的个数，m表示道路的条数，type表示询问的类型，type=0表示离线，type=1表示在线。

第二行n个整数，表示每个花园中花朵的颜色。

接下来m行，每行三个整数a,b,w表示有一条长度为w的道路，连接着编号为a和b的花园。

接下来一个整数Q，表示询问的个数。

每个询问一行，两个整数x和w。

如果type=1，则本次查询的$x=x\ xor\ lastans,w=w\ xor\ lastans$;

lastans为上一次询问的结果，刚开始lastans=0。保证每次真正询问的x为[1,n]范围内的数。

$C_i \in [1,n],w \leq 10^6$
 

#### 输出格式
对于每个询问，输出一个整数，表示可以摘到最多的花朵的编号。

### 分析:
题目就是让我们每一次查询一个块中最多的数字是多少，明显，有用的只有图中最小生成树的边，那么我们就可以发现我们要查询的块在用Kruskal 算法建最小生成树时候就都会出现过，但是我们并不能简单的把信息合并在一起，并且题目中要求在线询问，也就是说我们还要把**合并前的信息全都记下**，暴力一定是不行的，那么我们就可以考虑考虑 **Kruskal重构树**。

Kruskal 重构树与最小生成树的不同就是每一次合并时，把原本两颗树的根连向一个新节点，然后以新节点作为合并后树的根。

那么每一次询问就变成向上跳找到合法最大子树，接着找到子树里中最多的数字是多少。那么直接DSU预处理答案就可以了。

##### 时间复杂度： $O(n\log n)$

##### code:
```
#include<bits/stdc++.h>
using namespace std;
const int N=2e5+5;
int n,m,tp,f[N],F[19][N],sn[2][N],w[N],up[N>>1],l[N],sz[N],rs,ans[N],cnt[N],dp[N],la,r[N],ql,q[N];
struct P{
	int x,y,w;
	bool operator < (const P&t)const {
		return w<t.w;
	}
}a[300005];
int fi(int x){
	return x==f[x]? x:f[x]=fi(f[x]);
}
void R(int &x){
	x=0;char c=getchar();
	while(!isdigit(c))c=getchar();
	while(isdigit(c)){x=(x<<1)+(x<<3)+(c^48);c=getchar();}
}
void dfs(int x){
	l[x]=ql+1;
	if(!sn[0][x]){
		ans[x]=w[x];
		cnt[rs=w[x]]++;
		q[++ql]=w[x];
		r[x]=ql;
		return;
	}
	dfs(sn[1][x]);
	const int up=ql;
	for(int i=l[sn[1][x]];i<=up;++i)cnt[q[i]]--;
	rs=0;
	dfs(sn[0][x]);
	for(int i=l[sn[1][x]];i<=up;++i){
		cnt[q[i]]++;
		if(cnt[q[i]]>cnt[rs]||(cnt[q[i]]==cnt[rs]&&q[i]<rs))rs=q[i];
	}
	ans[x]=rs;
	r[x]=ql;
}
int go(int x,int y){
	for(int i=up[dp[x]];i>=0;i--)if(w[F[i][x]]<=y)x=F[i][x];
	return x;
}
int main(){
	int x,y;
	R(n);R(m);R(tp);
	up[0]=dp[0]=-1;
	w[0]=1e9;
	for(int i=1;i<=n;++i){
		f[i]=i;
		up[i]=up[i>>1]+1;
		R(w[i]);
		sz[i]=1;
	}
	for(int i=1;i<=m;++i)R(a[i].x),R(a[i].y),R(a[i].w);
	sort(a+1,a+m+1);
	for(int i=1;i<=m;++i){
		x=fi(a[i].x);y=fi(a[i].y);
		if(x!=y){
			f[x]=f[y]=F[0][x]=F[0][y]=++n;
			if(sz[x]<sz[y])swap(x,y);
			sn[0][n]=x;sn[1][n]=y;//建树 
			w[n]=a[i].w;f[n]=n;
			sz[n]=sz[x]+sz[y];
		}
	}
	for(int i=n;i;i--){
		dp[i]=dp[F[0][i]]+1;
		for(int o=1;o<=up[dp[i]];++o)F[o][i]=F[o-1][F[o-1][i]];
		if(!dp[i]){//图可能不连通 
			dfs(i);
			for(int o=l[i];o<=r[i];++o)cnt[q[o]]--;
			rs=0;
		}
	}
	R(m);
	for(int i=1;i<=m;++i){
		R(x);R(y);
		if(tp){x^=la;y^=la;}
		printf("%d\n",la=ans[go(x,y)]); 
	}
	fclose(stdin);fclose(stdout);
	return 0;
}
```