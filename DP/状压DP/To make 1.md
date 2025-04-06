# [To make 1](https://codeforces.com/contest/1246/problem/E)
### 简要题面
给 n 个数 $a_i$和 k，每次可以合并两个数 x,y 得到的结果是$f(x+y)$。

$$
f(x)= f(x/k),x\mod k=0
$$
$$
f(x)=x,x\mod k\ne 0
$$
问有没有一种方案，使最后只剩一个 1 
### 分析

先通过dp来判断存不存在解。

跟普通的01背包差不多，当 $dp[i][j*k]=1$ ，令$dp[i][j]=1$，但是$dp[i][j*k]$仍然保持为1。这样可以让合成路径树可以表现成完全刷子型。DP的复杂度也就可以从$O(3^n)->O(n*2^n)$

存在解的充要条件是存在一组$b_i$,满足$\sum_1^n {\dfrac{a_i}{b_i}}=1$。

那么问题就在于如何求解$b_i$,可以从 dp 数组逆推得到。

最后就是如何输出方案的问题。

把所有的数按 $b_i$分类，$b_i$相同的两个数可以合并。由于最后可以合成 1 。那么对于$b_i$最大的那个组一定有 $\sum a_i \mod k=0$， 那么我们就可以一直合并直到和整除 0 ,然后在把和加入到其它$b_i$的组里。一直执行下去就可以得到方案。

#### code
```
#include<bits/stdc++.h>
using namespace std;
const int N=25;
stack<int>stk[N*10];
int n,k,a[N],s;
bitset<2001>dp[65536];
int main(){
	scanf("%d %d",&n,&k);
	dp[0][0]=1;
	int up=1<<n;
	for(int i=1;i<=n;++i){
		scanf("%d",&a[i]);
		s+=a[i];
	}
	for(int i=1;i<up;++i){
		for(int o=1,u=1;u<=up;++o,u<<=1)if(u&i){
			dp[i]|=dp[i^u]<<a[o];
		}
		for(int o=s/k*k;o;o-=k)if(dp[i][o])dp[i][o/k]=1;
	}
	if(!dp[up-1][1])return puts("NO"),0;
	puts("YES");
	int b=0;
	for(int w=1,c=up-1;w;){
		while(w*k<=s&&dp[c][w*k])w*=k,b++;
		for(int i=1,o=1;i<=n;++i,o<<=1)if(a[i]<=w&&(o&c)&&dp[c^o][w-a[i]]){
			w-=a[i];
			c^=o;
			stk[b].push(a[i]);
			break;
		}
	}
	for(;b;b--){
		while(!stk[b].empty()){
			int w=stk[b].top();
			stk[b].pop();
			while(w%k){
				printf("%d %d\n",w,stk[b].top());
				w+=stk[b].top();
				stk[b].pop();
			}
			int nw=b;
			while(!(w%k))w/=k,nw--;
			stk[nw].push(w);
		}
	}
	return 0;
}
```