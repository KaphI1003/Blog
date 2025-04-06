# [Connect the Dots](https://codeforces.com/gym/104427/problem/K)

### 简要题意
一个数轴上$n$个点，每个点有一个颜色$a_i$，你现在需要给点连边，相同颜色的点间不能直接连边，边只能在$x$轴上方连，边与边不能在顶点外的地方相交，问最多可以连几条边。
$1\le n\le 2 \cdot 10^5$

### 问题分析
我们将数轴弯曲成一个圆，那么问题等价于在圆上连弦，让弦两两不相交。

相邻的点贪心能连就连，因为它们一定不会与其它边相交。那么问题就变成了在一个$n$边形内部连边的问题，所以此时最多就只能再多连$n-3$条边。

有一个结论，如果存在$3$种以上的颜色，我们一定可以找到一种连$n-3$条边的方案。只要有三种颜色以上，我们就一定可以找到3个连续颜色不相同的位置$x,y,z$，如果$y$是其颜色的最后一个，那么它就可以向剩下的除$x,z$的所有点连边；否则$x,z$连边去掉$y$，剩下的$n-1$边形仍可以循环上面的步骤。

我们开个队列存可以可以删去的点，用链表找相邻的点，每次取队首判断它是否还未被删除，如果它是其颜色的最后一个，向其它点连边结束；否则判断其是否仍满足被删除的条件，如果能就删它，判断和它相邻的点是否满足条件，满足就加入队列，因为每次删一个最多加两个进队列，最多删 $n$次，所以这块时间复杂度为 $O(n)$。

##### 时间复杂度 $O(n)$
##### code 
```cpp
#include<bits/stdc++.h>
#define mp make_pair
using namespace std;
const int N=1e6+5;
int T,n,m,a[N],c[N],l[N],r[N],ll[N],rr[N];
queue<int>Q;
vector< pair<int,int> >rs;
void slv(){
	scanf("%d %d",&n,&m);
	a[n+1]=0;
	int k=0,t=0;
	for(int i=1;i<=m;++i)c[i]=0;
	for(int i=1;i<=n;++i){
		scanf("%d",&a[i]);
		if(!c[a[i]])k++;
		c[a[i]]++;
		if(a[i]!=a[i-1])t++;
	}
	rs.clear();
	if(k==1){
		puts("0");
		return;
	}
	if(a[n]==a[1]){
		t--;
		l[1]=n;
		while(a[l[1]-1]==a[1])l[1]--;
	}else l[1]=1;
	for(int i=1,j=2;i<=t;++i,++j){
		while(a[j]==a[j-1])++j;
		r[i]=j-1;
		l[i+1]=j;
	}
	for(int i=1;i<t;++i){
		for(int j=l[i+1];j<=r[i+1];++j)rs.push_back(mp(r[i],j));
	}
	for(int j=l[1];j!=r[1];j=j%n+1)rs.push_back(mp(r[t],j));
	if(t>2)rs.push_back(mp(r[t],r[1]));
	if(k==2){
		for(int i=1;i<=((t-4)>>1);++i){
			rs.push_back(mp(r[i],r[t-1-i]));
		}
	}else {
		while(!Q.empty())Q.pop();
		for(int i=1;i<=m;++i)c[i]=0;
		for(int i=1;i<=t;++i){
			int x=r[i],y=r[(i+t-2)%t+1],z=r[i%t+1];
			ll[x]=y,rr[x]=z;
			if(a[y]!=a[z])Q.push(x);
			c[a[x]]++;
		}
		while(!Q.empty()){
			int x=Q.front();
			Q.pop();
			if(ll[x]==-1)continue;
			if(c[a[x]]==1){
				for(int i=rr[rr[x]];i!=ll[x];i=rr[i]){
					rs.push_back(mp(x,i));
				}
				break;
			}
			int p=ll[x],q=rr[x];
			if(a[p]==a[q])continue;
			rr[p]=q,ll[q]=p;
			rs.push_back(mp(p,q));
			ll[x]=rr[x]=-1;
			c[a[x]]--;
			if(a[ll[p]]!=a[rr[p]])Q.push(p);
			if(a[ll[q]]!=a[rr[q]])Q.push(q);
		}
	}
	printf("%d\n",(int)rs.size());
	for(auto t:rs)printf("%d %d\n",t.first,t.second);
}
int main(){
	scanf("%d",&T);
	while(T--)slv();
	return 0;
}
```

