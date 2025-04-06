# [Cactus Wall](https://codeforces.com/contest/1749/problem/E)
### 简要题意：

有个 $n*m$ 的地图，图上已经有了一些仙人掌，你可以在上面放仙人掌，但是两个仙人掌是不可以相邻的，问最少放多少个仙人掌可以将地图分成上下两个不互通的部分。输出方案。

### 问题分析：
很明显将图黑白染色后，我们要的分割线一定是由同色的仙人掌构成。那么我们可以考虑从左边搞多源最短路。通过分析我们还可以发现一次更新所产生的新点的权值要么加0，要么加1，所以我们可以使用双段队列广搜来代替优先队列，来优化时间复杂度。
初始化双端队列时也要考虑将0放前面，1放后面。
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=4e5+5,k[]={1,1,-1,-1},K[]={1,-1,-1,1};
int T,n,m;
struct P{
	int x,y;
};
deque<P>Q;
void slv(){
	scanf("%d %d",&n,&m);
	char c[n+5][m+5]={};
	int dp[n+5][m+5],v[n+5][m+5];
	P fr[n+5][m+5];
	memset(v,0,sizeof(v));
	memset(fr,0,sizeof(fr));
	memset(dp,-1,sizeof(dp));
	for(int i=1;i<=n;++i){
		scanf("%s",c[i]+1);
	}
	while(!Q.empty())Q.pop_back();
	for(int o=1;o<=n;++o){
		if(c[o-1][1]=='#')continue;
		if(c[o+1][1]=='#')continue;
		if(c[o][2]=='#')continue;
		if(c[o][1]=='#'){
			dp[o][1]=0;
			Q.push_back(P{o,1});
		}
	}
	for(int o=1;o<=n;++o){
		if(c[o-1][1]=='#')continue;
		if(c[o+1][1]=='#')continue;
		if(c[o][2]=='#')continue;
		if(c[o][1]=='.'){
			dp[o][1]=1;
			Q.push_back(P{o,1});
		}
	}
	while(!Q.empty()){
		P t=Q.front();
		Q.pop_front();
		if(v[t.x][t.y])continue;
		v[t.x][t.y]=1;
		for(int i=0;i<4;++i){
			int x=t.x+k[i],y=t.y+K[i];
			if(x<1||x>n)continue;
			if(y<1||y>m)continue;
			if(v[x][y])continue;
			if(c[x-1][y]=='#')continue;
			if(c[x+1][y]=='#')continue;
			if(c[x][y-1]=='#')continue;
			if(c[x][y+1]=='#')continue;
			int w=(c[x][y]=='#')? dp[t.x][t.y]:(1+dp[t.x][t.y]);
			if(dp[x][y]==-1||dp[x][y]>w){
				fr[x][y]=t;
				dp[x][y]=w;
				if(c[x][y]=='#')Q.push_front((P){x,y});
				else Q.push_back((P){x,y});
			}
		}
	}
	int nx=n+1,ny=m+1;
	dp[nx][ny]=1e9;
	for(int i=1;i<=n;++i)if(dp[i][m]>-1){
		if(dp[i][m]<dp[nx][ny])nx=i,ny=m;
	}
	if(nx==n+1){puts("NO");return;}
	puts("YES");
	while(nx){
		c[nx][ny]='#';
		int x=fr[nx][ny].x,y=fr[nx][ny].y;
		nx=x,ny=y;
	}
	for(int i=1;i<=n;++i)puts(c[i]+1);
}
int main(){
	scanf("%d",&T);
	while(T--)slv();
	return 0;
}
```