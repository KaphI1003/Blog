# [Twin Clusters](https://codeforces.com/contest/1835/problem/C)

### 简要题面
给你一个整数$k$，再给你一个长$2^{k+1}$的序列，每个数在$[0,4^k)$的范围内，请你找出两个**不相交**区间使这两个区间的异或和相同。

$1 \le k \le 17$

### 问题分析
本题解法类似基数排序和抽屉原理，首先我们考虑后$k$位，求取前缀我们得到了$4^k+1$个前缀，而前缀的值域大小只有$2^k$,所以我们至少可以得到$2^k+1$个区间，使它们的后$k$位异或和为 0 。

而现在对这$2^k+1$个前缀考虑前$k$位，那么至少存在一对区间满足他们两个区间的异或和的异或为 $0$ 。那么这对区间就是我们想要的答案，但是这两个区间不一定不相交，不过我们把公共的部分去掉就可以了，在代码实现的过程中通过更新赋值可以避免去掉公共部分后区间为空的情况。

##### 时间复杂度 $O(2^k)$
##### code 
```
#include<bits/stdc++.h>
using namespace std;
const int N=1e6+5;
int T,k,n,m,h[N],l[N],r[N];
long long a[N],s[N];
void slv(){
	scanf("%d",&k);
	n=1<<(k+1);
	m=n>>1;
	for(int i=0;i<m;++i)h[i]=l[i]=r[i]=-1;
	h[0]=0;
	for(int i=1;i<=n;++i){
		scanf("%lld",&a[i]);
		s[i]=s[i-1]^a[i];
	}
	for(int i=1,pre=0;i<=n;++i){
		pre^=a[i]&(m-1);
		if(h[pre]!=-1){
			int x=(s[i]^s[h[pre]])>>k;
			if(l[x]!=-1){
				int l1=h[pre]+1,r1=i,l2=l[x],r2=r[x];
				if(l1<=r2){
					if(l1<l2){
						int t=r2;
						r2=l2-1;
						l2=l1;
						l1=t+1;
					}else {
						swap(l1,r2);
						r2--,l1++;
					}
				}
				
				printf("%d %d %d %d\n",l2,r2,l1,r1);
				return;
			}else l[x]=h[pre]+1,r[x]=i;
		}
		h[pre]=i;
	}
	
}
int main(){
	scanf("%d",&T);
	while(T--)slv();
	return 0;
}
```