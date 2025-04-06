# [Red-Blue Operations (Hard Version)](https://codeforces.com/contest/1832/problem/D2)
### 简要题意
给你一个长为 n 的序列，开始时每个元素都是红色，第$i$次操作你可以任选一个元素操作

1.如果该元素操作前是红色，该元素加上$i$并变成蓝色。
2.如果该元素操作前是蓝色，该元素减去$i$并变成红色。

m 次询问，每次询问一个 $k$ ，问 $k$ 次操作后最小值最大是多少。

### 问题分析
很显然的一点就是，一个数操作奇数次一定比原来大，操作偶数次一定小于等于原来的值。

当 $k \leq n $ 时，我们显然是要调处前 $k$ 小的数字操作。 

当 $k > n $ 时，我们就要对 $k-n$ 的奇偶性分类讨论
。
当 $k-n$ 为偶数时，我们最好结束情况就是所有的数字都是蓝色。那么先把数字从小到大分别加上$k,k-1,k-2,...,k-n+1$，然后对于剩下的$k-n$次操作，**每两次操作都可以当作是给任意一个数减一**，所以每次选最大的数减一即可。这些都可以通过简单的数学 $O(1)$ 实现。

当 $k-n$ 为奇数时，总有一个数不能比原来大，所以答案最大不能超过原序列的最大值，所以我们把最大值设为红色，其它设为蓝色，数字从小到大分别加上$k,k-1,k-2,...,k-n+1$。然后对于剩下的$k-n+1$次操作与上面类似。

$n=1$且$k$为偶数的情况需要小心数组越界。
##### 时间复杂度 O(n)

##### code
```
#include<bits/stdc++.h>
using namespace std;
const int N=2e5+5;
int n,m,a[N],k[N],mn[N];
long long s;
void slv(int k){
	if(k<=n){
		int rs=mn[k]+k;
		if(k<n)rs=min(rs,a[k+1]);
		printf("%d ",rs);
		return ;
	}
	if(n%2==k%2){
		int rs=mn[n]+k;
		int t=(k-n)/2;
		long long w=s+(k+k-n+1ll)*n/2-1ll*rs*n;
		if(w<t){
			t=(t-w-1)/n+1;
		}else t=0;
		printf("%d ",rs-t);
	}else {
		int rs=(n>1)? min(a[n],mn[n-1]+k):a[1];
		int t=(k-n+1)/2;
		long long w=s+(k+k-n+2ll)*(n-1)/2-1ll*rs*n;
		if(w<t){
			t=(t-w-1)/n+1;
		}else t=0;
		printf("%d ",rs-t);
	}
}
int main(){
	scanf("%d %d",&n,&m);
	for(int i=1;i<=n;++i){
		scanf("%d",&a[i]);
		s+=a[i];
	}
	sort(a+1,a+n+1);
	mn[1]=a[1];
	for(int i=2;i<=n;++i){
		mn[i]=min(mn[i-1],a[i]-i+1);
	}
	for(int i=1;i<=m;++i){
		scanf("%d",&k[i]);
	}
	for(int i=1;i<=m;++i)slv(k[i]);
	return 0;
}
```