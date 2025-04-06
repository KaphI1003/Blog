# Comet OJ-Contest #7 C

[题目链接](https://www.cometoj.com/contest/52/problem/C?problem_id=2416)
### 简要题面
你现在有一个长为$n$的排列$a$，和一个数列$p$。$p_i$表示$i$不能填入到$p_i$的位置。一个合法排列$a$产生的贡献是$(j-i)*(a_i-a_j)$，其中$j>i，a_i>a_j$。问所有合法情况的贡献和是多少。

$n \le 16$
### 问题分析
首先看到 n 小于等于 16 的数据范围，明显的要状压DP，但是由于还需要乘上两个数之间的距离，导致普通的状压DP无法完成这样的操作，就显得很难受了。这时，我们就要挖掘一些新的性质。

假设我们现在在选第i个位置的数字，那么***剩下的没选的的数一定要排在当前选的数的后面！***，根据这个性质我们就可以**把剩下的数的贡献先累积上**，实质就是将两个数间的距离分成一个个小块来算，这样每次转移时就把没选的所有的数的贡献加上，这个可以用$O(n)$扫描完成，问题解决。

时间复杂度$O(n·2^n)$

##### code:

```
#include<bits/stdc++.h>
using namespace std;
int n,T,a[20],x,rs,s,z,g,k;
long long dp[65536],w[65536];
void mian(){
	scanf("%d",&n);
	for(int i=1;i<=n;++i){
		scanf("%d",&a[i]);
	}
	k=(1<<n)-1;
	memset(dp,0,sizeof(dp));
	memset(w,0,sizeof(w));
	dp[0]=1;
	for(int i=0;i<=k;++i){
		if(!dp[i])continue;
		x=i,rs=s=z=g=0;
		while(x){x-=x&-x;rs++;}
		for(int o=n,j=1<<(n-1);o;--o,j>>=1){
			if(j&i)s++,z+=o;
			else g+=z-s*o;
		}
		for(int o=1,j=1;o<=n;++o,j<<=1){
			if(!(j&i)&&a[o]!=rs+1){
				w[i^j]+=dp[i]*g+w[i];
				dp[i^j]+=dp[i];
			}
		}
	}
	printf("%lld\n",w[k]);
}
int main(){
	scanf("%d",&T);
	while(T--)mian();
}
```

updata:

许久没看看不懂自己写的东西了，所以重新写一篇吧。。。

我们从小到大往里面填数，每次填入一个数 $i$，它会和位置在它后面已经填了的数和位置在它前面还没有填的数产生贡献。我们把贡献拆开得到$|i-j|*|pos_i-pos_j|=i*(pos_j-pos_i)+j*(pos_i-pos_j)$。我们填入$i$时只考虑$i$和$pos$之间产生的贡献。

设$i$后面有$cnt_1$个位置填了数，$cnt_1$个填了数的$pos$的和为$sum_1$。$i$前面有$cnt_2$个位置没有填数，$cnt_2$个没填数的$pos$的和为$sum_2$。那么$i$产生的贡献就是$i*(sum_1+sum_2-pos_i*(cnt_1+cnt_2))$。

于是我们枚举并计算各种情况的数量并算出贡献即可得到答案。

##### 时间复杂度 $O(n2^n)$
##### code
```
#include<bits/stdc++.h>
typedef  long long LL;
using namespace std;
const int N=1e5+5;
int n,T,p[55];
long long cnt1[N],cnt2[N],ans;
void slv(){
	scanf("%d",&n);
	for(int i=0;i<n;++i){
		scanf("%d",&p[i]);
		p[i]--;
	}
	memset(cnt1,0,sizeof(cnt1));
	memset(cnt2,0,sizeof(cnt2));
	ans=0;
	cnt1[0]=cnt2[0]=1;
	int m=1<<n;
	for(int i=1;i<m;++i){
		int s=-1,t=i;
		while(t)s++,t&=(t-1);
		t=n-1-s;
		for(int j=0,k=1;j<n;++j,k<<=1)if(k&i){
			if(p[s]!=j)cnt1[i]+=cnt1[i^k];
			if(p[t]!=j)cnt2[i]+=cnt2[i^k];
		}
	}
	for(int i=1;i<m;++i){
		int s=-1,t=i;
		while(t)s++,t&=(t-1);
		int s1=0,c1=0,s2=0,c2=0;
		for(int j=0,k=1;j<n;++j,k<<=1)if(k&i)s1+=j,c1++;
		for(int j=0,k=1;j<n;++j,k<<=1){
			if(k&i){
				s1-=j,c1--;
				if(p[s]!=j)ans+=cnt1[i^k]*cnt2[(m-1)^i]*(s+1)*(s2+s1-j*(c1+c2));
			}else {
				s2+=j,c2++;
			}
		}
	}
	printf("%lld\n",ans);
}
int main(){
	scanf("%d",&T);
	while(T--)slv();
	return 0;
}
```