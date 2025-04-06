# [The Number of Subpermutations ](https://codeforces.com/contest/1175/problem/F)


### 题意：
给定一个长度为n的序列{a}，求有多少个子区间满足区间内 1~区间长度中的每一个数字都恰出现一次。
#### 范围
a[i]<=n;
n<=3e5;

### 分析：
首先每一个满足的区间一定都包含一个1，所以我们可以，以相邻的1为界，从每一个1向外扩展，这样我们就可以得到一个$O(n^2)$的算法。接下来就是要对算法进行优化。

假设一个区间的最大值在一的左侧，那么我们就可以分析出右端的最大值不可以超过其，并且区间长度就需要等于最大值，所以我们就可以通过回撤尺取来解决。左右区间的最值都可以O(L)求得，从左到右枚举左端点，并保证没有重复（方式见代码）。然后再移动右端点，判断是否符合要求（因为ans的大小并不大）。最大值在右区间同理。

#### 关于移动右端点：
首先可以得知最大值一定是递减的，最大变化L。而枚举左区间时右端点跟着移动，最多也只变化L，所以一次的时间复杂度就是$O(L)$

时间复杂度：$O(n)$
##### code:

```
#include<bits/stdc++.h>
using namespace std;
const int N=3e5+5;
int n,ans,ma[N],b[N],a[N],l,v[N],t;
void sv(){
	for(int i=1;i<=l;++i){
		int z=b[i-1]+1,w=b[i],y=b[i+1]-1,L,R=w;
		ma[w]=1;
		v[1]=++t;
		for(L=w-1;L>=z;--L){
			if(v[a[L]]!=t)v[a[L]]=t;
			else break;
			ma[L]=max(a[L],ma[L+1]);
		}L++;
		for(int o=w+1;o<=y;++o)ma[o]=max(a[o],ma[o-1]);
		for(;L<w;++L){
			while(v[a[R+1]]!=t&&ma[R+1]<ma[L]&&R-L+1<ma[L]){
				R++;v[a[R]]=t;
			}
			while(R-L+1>ma[L]&&R!=w){
				v[a[R]]=0;R--;
			}//R总共最多变换2*区间长度次 
			if(ma[L]>ma[R]&&R-L+1==ma[L])ans++;
			v[a[L]]=0;
		}
	}
}
int main(){
	scanf("%d",&n);
	for(int i=1;i<=n;++i){
		scanf("%d",&a[i]);
		if(a[i]==1)b[++l]=i;
	}
	b[l+1]=n+1;a[n+1]=a[0]=1;
	ans+=l;
	sv();l=0;
	for(int i=n>>1;i;--i)swap(a[i],a[n-i+1]);
	for(int i=1;i<=n;++i)if(a[i]==1)b[++l]=i;
	sv();
	printf("%d\n",ans);
}
```
