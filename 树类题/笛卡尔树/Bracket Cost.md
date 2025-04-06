# [Bracket Cost](https://codeforces.com/contest/1750/problem/E)
### 简要题意：
一个长为 n 的括号序列，你可以进行两种操作：

1.选择一个子区间，里面的括号都向右移动一格，最后一个放到原来的第一个的位置。
2.在任意位置加入一个括号。

S 为一个括号序列通过操作变成合法括号序列的最小操作数，求所给长为 n 的括号序列的所有子区间的 S 的和。

### 问题分析：
通过手玩可以发现 1 操作的作用就是消去一对未匹配的左右括号。那么

S = max{未匹配的左括号数，未匹配的右括号数}

令左括号为 1，右括号为 -1，序列的前缀和为 $pre_i$。
假设我们在处理区间 [l,r]，那么

未匹配的左括号数 = $pre_r-\min\{pre_{l-1},pre_{l},...,pre_{r}\}$
未匹配的右括号数 =$pre_{l-1}-\min\{pre_{l-1},pre_{l},...,pre_{r}\}$
S= $max\{pre_{l-1},pre_r\}-\min\{pre_{l-1},pre_{l},...,pre_{r} $

考虑将两个部分分开计算，前面的贡献可以用桶计数的办法得到，后面的贡献也可以通过笛卡尔树算得。

时间复杂度: O(n)
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=4e5+5;
int T,n,s[N],ls[N],rs[N],q[N],ql,st[N],tp,cnt[N];
long long ans;
char c[N];
void clc(int l,int r,int x){
	ans-=s[x]*(1ll*(x-l+1)*(r-x+1)-1);
	if(~ls[x])clc(l,x-1,ls[x]);
	if(~rs[x])clc(x+1,r,rs[x]);
}
void slv(){
	scanf("%d %s",&n,c+1);
	ans=0;
	cnt[n]++;
	q[ql=1]=0;
	ls[0]=rs[0]=-1;
	for(int i=1;i<=n;++i){
		ls[i]=rs[i]=-1;
		if(c[i]=='(')s[i]=s[i-1]+1;
		else s[i]=s[i-1]-1;
		cnt[s[i]+n]++;
		while(ql&&s[q[ql]]>s[i])st[++tp]=q[ql--];
		for(int o=1;o<tp;++o)rs[st[o+1]]=st[o];
		if(tp)ls[i]=st[tp];
		tp=0;
		q[++ql]=i;
	}
	for(int i=1;i<ql;++i)rs[q[i]]=q[i+1];
	clc(0,n,q[1]);
	for(int i=n+n,s=n;~i;i--){
		while(cnt[i]){
			ans+=1ll*(i-n)*s;
			s--;
			cnt[i]--;
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