# [World Fragments II](https://ac.nowcoder.com/acm/contest/57357/F)
~~（虽然不是第一次见了这种题了，但做法确实是赛后看题解才回忆起来）~~

### 简要题面
有$T$组询问，每次询问问你$x$变到$y$最少需要几次操作，一次操作你可以在$x$的十进制表示中出现的数中任选一个（设之为 $b$），使$x$变成$x-b$或$x+b$。例如对 $114514$ 你可以选择的$b$有$1,4,5$。

$T,x,y \le 3·10^5$

强制在线

### 问题分析

显然一次操作最多让数的大小产生$9$的波动，这就可以让我们使用分治或分块解决题目。

分治的话每次取中间 9 个点为特殊点，预处理分治区间内每一个点到特殊点和特殊点到每一个点的最短距离。查询时如果有一个点是特殊点直接返回答案，否则枚举特殊点，算$x$到特殊点与特殊点到$y$的和的最小值，然后继续向下分治。

分块的话首先计算每个块内的两点之间的最短距离，分块时让相邻的两个块之间有 9 格的重叠，然后每次$O(k)$暴力跨块即可，此题使用 TLE 的可能太大，于是使用分治。

PS :此题要注意分治的区间右端点要为$3e5+10$而不是$3e5$，不然可能会导致答案偏大或无解。

##### 时间复杂度 $O(kn\log n)$
##### code
```
#include<bits/stdc++.h>
using namespace std;
const int N=3e5+15,inf=1e9;
int n=300010,T,u,v,rs,fr[21][10][N],to[21][10][N],q[N],ql;
vector<int>Fr[N],To[N];
void bfs(int rt,int l,int r,int *fr,int *to){
	for(int i=l;i<=r;++i)fr[i]=to[i]=-1;
	fr[rt]=0,q[ql=1]=rt;
	for(int i=1;i<=ql;++i){
		int x=q[i];
		for(auto y:Fr[x])if(l<=y&&y<=r&&fr[y]==-1){
			fr[y]=fr[x]+1;
			q[++ql]=y;
		}
	}
	to[rt]=0,q[ql=1]=rt;
	for(int i=1;i<=ql;++i){
		int x=q[i];
		for(auto y:To[x])if(l<=y&&y<=r&&to[y]==-1){
			to[y]=to[x]+1;
			q[++ql]=y;
		}
	}
}
void init(int z,int y,int dep){
	if(z>y)return;
	int mid=(z+y)>>1;
	int l=max(z,mid-4),r=min(y,mid+4);
	init(z,l-1,dep+1),init(r+1,y,dep+1);
	for(int i=l;i<=r;++i)bfs(i,z,y,fr[dep][i-l],to[dep][i-l]);
}
int slv(int z,int y,int dep,int u,int v){
	int mid=(z+y)>>1;
	int l=max(z,mid-4),r=min(y,mid+4);
	if(l<=u&&u<=r)return to[dep][u-l][v];
	if(l<=v&&v<=r)return fr[dep][v-l][u];
	for(int i=l;i<=r;++i){
		if(fr[dep][i-l][u]>=0&&to[dep][i-l][v]>=0)
			rs=min(rs,fr[dep][i-l][u]+to[dep][i-l][v]);
	}
	if(u<l&&v<l)return slv(z,l-1,dep+1,u,v);
	if(u>r&&v>r)return slv(r+1,y,dep+1,u,v);
	return -1;
}
int main(){
	for(int i=1;i<=n;++i){
		int t=i;
		while(t){
			int x=t%10;
			t/=10;
			Fr[i-x].push_back(i);
			To[i].push_back(i-x);
			if(i+x>n)continue;
			Fr[i+x].push_back(i);
			To[i].push_back(i+x);
		}
	}
	init(0,n,0);
	while(T--){
		scanf("%d %d",&u,&v);
		u^=rs+1,v^=rs+1;
		rs=inf;
		int w=slv(0,n,0,u,v);
		if(w>=0)rs=min(rs,w);
		if(rs==inf)rs=-1;
		printf("%d\n",rs);
	}
	return 0;
}
```