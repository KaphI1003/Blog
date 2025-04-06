# [Old Mobile](https://codeforces.com/contest/1835/problem/E)

### 简要题面

你现在有一个密码机，上面有$m+1$个键，其中$m$个是不同的字符键，$1$个是退格键。由于老化你现在看不清键帽上面的符号，所以你只能够按下这个键输入到屏幕上才能知道按下的按键是什么。你现在需要输入一串长度为$n$的密码。问成功输入密码需要按下键的期望次数是多少？

$n\le 10^6 , m\le 1000$

### 问题分析

一个正确的按钮找到后，再次按下的代价就是$1$，所以我们将密码简化，只保留每个数字第一次出现的位置，其余的直接按答案加一处理，这样我们就将密码长度降到$O(m)$，其次由于每个按键之间完全相同，所以我们只要知道当前不知道的按键有几个就可以了。

设我们现在还有$i$个在密码中出现的字符没试出来，$j$个没在密码中出现的字母没有试出来，我们再把 DP 状态分成三种，分别是试出了退格键(0)，前缀是正确密码的(1)，前缀不是正确密码(2)。那么我们就可以列出 DP 方程：

$$
dp[i][j][0]= 
\left\{
\begin{aligned}
& \frac{1}{i+j} (1+dp[i-1][j][0]) \\
& \frac{i-1}{i+j} (3+dp[i-1][j][0]) \\
& \frac{j}{i+j} (2+dp[i][j-1][0]) \\
\end{aligned}
\right.
$$

$$
dp[i][j][1]= 
\left\{
\begin{aligned}
& \frac{1}{i+j+1} (1+dp[i-1][j][1]) \\
& \frac{i-1}{i+j+1} (3+dp[i-1][j][2]) \\
& \frac{j}{i+j+1} (2+dp[i][j-1][2]) \\
& \frac{1}{i+j+1} (2+dp[i][j][0]) \\
\end{aligned}
\right.
$$

$$
dp[i][j][2]= 
\left\{
\begin{aligned}
& \frac{i}{i+j+1} (3+dp[i-1][j][2]) \\
& \frac{j}{i+j+1} (2+dp[i][j-1][2]) \\
& \frac{1}{i+j+1} (dp[i][j][0]) \\
\end{aligned}
\right.
$$

在 DP 时，输出正确字符到正确位置花费 1 ，正确字符到错误位置花费 3 ，错误字符花费 2 。除了 $dp[i][j][2]$ 的第 4 条比较特殊，我们用退格删掉了正确的字符，所以又重新输入回来花费 2。

最后我们是从一个空数列开始输入密码的，所以第一次按下回车时我们是不需要重新输入的，故设我们需要的字符有$s$个，不需要的就有$t=m-s$个，答案就是

$$
ans=  n-s+
\left\{
\begin{aligned}
& \frac{1}{i+j+1} (1+dp[i-1][j][1]) \\
& \frac{i-1}{i+j+1} (3+dp[i-1][j][2]) \\
& \frac{j}{i+j+1} (2+dp[i][j-1][2]) \\
& \frac{1}{i+j+1} (1+dp[i][j][0]) \\
\end{aligned}
\right.
$$

##### 时间复杂度 $O(m^2)$

##### code

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=1005,M=1e9+7;
int n,m,dp[N][N][3],inv[N],v[N],s,t,ans;
int pw(int x,int t){
	int rs=1;
	for(;t;t>>=1,x=1ll*x*x%M)if(t&1)rs=1ll*rs*x%M;
	return rs;
}
int main(){
	scanf("%d %d",&n,&m);
	for(int i=1;i<=n;++i){
		int x;
		scanf("%d",&x);
		if(!v[x]){
			v[x]=1;
			s++;
		}else ans++;
	}
	t=m-s;
	for(int i=1;i<=m+1;++i)inv[i]=pw(i,M-2);
	for(int i=0;i<=s;++i){
		for(int j=0;j<=t;++j){
			if(i){
				dp[i][j][0]=(1ll*i*dp[i-1][j][0]+1ll*j*dp[i][j-1][0]+3*i-2+2*j)%M*inv[i+j]%M;
				dp[i][j][1]=(1ll*(i-1)*dp[i-1][j][2]+1ll*j*dp[i][j-1][2]+dp[i-1][j][1]+dp[i][j][0]+3*i+2*j)%M*inv[i+j+1]%M;
			}
			dp[i][j][2]=(1ll*i*dp[i-1][j][2]+1ll*j*dp[i][j-1][2]+dp[i][j][0]+3*i+2*j)%M*inv[i+j+1]%M;
		}
	}
	int rs=((1ll*(s-1)*dp[s-1][t][2]+1ll*t*dp[s][t-1][2]+dp[s-1][t][1]+dp[s][t][0]+3*s+2*t-1)%M*inv[s+t+1]+ans)%M;
	printf("%d\n",rs);
	return 0;
}
```

