# [Moving Boxes](https://codeforces.com/gym/104396/problem/G)

### 简要题面

数轴上有$n$个货物，每个货物都有它开始所在的位置$x_i$和它要送到的位置$y_i$（$x_i \ne y_i$），有一个机器人可以用来搬运货物，机器人可以从任意一个位置开始搬运货物，搬起货物和放下货物不需要时间，但机器人每前进一个单位距离花费一秒，转身花费$C$秒，搬运完所有货物后机器人需要回到初始位置而且朝向要和开始时相同，问机器人最少需要多少时间搬运完所有货物。

$1 \le n \le 10^5,1 \le C,x_i,y_i \le 10^9$

### 问题分析

先离散化分成许多小段，前缀和算出每一小段有多少货物从左到右走，有多少货物要从右到左走，记为$a_i,b_i$，由于最后一定要回到原点，我们可以知道每一段从左到右走和从右到左走的次数是一样的，设一共要往返这一小段$w_i$次，那么$w_i \ge \max(a_i,b_i,1)$。

如果我们设转向不花费时间，那么最优情况下，$w_i = \max(a_i,b_i,1)$，转向次数$cnt=\sum_{i=0}^n |w_i-w_{i+1}|,\ w_0=w_{n+1}=0$

再考虑用路程换转向次数的优劣，考虑段 $i-1,i,i+1$，满足$w_{i-1}>w_{i}<w_{i+1}$，中间的第$i$段往返走一次，就可以减少两次转向次数。这样的转换最多$\min(w_{i-1},w_{i+1})-w_i$次。

那么我们对可以优化的段全部转换就可以得到最优答案。但是通常一个段的两个边界不一定就正好挨着这个段，这就需要用单调栈来寻找左右第一个$w_i$比它大的段。注意需要左边界严格大于，右边界大于等于（或者反过来）。以此来保证两个段之间都可以比较出大小不会出现等于的情况。

##### 时间复杂度 $O(n\log n)$

##### code

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=2e5+5;
int n,C,a[N],b[N],c[N],cl,pre[N],suf[N],w[N],l[N],r[N],st[N],tp,mn=1e9;
long long t,ans;
int main(){
	scanf("%d %d",&n,&C);
	for(int i=1;i<=n;++i){
		scanf("%d %d",&a[i],&b[i]);
		c[++cl]=a[i],c[++cl]=b[i];
	}
	sort(c+1,c+cl+1);
	cl=unique(c+1,c+cl+1)-c-1;
	for(int i=1;i<=n;++i){
		a[i]=lower_bound(c+1,c+cl+1,a[i])-c;
		b[i]=lower_bound(c+1,c+cl+1,b[i])-c;
		if(a[i]<=b[i])pre[a[i]]++,pre[b[i]]--;
		else suf[a[i]-1]++,suf[b[i]-1]--;
	}
	for(int i=1;i<cl;++i)pre[i]+=pre[i-1];
	for(int i=cl-1;i;i--)suf[i-1]+=suf[i];
	for(int i=1;i<cl;++i)w[i]=max(1,max(pre[i],suf[i]));
	for(int i=1;i<cl;++i){
		while(tp&&w[i]>w[st[tp]])tp--;
		l[i]=st[tp];
		st[++tp]=i;
		t+=abs(w[i]-w[i-1]);
		mn=min(mn,w[i]);
		ans+=2ll*w[i]*(c[i+1]-c[i]);
	}
	t+=w[1];
	tp=0;
	for(int i=cl-1;i;i--){
		while(tp&&w[i]>=w[st[tp]])tp--;
		r[i]=st[tp];
		st[++tp]=i;
	}
	for(int i=1;i<cl;++i){
		if(!l[i]||!r[i])continue;
		int ll=c[r[i]]-c[l[i]+1];
		if(C>ll){
			int k=min(w[l[i]],w[r[i]])-w[i];
			t-=2ll*k;
			ans+=2ll*ll*k;
		}
	}
	printf("%lld\n",ans+1ll*C*t);
	return 0;
}
```

