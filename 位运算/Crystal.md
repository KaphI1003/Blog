# Crystal

[题目链接](http://10.220.121.203/judge/problem.php?id=5442\&csrf=Qf3SjcIO2fY0U2ZANALBV6eRuoz2Y78J)

### 题目描述

给定n个数A1,A2,⋯,An

问多少组X1,⋯,Xn满足

(1)0≤Xi≤Ai

(2)X1⊕X2⊕⋯⊕Xn=0

(3)X1+X2+⋯+Xn>0

##### 输入

第一行一个整数n，表示A数组的长度

接下来n个整数，表示Ai

2≤n≤50,1≤Ai≤2^32−1

##### 输出

输出方案数

##### 样例输入

3

2 1 3

##### 样例输出

5

### 分析：

题目中给出的要求是让所有数异或和为零。由于位运算的每一位都互不干涉，所以我们可以按位数从高到低来处理。

假设我们现在的数字是这样的：

$$
11001  \\
$$
$$
10110  \\
$$
$$
01001
$$

最高位上有两个 1，如果我们要满足题目的限制，我们只有全为0或两个1，一个0这两个选项。如果选后面的，就直接进入下一层；如果选前面的，那么第一个数和第二个数剩下的位数就可以随便填（类比数位DP），那我们就称之为魔力数（滑稽），这时我们就可以直接计算当前情况对答案的贡献了。**因为魔力数可以把任意一个数异或为零，所以我们只需要计算把一个魔力数去掉后剩下的所有组合的个数**。

时间复杂度：$O(n^2log(int_{max}))$

#### code：

```cpp
#include<bits/stdc++.h>
using namespace std;
int n,s;
unsigned long long a[55],b[55],dp[55][55],ans,v,g,k;
bool fl;
int main(){
	scanf("%d",&n);
	for(int i=1;i<=n;++i){
		scanf("%llu",&a[i]);
		dp[0][i+1]=1;
	}
	for(int i=31;i>=0;i--){
		k=1ll<<i,s=0;
		v=g=1;
		for(int o=1;o<=n;++o){
			if((a[o]>>i)>0){
				a[o]^=k;
				b[++s]=a[o]+1;
			}
			else v*=a[o]+1;
		}
		if(!s)continue;
		for(int o=1;o<s;++o)g*=k;
		ans+=v*g;
		if(s&1)fl=1;
		for(int u=1;u<=s;++u){
			g/=k;
			dp[u][s-u+2]=0;
			for(int o=s-u+1;o;--o){
				dp[u][o]=dp[u-1][o+1]*b[o];
				dp[u][o]+=dp[u][o+1];
			}
			if(!(u&1)){
				ans+=v*g*dp[u][1];
			}
		}
		if(fl)break;
	}
	printf("%llu",ans-1);
}
```

