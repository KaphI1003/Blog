# [Tenzing and Random Real Numbers](https://codeforces.com/contest/1842/problem/H)

### 简要题面
$n$个$[0,1]$中的随机实数，有$m$条限制，每条限制为$x_i+x_j \le 1$或$x_i+x_j \ge 1$。问满足所有限制的可能性是多少，输出在模$998244353$下的答案。

$n \le 20 ，m\le n*(n+1)$
### 问题分析

将 $0.5$从 $[0,1]$ 中剔除不会影响答案，将点分类，$x_i < 0.5$的为白点，$x_i>0.5$为黑点。我们设 $y_i = \min(x_i,1-x_i)$，那么$y_i$就是在 $[0,0.5]$ 中的随机变量。我们枚举排列$p$，让 $y_{p_1}<y_{p_2}<y_{p_3}<···<y_{p_n}$，现在限制就变成了在排列中，较前的点必须为白色或必须为黑色。因为都是随机变量，所以每种排列出现的概率也相同，这样我们就跳出了实数的限制。

我们考虑状压，从后往前确定排列中每个位置填的点。每次和后面已经确定的点看一下限制，得到能选的颜色，直接转移方案数即可。

##### 时间复杂度 $O(n2^n)$
##### code
```
#include<bits/stdc++.h>
using namespace std;
const int N=22,M=998244353;
int n,m,dp[1<<20],e[1<<20][N];
void ck(int &x){(x>=M)&&(x-=M);}
int pw(int x,int t){
	int rs=1;
	for(;t;t>>=1,x=1ll*x*x%M)if(t&1)rs=1ll*rs*x%M;
	return rs;
}
int main(){
	scanf("%d %d",&n,&m);
	for(int i=1;i<=m;++i){
		int k,x,y;
		scanf("%d %d %d",&k,&x,&y);
		x--,y--;
		e[1<<x][y]|=1<<k,e[1<<y][x]|=1<<k;
	}
	int k=1<<n,w=k;
	for(int i=2;i<=n;++i)w=1ll*w*i%M;
	dp[0]=pw(w,M-2);
	for(int i=1;i<k;++i){
		for(int j=0;j<n;++j){
			e[i][j]=e[i&(i-1)][j]|e[i&-i][j];
		}
		for(int j=0;j<n;++j)if(i&(1<<j)){
			int p=e[i][j];
			if(!(p&1))ck(dp[i]+=dp[i^(1<<j)]);
			if(!(p&2))ck(dp[i]+=dp[i^(1<<j)]);
		}
	}
	printf("%d\n",dp[k-1]);
	return 0;
}
```