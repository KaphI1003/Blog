# [Miriany and Matchstick](https://codeforces.com/contest/1852/problem/D)

### 简要题面
一个$2*n$的格子图，每个格子只能填 A 或 B ，第一行已经被填好了，你现在要给出一种第二行的填法，使得相邻两个格子但填的字母不同的格子的对数正好是$k$。可能有无解
情况。
$n\le 2·10^5$
### 问题分析
很容易就能想到$O(n^2)$的 DP 怎么写，也很容易知道这个可以 bitset 优化，但是这样还是不能够通过此题。

分析得到 bitset 优化$O(n^2)$ DP 需要的操作只有左移和或。那么我们考虑换一个方法，直接存储DP状态中 bitset 对应的所有 1 的区间。~~（通过感觉）~~可以发现每个 DP 状态的区间其实很少，所以我们只记区间的$l,r$，左移即端点加，或即合并区间。

##### code
```
#include<bits/stdc++.h>
#define l first
#define r second
#define DP vector<pair<int,int> >
#define mp make_pair
using namespace std;
const int N=2e5+5;
int T,n,k;
char s[N],c[N];
DP dp[N][2];
DP add(DP v,int w){
	DP rs={};
	for(auto x:v){
		rs.push_back(mp(x.l+w,x.r+w));
	}
	return rs;
}
DP merge(DP x,DP y){
	int n=x.size(),m=y.size();
	DP rs={};
	int i=0,j=0,l=0;
	if(x[0].l<y[0].l)rs.push_back(x[i++]);
	else rs.push_back(y[j++]);
	while(i<n||j<m){
		pair<int,int>t =mp(0,0);
		if(i==n)t=y[j++];
		else if(j==m)t=x[i++];
		else if(x[i].l<y[j].l)t=x[i++];
		else t=y[j++];
		if(rs[l].r+1>=t.l){
			rs[l].r=max(rs[l].r,t.r);
		}else {
			rs.push_back(t),l++;
		}
	}
	return rs;
}
bool in(DP v,int w){
	for(auto x:v){
		if(x.l<=w&&w<=x.r)return 1;
	}
	return 0;
}
void slv(){
	scanf("%d %d",&n,&k);
	scanf("%s",s+1);
	dp[1][0].clear(),dp[1][1].clear();
	dp[1][0].push_back(mp((int)(s[1]!='A'),(int)(s[1]!='A')));
	dp[1][1].push_back(mp((int)(s[1]!='B'),(int)(s[1]!='B')));
	for(int i=2;i<=n;++i){
		dp[i][0]=add(merge(dp[i-1][0],add(dp[i-1][1],1)),(s[i]!='A')+(s[i-1]!=s[i]));
		dp[i][1]=add(merge(dp[i-1][1],add(dp[i-1][0],1)),(s[i]!='B')+(s[i-1]!=s[i]));
	}
	if(!in(dp[n][0],k)&&!in(dp[n][1],k)){
		puts("NO");
		return;
	}
	c[n+1]=0;
	int p=in(dp[n][1],k);
	c[n]='A'+p;
	for(int i=n-1;i;i--){
		k-=(s[i+1]!=c[i+1])+(s[i]!=s[i+1]);
		if(!in(dp[i][p],k))p^=1,k--;
		c[i]='A'+p;
	}
	puts("YES");
	puts(c+1);
}
int main(){
	scanf("%d",&T);
	while(T--)slv();
	return 0;
}
```