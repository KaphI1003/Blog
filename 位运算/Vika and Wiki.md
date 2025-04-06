# [Vika and Wiki](https://codeforces.com/contest/1848/problem/F)

### 简要题面

一个长$2^k$的序列$a$（下标从$0$开始），$n=2^k$。每一次操作会同时让所有的数字$a_i$变成$ a_i \oplus a_{(i+1) \mod n}$。问要多少次操作才能将$a$ 全部变成$0$。

$k \le 20$

### 问题分析

通过手动模拟几次，不难发现经过$t$次操作后，$a_i$ 会变成 $\oplus a_{(i+x) \mod n}$，其中$x$是所有满足$ x|t=t $ 的数。通过这个性质，我们可以知道操作$2^k$次后，序列一定会归零。

当序列已经变成全零时，继续操作仍然会得到一个全零序列。所以我们就可以通过二分的办法寻找答案。当操作次数正好是$2^p$时，所有的$a_i$都变成了$a_i \oplus a_{(i+2^p)\mod n}$。这样我们就可以直接枚举判断$a_i$是否全部为$0$，如果是，那么答案$ans \le 2^p$，继续处理$2^{p-1}$；否则我们就要处理$2^p+2^{p-1}$，直接处理比较困难，我们让$a$先操作$2^p$次，也就是让$a_i: a_i \oplus a_{(i+2^p) \mod n}$，那么就可以同样归入$2^{p-1}$次处理了。

注意到每次操作完不论两种情况中的哪一种都有$a_i = a_{(i+2^p) \mod n}$，所以我们每次只要处理上次的一半的区间即可。

##### 时间复杂度$O(n)$

##### code

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=2e6+5;
int n,rs,a[N];
int main(){
	scanf("%d",&n);
	for(int i=0;i<n;++i){
		scanf("%d",&a[i]);
	}
	while(n>1){
		int k=n>>1;
		bool fl=1;
		for(int i=0;i<k;++i)if(a[i]!=a[i+k]){
			fl=0;
			break;
		}
		if(!fl){
			rs+=k;
			for(int i=0;i<k;++i)a[i]^=a[i+k];
		}
		n>>=1;
	}
	if(a[0])rs++;
	printf("%d\n",rs);
}
```

