# [Wish I Knew How to Sort](https://codeforces.com/contest/1753/problem/C)

### 简要题意：
一个长为 n 的 01 序列，一次操作随机选择两个数，如果位置靠前的数是 1，位置靠后的数是 0，则交换两个数，问使这个 01 序列变成升序序列期望需要多少次？
### 问题分析：
首先 1 的个数是确定的，设 1 的个数为 k ，那么对于最后 k 个位置，1 是不会被换到前面 n-k 个位置里。那么设$F_i$为达到最后 k 个位置里有 i 个 1 的情况的期望，我们就可以列出期望方程：

$$
F_i=\dfrac{(k-i)^2}{n*(n+1)/2}F_{i+1}+(1-\dfrac{(k-i)^2}{n*(n+1)/2})F_i+1
$$

算出 $F_k$即可。
通过预处理可以做到读入后直接输出答案。
时间复杂度： $O(n)$
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=2e5+5,M=998244353;
int T,n,a[N],s,s0,S[N],F[N];
int pw(int x,int t){
	int rs=1;
	for(;t;t>>=1,x=1ll*x*x%M)if(t&1)rs=1ll*rs*x%M;
	return rs;
}
void slv(){
	scanf("%d",&n);
	s=s0=0;
	for(int i=1;i<=n;++i){
		scanf("%d",&a[i]);
		s+=a[i];
	}
	for(int i=n;i>n-s;--i)s0+=1^a[i];
	printf("%lld\n",(1ll*n*(n-1)/2)%M*F[s0]%M);
}
int main(){
	int n=200000;
	S[0]=1;
	for(int i=1;i<=n;++i)S[i]=1ll*S[i-1]*i%M;
	F[n]=pw(S[n],M-2);
	for(int i=n;i;i--)F[i-1]=1ll*F[i]*i%M;
	F[0]=0;
	for(int i=1;i<=n;++i){
		F[i]=1ll*F[i]*S[i-1]%M;
		F[i]=(1ll*F[i]*F[i]+F[i-1])%M;
	}
	scanf("%d",&T);
	while(T--)slv();
	return 0;
}
```