# [Lockout vs tourist](https://codeforces.com/gym/103260/problem/B)
### 简要题面
现在有 $n$ 个题目等待解决，每个题目都有一个分值$a_i$，但只有第一个通过该题的人才能得到分数。你现在要和一个比你强的对手竞争。每一轮你和你的对手都会选择一个题目做，如果题目相同，你的对手会比你快解决题目得到分数；如果不同，你可以得到你做的题目的分数，但是你的对手会暴怒的将剩下的题目全部通过，所以你最多只能得到一道题目的分数。你希望你的分数尽量高，而你的对手希望你的分数尽量低。求你的期望分数。

$n \le 22$

### 问题分析
这个一看就是纳什均衡 + 状压DP。
考虑状态如何转移，设此时可选的题目分值分别为$x_i$，和对手冲突的期望分数为$y_i$，冲突的概率为$q_i$。那么此时选这道题的期望得分为$x_i+q_i·(y_i-x_i)=k$。$k$为时纳什均衡值。又由$\sum q_i = 1$求解得到$k=\dfrac{\sum \frac{x_i}{x_i-y_i}-1}{\frac{1}{x_i-y_i}}$。

但是这样可能会出现 $q_i<0$的非法情况，所以我们要对选的数进行筛选。易得当$x_i>x_j$时，$y_i<y_j$。设当前的纳什均衡值为$k$，那么我们加入$x_i$大于$k$的题目可以提升纳什均衡值，同样的加入$x_i$小于$k$的题目会降低纳什均衡值。所以我们一定是按$x_i$从大到小加入题目。如果下一道题目的$x_i$小于当前纳什均衡值就退出。但是如果遇到$x_i \le y_i$的情况那么加入它会可能导致$q_i < 0$，不过此时可以直接贪心得到纳什均衡值就是$x_i$。

##### 时间复杂度 $O(n·2^n)$
##### code
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=23;
int n,a[N];
double dp[1<<N],x[N],y[N];
int main(){
	scanf("%d",&n);
	for(int i=0;i<n;++i)scanf("%d",&a[i]);
	sort(a,a+n);
	int k=1<<n;
	for(int i=1;i<k;++i){
		int xl=0;
		for(int j=n-1;~j;j--)if((i>>j)&1){
			x[++xl]=a[j];
			y[xl]=dp[i^(1<<j)];
		}
		double s0=0,s1=0,k=0;
		for(int j=1;j<=xl;++j){
			if(x[j]<=y[j]){
				dp[i]=x[j];
				break;
			}
			s0+=x[j]/(x[j]-y[j]);
			s1+=1/(x[j]-y[j]);
			k=(s0-1)/s1;
			if(k>=x[j+1]||j==xl){
				dp[i]=k;
				break;
			}
		}
	}
	printf("%.13lf\n",dp[k-1]);
	return 0;
}
```