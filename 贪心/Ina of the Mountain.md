# [Ina of the Mountain](https://codeforces.com/contest/1852/problem/C)

### 简要题面
给你一个长为 $n$的序列$a$，每次你可以选择一个区间$[l,r]$让$a_i$变成$1+(a_i-2)\mod k$，问最少要多少次才能把所有数字全部变成 $k$。

$n \le 2·10^5，k\le 10^9$

### 问题分析
先做差分，那么操作就变成了每次选一对 $1\le l \le r \le n$，使$a_l--，a_{r+1}++$。那么我们就是要确定每个数是该减到 $0$ 还是加到 $k$。下标从小到大考虑，我们要保证每一时刻减的总次数都不小于加总次数，设 $cnt_i=(cnt-)_i-(cnt+)_i$，那么就是要让$cnt_i \ge 0$恒成立，答案就是$(cnt-)_n$，我们需要让其最小，我们先去掉所有的 $0$，让所有的数做减法，这样如果一个数从减法改成了加法，那么就会使$cnt_i -= k$，所以我们每次记录下最小的$p_i$ $cnt_i \ge ik$，这样我们的我们就得到的第 $i$个变成加的数可以选的区间，倒过来优先队列做一遍就可以了。
##### 时间复杂度 $O(n \log n)$
##### code
```
#include<bits/stdc++.h>
using namespace std;
const int N=2e5+5;
int n,k,T,a[N],al,l[N];
priority_queue<int>Q;
void slv(){
	while(!Q.empty())Q.pop();
	al=0;
	scanf("%d %d",&n,&k);
	for(int i=1;i<=n;++i){
		scanf("%d",&a[i]);
	}
	for(int i=n;i;i--)a[i]=(a[i]-a[i-1]+k)%k;
	for(int i=1;i<=n;++i)if(a[i])a[++al]=a[i];
	n=al;
	int ll=0;
	long long s=0;
	for(int i=1;i<=n;++i){
		s+=a[i];
		if(s>=(ll+1ll)*k)l[++ll]=i;
	}
	for(int i=ll,nw=n;i;i--){
		while(nw>=l[i]){
			Q.push(a[nw--]);
		}
		int x=Q.top();
		s-=x;
		Q.pop();
	}
	printf("%lld\n",s);
}
int main(){
	scanf("%d",&T);
	while(T--)slv();
}
```