# Function
### 简要题面
有一个函数满足：
$$
f(x)=\left\{
\begin{aligned}
& 0 & x>n  \\
& 1+\sum_{k=2}^{20210926}f(kx) & x \le n \\
\end{aligned}
\right.
$$
求$f(1)$。

$n \le 10^9$

### 问题分析

我们设
$$
g(x)=\left\{
\begin{aligned}
& 0 & x>n  \\
& 1+\sum_{k=2}^{20210926}g(\lfloor x/k \rfloor) & x \le n \\
\end{aligned}
\right.
$$

我们可发现 $f(x)=g(\lfloor n/x \rfloor)$。

**归纳法证明：**
显然$f(n)=g(1)=1$成立。

我们设当$t<i\le n$时,$f(i)=g(\lfloor n/i \rfloor)$恒成立。那么：
$$
f(t)=1+\sum_{t=2}^{20210926}f(kt)=1+\sum_{k=2}^{20210926}g(\lfloor n/(kt) \rfloor)=1+\sum_{k=2}^{20210926}g(\frac{ \lfloor  n/t\rfloor} {k} )=g(\lfloor n/t \rfloor)
$$
故归纳证毕。

计算$g(x)$，我们直接数论分块加记忆化即可。
##### code
```
#include<bits/stdc++.h>
using namespace std;
const int M=998244353;
int n,w[1000005];
map<int,int>mp;
int min(int x,int y){return (x<y)? x:y;}
int f(int x){
	if(x<=3)return x;
	if(x<=1000000&&w[x])return w[x];
	if(mp.find(x)!=mp.end())return mp[x];
	int rs=1;
	for(int l=2,r;l<=min(x,20210926);l=r+1){
		r=min(x/(x/l),20210926);
		rs=(rs+1ll*(r-l+1)*f(x/l))%M;
	}
	return (x<=1000000)? (w[x]=rs):(mp[x]=rs);
}
int main(){
	scanf("%d",&n);
	printf("%d\n",f(n));
	return 0;
}
```