# [Lottery](https://codeforces.com/contest/1835/problem/B)
### 简要题面
有 $n$ 个人参加比赛争夺 $k$ 个奖，每个人有一个 $0$到$m$ 的数字$a_i$，比赛结束时会公布一个数字$x$，与$x$相差最小的$k$个人获胜，相同差值的情况下编号小的人获胜。

而你作为第 $n+1$ 个参赛选手插入比赛，你的编号最大所以相同差值的情况下你总是输，但你可以随意选择一个数字$y$作为你的数字，你想知道你可以获奖的最大机率和此时的最小数字$y$。

### 问题分析
把每个人的数字映射到数轴上，可以获奖的人显然是一段区间，假设我们现在选的数字是$p$，从$p$往前数$k$个人，他的数字是$a$，从$p$往后数$k$个人，他的数字是$b$，那么当$x \in (\frac{b+p}{2},\frac{a+p}{2})$时，我们可以获奖，其它情况则不行。

如果后面没够$k$个人，那么合法的区间就是$[0,\frac{a+p}{2})$，如果前面没够$k$个人，那么合法的区间就是$(\frac{b+p}{2},m]$。

当我们选的数字$p$上面本来就有人的话，因为我们编号最大的原因，在往前和往后数的过程中我们都要这些人考虑进去。

因为$m$范围$10^{18}$的原因，我们不可能把所有的位置都枚举算一遍。但是我们观察式子$(\frac{b+p}{2},\frac{a+p}{2})$，当p增减一个偶数时，区间的长度是不会发生变化的，所以我们只需要考虑$a_i,a_i+1,a_i+2$即可。

而对于式子$[0,\frac{a+p}{2})$，答案显然就会随着$p$的增长而变大，所以我们还要**额外考虑**$a_k-1,a_k-2$的位置。（比赛的时候最后就是这点没有注意到，艹）。

##### 时间复杂度 O(n)
##### code 
```
#include<bits/stdc++.h>
using namespace std;
const int N=1e6+5;
int n,k;
long long m,a[N];
void Upd(long long &mx,long long &rs,long long w,long long p){
	if(w>mx)mx=w,rs=p;
	else if(w==mx)rs=min(rs,p);
}
int main(){
	scanf("%d %lld %d",&n,&m,&k);
	for(int i=1;i<=n;++i)scanf("%lld",&a[i]);
	sort(a+1,a+n+1);
	if(k>n){
		printf("%lld %d\n",m+1,0);
		return 0;
	}
	a[0]=a[n+1]=-1;
	long long mx=0,rs=0;
	if(a[1]!=0){
		mx=(a[1]-1+a[k]-1)/2+1;
		if(a[1]>1&&(a[1]-2+a[k]-1)/2+1==mx)rs=a[1]-2;
		else rs=a[1]-1;
	}
	if(a[n]<m){
		long long w=m-(a[n]+1+a[n-k+1])/2;
		Upd(mx,rs,w,a[n]+1);
		if(a[n]<m-1){
			w=m-(a[n]+2+a[n-k+1])/2;
			Upd(mx,rs,w,a[n]+2);
		}
	}
	for(int i=1,tl=1,tr=1;i<=n;++i){
		while(tr<n&&a[tr+1]==a[tl])tr++;
		if(tr-tl+1<k){
			long long p=a[i];
			long long l=(tr-k+1<1)? 0:((p+a[tr-k+1])/2+1);
			long long r=(tl+k-1>n)? m:(p+a[tl+k-1]-1)/2;
			long long w=r-l+1; 
			Upd(mx,rs,w,p);
		}
		if(a[i]!=a[i+1])tl=i+1;
		if(i<n&&a[i]<a[i+1]-1){
			long long p=a[i]+1;
			long long l=(i-k+1<1)? 0:((p+a[i-k+1])/2+1);
			long long r=(i+k>n)? m:(p+a[i+k]-1)/2;
			long long w=r-l+1;
			Upd(mx,rs,w,p);
			
			p=a[i+1]-1;
			l=(i-k+1<1)? 0:((p+a[i-k+1])/2+1);
			r=(i+k>n)? m:(p+a[i+k]-1)/2;
			w=r-l+1;
			Upd(mx,rs,w,p);
			if(a[i]<a[i+1]-2){
				p=a[i]+2;
				l=(i-k+1<1)? 0:((p+a[i-k+1])/2+1);
				r=(i+k>n)? m:(p+a[i+k]-1)/2;
				w=r-l+1; 
				Upd(mx,rs,w,p);
				
				p=a[i+1]-2;
				l=(i-k+1<1)? 0:((p+a[i-k+1])/2+1);
				r=(i+k>n)? m:(p+a[i+k]-1)/2;
				w=r-l+1;
				Upd(mx,rs,w,p);
			}
		}
	}
	printf("%lld %lld\n",mx,rs);
	return 0;
}
```