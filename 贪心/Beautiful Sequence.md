# [Beautiful Sequence](https://codeforces.com/gym/104427/problem/F)

### 简要题面

给你一个长度为$n$的序列$a$，如果$a_i$是好的当且仅当$a_{i-1}\le a_i \ge a_{i+1}$。特殊的，$a_0=a_{n+1}=-\inf$。问重排$a$后，最多能有多少个好的数。

### 问题分析

我们把好的数当作峰，把坏的数当作谷，那么我们就需要让谷的数量越少越好。考虑终局情况，我们设选出来的谷底是一个可重集$V$，剩下的数的不可重集是$U$,$V$中第$i$小的数是$v_i$，$U$中第$i$小的数是$u_i$。那么我们需要满足

1.  $|U| \le |V|+1$
2.  $v_i \le u_i，i \le |U|$

我们让$U,V$从空开始填充，直到满足条件时停止。我们改变 $U$的形式，让其存储一个同时保存出现次数和值的集合。在$U$中，我们保存当前可以填入到峰的（即在集合$V$外的）最小的数和它们的出现次数，且让$|U|$始终保持是$|V|+1$。我们要向$V$中填充数时，我们只能从$U$中选出单独一个数填入$V$中（不从中选的话会导致新的谷比前面最后一个峰大），然后更新$U$让$|U|$保持是$|V|+1$（除非没有数可以插入了）。当$V$外的数的种类数小于等于$|V|+1$时，就可以终止输出答案了。

但是我们该从$U$中选什么数插入到$V$中呢？为了让当$V$外的数的种类尽可能少，每次应当选出现次数最少的数插入到$V$中，这可以用优先队列实现。

##### 时间复杂度 $O(n \log n)$

##### code

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=3e5+5;
int T,n,a[N],b[N],c[N],bl;
struct P{
	int w,c;
	bool operator < (const P&t)const{
		return c>t.c;
	}
};
priority_queue<P>Q;
void slv(){
	while(!Q.empty())Q.pop(); 
	scanf("%d",&n);
	for(int i=1;i<=n;++i){
		scanf("%d",&a[i]);
	}
	sort(a+1,a+n+1);
	bl=0;
	for(int i=1;i<=n;++i){
		if(a[i]!=a[i-1]){
			b[++bl]=a[i];
			c[bl]=0;
		}
		c[bl]++;
	}
	int nw=1,knd=bl,rs=0;
	while(knd>rs+1){
		rs++;
		if(nw<=bl){
			Q.push((P){b[nw],c[nw]});
			nw++;
		}
		P t=Q.top();
		Q.pop();
		if(t.c==1){
			knd--;
			if(nw<=bl){
				Q.push((P){b[nw],c[nw]});
				nw++;
			}
		}else Q.push((P){t.w,t.c-1});
	}
	printf("%d\n",n-rs);
}
int main(){
	scanf("%d",&T);
	while(T--)slv();
	return 0;
}
```

