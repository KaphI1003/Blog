# [Min Cost Permutation](https://codeforces.com/contest/1844/problem/F2)
### 简要题面
给你一个长度为$n$的序列$a$，请找出一个$a$的重排$b$，使
$$
\sum_1^{n-1} |b_{i+1}-b_i-c|
$$
最小，输出符合条件的字典序最小的答案。
$1 \le n \le 2 \cdot 10^5, -10^9 \le c,a_i \le 10^9$
### 问题分析
当 $c \ge 0$ 时，明显把$a$从小到大排序后输出即可。

当 $c < 0$时，将$a$从大到小排序我们就可以得到最小的$\sum_1^{n-1} |b_{i+1}-b_i-c|$。但是字典序不一定是最小的，所以我们要从中优化。

先将$a$从大到小排序。

此时$\sum_1^{n-1} |b_{i+1}-b_i-c|$取到最大是因为我们让尽量多多的$b_{i+1}-b_i-c \ge 0$，所以我们让重排的过程中让$\sum max{0,c+b_i-b_{i+1}}$与大到小排序相同即可得到一样的$\sum_1^{n-1} |b_{i+1}-b_i-c|$ 。

那么也就是说，我们在重排的过程中，原先$a_{i+1}-a_i<c$的两个位置的数不能发生改变，其它位置保证重排后仍满足$b_{i+1}-b_i \ge c$即可。特殊的，$b_1=a_1,b_n=a_n$，也就是首尾不能发生改变。

于是我们可以把问题转化成一个图上的问题，我们给满足$a_i-a_j \le -c$的$i,j$连一条 $i->j$的边，原先$a_{i+1}-a_i<c$的位置连一条$i->i+1$的边，问题就变成找一条经过每个点一次且字典序最小的路径。

在该图中一个点$i$能一步走到的点的集合是一段区间，设为$[1,r_i]$,明显，$r_i$是递增的。

在图中存在一些割点（$r[i-1]=i$ 的点），我们在遍历到割点前要把在编号比割点小的点全部遍历过才行。要注意的是我们遍历过一个点后要删去这个点，这可能会导致割点的变动，我们需要动态维护割点。

于是我们可以开一个 set 来保存所有的非割点，一个点$i$是割点的充要条件是$a_{i+1}-a_{i-1} \le -c$。其中$a_{i+1}$表示未删的比$a_i$大的最小的点，$a_{i-1}$表示未删的比$a_i$小的最大的点。而删一个点只会影响未删的与它相邻的两个点是否是割点。所以我们每次从  set 中选出最小的可以到达的非割点并走到它更新，如果选不出来就说明我们前面挡了一个割点，选最小的割点并更新即可。

##### 时间复杂度 $O(n\log n$
##### code 
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=1e6+5;
int T,n,c,a[N],l[N],r[N],ans[N];
set<pair<int,int> >st;
set<pair<int,int> >::iterator it;
void ck(int x){
	if(x&&l[x]&&r[x]&&a[r[x]]-a[l[x]]>=-c)st.insert(make_pair(a[x],x));
}
void Del(int x){
	st.erase(make_pair(a[x],x));
	if(l[x])st.erase(make_pair(a[l[x]],l[x]));
	if(r[x])st.erase(make_pair(a[r[x]],r[x]));
	r[l[x]]=r[x];
	l[r[x]]=l[x];
	ck(l[x]);
	ck(r[x]);
}
void slv(){
	scanf("%d %d",&n,&c);
	st.clear();
	for(int i=1;i<=n;++i)scanf("%d",&a[i]);
	sort(a+1,a+n+1);
	if(c>=0){
		for(int i=1;i<=n;++i)printf("%d ",a[i]);
		puts("");
		return;
	}
	c=-c;
	reverse(a+1,a+n+1);
	r[n]=l[0]=0;
	for(int i=1;i<=n;++i)l[i+1]=r[i-1]=i;
	for(int i=1;i<=n;++i)ck(i);
	ans[1]=1;
	Del(1);
	for(int i=2;i<=n;++i){
		it=st.lower_bound(make_pair(a[ans[i-1]]-c,0));
		int y= (it==st.end())? r[0]:it->second;
		ans[i]=y;
		Del(y);
	}
	for(int i=1;i<=n;++i)printf("%d ",a[ans[i]]);
	puts("");
}
int main(){
	scanf("%d",&T);
	while(T--)slv();
	return 0;
}
```