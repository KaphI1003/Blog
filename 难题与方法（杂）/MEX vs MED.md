# [MEX vs MED](https://codeforces.com/contest/1744/problem/F)

### 简要题意：
给一个长为 n 的排列 p，问有多少个子区间满足 mex > med。

### 问题分析：

mex的规律比较好找，所以从 mex 入手解决问题。

设 $[l_i,r_i]$ 为包含 0,1,2,3... i-1的最小区间，如果这个区间包含 i，跳转到下一个区间。否则根据 i 的位置分别算出$[l_i,r_i]$向左和向右分别最多能扩展多少。由于区间已经包含了 0,1,2,3... i-1 ，所以 最大合法区间的长度为 2i，通过分类讨论和数学计算就可以算出答案。
##### 时间复杂度 $O(n)$
##### code
```
#include<bits/stdc++.h>
using namespace std;
const int N=2e5+5;
int T,n,a[N],p[N];
void slv(){
	scanf("%d",&n);
	for(int i=1;i<=n;++i){
		scanf("%d",&a[i]);
		p[a[i]]=i;
	}
	long long rs=1;
	for(int i=1,l=n,r=0;i<n;++i){
		l=min(l,p[i-1]);
		r=max(r,p[i-1]);
		if(l<=p[i]&&p[i]<=r)continue;
		int lw=l,rw=n-r+1;
		if(p[i]<lw)lw=l-p[i];
		else rw=p[i]-r;
		int a=min(n,2*i)-(r-l);
		if(a<=0)continue;
		if(lw>rw)swap(lw,rw);
		if(lw<a){
			rs+=1ll*lw*(lw+1)/2;
			if(rw<a){
				rs+=1ll*(rw-lw)*lw;
				if(lw+rw>a){
					rs+=1ll*(a-rw)*(lw+lw-a+rw-1)/2;
				}else rs+=1ll*lw*(lw-1)/2;
			}else rs+=1ll*(a-lw)*lw;
		}else rs+=1ll*a*(a+1)/2;
	}
	printf("%lld\n",rs);
}
int main(){
	scanf("%d",&T);
	while(T--)slv();
	return 0;
}
```