# [Survey](https://qoj.ac/contest/824/problem/2607)
### 简要题意
你有 $m$ 元钱，要雇佣 $n$ 个外包工作，但第 $i$ 个外包只有在收到不少于 $x_i$ 的工资才会认真工作。发工资的方式是将 $m$ 元有你随意分成 $n$ 份（可以分 $0$ 元），然后每个人均匀概率得到其中一份。现在你希望让认真工作的人数的期望最大，问最大期望为多少。

$1 \le n \le 1000$，$1 \le m \le 5000$，$0 \le x_i \le m$

### 问题分析
如果分出 $y$ 元，如果有 $k$ 个人的 $x$ 不大于 $w$ ，我们就可以确定 $y$ 的期望收益为 $\frac{k}{n}$ 。用这个办法直接算出 $0$ 到 $m$ 元的所有数的期望收益 $v_i$。

那么这就可以直接转化成 DP ，背包的物品上限为 $n$ ，重量上限为 $m$ 。有 $m$ 个物品，第 $i$ 个物品重 $i$ ，价值为 $v_i$ ，求最大价值。

直接 DP 是 $O(nm^2)$ 的，考虑优化。通过观察可以发现，我们规定在放入第 $i$ 个物品时，放入物品的重量不能超过 $\frac{m}{i}$ ，这样 DP 也一样不会漏情况（因为重 $w$ 的物品最多放入 $\frac{m}{w}$ 个）。这样时间复杂度就来到了 $O(nm \log m)$ 。

##### code：
```cpp
#include<bits/stdc++.h>
#define double long double 
using namespace std;
const int N=5005;
const double eps=1e-15;
int n,m,a[N],cnt[N];
double w[N],dp[N];
int ck(double k){
	for(int i=0;i<=m;++i)dp[i]=cnt[i]=0;
	for(int i=1;i<=n;++i){
		for(int j=a[i];j<=m;++j){
			double v=dp[j-a[i]]+w[a[i]]-k;
			if(v>=dp[j])dp[j]=v,cnt[j]=cnt[j-a[i]]+1;
		}
	}
	return cnt[m];
}
int main(){
	scanf("%d %d",&n,&m);
	for(int i=1;i<=n;++i)scanf("%d",&a[i]);
	sort(a+1,a+n+1);
	for(int i=1;i<=n;++i){
		w[a[i]]=1.0*i/n;
	}
	double l=w[0],r=1.01,ans=1e9;
	while(r-l>eps){
		double mid=l+r;
		mid/=2;
		int g=ck(mid);
		if(g>n){
			l=mid;
		}else if(g<=n){
			ans=min(ans,dp[m]+n*mid);
			r=mid;
		}
	}
	printf("%.12Lf\n",ans);
}
```

