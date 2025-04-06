# [Recombination](https://tsinghua.contest.codeforces.com/group/sTsHnFxwiH/contest/453495/problem/F)

### 简要题面
给你一个长度为 $n$ 的字符串环$S$，$n$为偶数。定义两个环同构的表示将其中一个环不断顺时针旋转，有一个位置可以使得两个环完全相同。每一对数$(i,j)$可以产生一个新的环为 $S_i,S_{j+1},S_{i+2},S_{j+3},...,S_{i+n-2},S_{j+n-1}$,问有多少组$(i,j)$产生的环与$S$不同构。
$n \le 10^6$
### 问题分析
我们找到同构的数量$cnt$，那么$n^2-cnt$就是答案。
考虑哈希，$S$一共可以得到最多$n$同构环，用一个$set$存哈希值。然后我们找到有多少个$(1,j)$的环与$S$同构，设为$w$，由于旋转·$(1,j)$同构于$((1+k)\mod n+1,(j+k)\mod n+1)$，我们可以知道一共有$n·w$个同构的环。所以答案为$n(n-w)$

##### 时间复杂度 $O(n \log n)$
##### code
```cpp
#include<bits/stdc++.h>
#define w1 first
#define w2 second
#define mp make_pair
using namespace std;
const int N=1e6+5,M1=1e9+7,M2=1e9+9;
int n;
char c[N];
set<pair<int,int> >st;
int main(){
	scanf("%d",&n);
	scanf("%s",c+1);
	pair<int,int>p=mp(0,0),q=mp(0,0),t=mp(1,1);
	for(int i=1;i<=n;++i){
		p.w1=(26ll*p.w1+(c[i]-'a'))%M1;
		p.w2=(26ll*p.w2+(c[i]-'a'))%M2;
		t.w1=26ll*t.w1%M1;
		t.w2=26ll*t.w2%M2;
	}
	st.insert(p);
	t.w1=(1-t.w1+M1)%M1;
	t.w2=(1-t.w2+M2)%M2;
	for(int i=1;i<n;++i){
		p.w1=(26ll*p.w1+1ll*t.w1*(c[i]-'a'))%M1;
		p.w2=(26ll*p.w2+1ll*t.w2*(c[i]-'a'))%M2;
		st.insert(p);
	}
	p=mp(0,0);
	for(int i=1;i<=n;i+=2){
		p.w1=(676ll*p.w1+(c[i]-'a'))%M1;
		p.w2=(676ll*p.w2+(c[i]-'a'))%M2;
	}
	for(int i=2;i<=n;i+=2){
		q.w1=(676ll*q.w1+(c[i]-'a'))%M1;
		q.w2=(676ll*q.w2+(c[i]-'a'))%M2;
	}
	int rs=0;
	for(int i=2;i<=n;i+=2){
		pair<int,int> x=mp(0,0);
		x.w1=(26ll*p.w1+q.w1)%M1;
		x.w2=(26ll*p.w2+q.w2)%M2;
		if(st.find(x)!=st.end())rs++;
		q.w1=(676ll*q.w1+1ll*t.w1*(c[i]-'a'))%M1;
		q.w2=(676ll*q.w2+1ll*t.w2*(c[i]-'a'))%M2;
	}
	q=p;
	for(int i=1;i<=n;i+=2){
		pair<int,int> x=mp(0,0);
		x.w1=(26ll*p.w1+q.w1)%M1;
		x.w2=(26ll*p.w2+q.w2)%M2;
		if(st.find(x)!=st.end())rs++;
		q.w1=(676ll*q.w1+1ll*t.w1*(c[i]-'a'))%M1;
		q.w2=(676ll*q.w2+1ll*t.w2*(c[i]-'a'))%M2;
	}
	printf("%lld\n",1ll*n*(n-rs));
	return 0;
}
```