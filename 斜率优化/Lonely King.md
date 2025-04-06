# [Lonely King](https://codeforces.com/gym/104427/problem/D)
### 简要题面
一棵$n$个节点根节点为 $1$ 的有向树，每个父亲向儿子连一条蓝色的有向边，每个点住着$a_i$个人，你可以把树上的一条全蓝色路径替换成一条红色的从路径起点指向路径终点的边，一个图的代价是有多少对人可以从一方出发到达另一方， 问替换边后全图最小的代价是多少。

$n\le 2 \cdot 10^5,\ a_i \le 10^6$
### 解法1：set 维护凸包启发式合并
我们最后肯定是要把所有的边全部变成红色，这样肯定最优，那么对于一个非根节点$x$我们就要考虑让哪个儿子的红边从$x$传上去。最后得到的图就是每个非叶子节点连其直接儿子个数减一个叶子的图的并（特殊的，根节点 1 是连其直接儿子个数个叶子）。

深入探讨发现我们可以把每个点的状态写成一个由多条直线组成的上凸包的函数 F(x,w)，表示如果给 $x$向上的边接一个值为$w$的点的最小代价。

那么我们就可以写出两个儿子($u,v$)的合并方程，考虑上传 $u$，那么我们会消耗$F(v,a_x)$的代价，即将$u$的直线凸包全体加上$F(v,a_x)$的代价，上传$v$同理，那么我们考虑如何合并两个凸包，最简单的办法就是树剖启发式合并。但是我们用了 $set$ 来维护直线构成的凸包就不能修改 $set$ 里面的数值，所以直线全体加上一个数就只能放在外面开个数组$C_i$来维护。设$x$的直线集合为$k_ix+b_i+C_x$，$y$的直线集合为$K_ix+B_i+C_y$，那么我们把$y$并入到$x$里时，$x$的新直线集合为$k_ix+b_i+C_x+C_y+\min\{ K_i\cdot a_x + B_i\}$,$y$的新直线集合为$K_ix+B_i+C_x+C_y+\min\{ k_i\cdot a_x + b_i\}$

合并就需要让 $C_x$ 加上 $\min\{ K_i\cdot a_x + B_i\}\ +C_y$。然后在插入$y$集合的直线时我们就让每条直线的$B_i$加上$\min\{ k_i\cdot a_x + b_i\}-\min\{ K_i\cdot a_x + B_i\}$即可。


##### $时间复杂度O(n\log^2 n)$
##### code
```cpp
#include<bits/stdc++.h>
typedef long long LL;
using namespace std;
const int N=2e5+5;
const double inf=4e18;
struct P{
	LL k,b;
	double x;
	bool operator < (const P&t)const{
		if(k&&t.k)return (k!=t.k)? k > t.k : b < t.b;
		return x < t.x;
	}
};
int n,sz[N],a[N],sn[N],f[N],top[N];
LL C[N];
vector<int>E[N];
double cross(P u,P v){
	return 1.0*(u.b-v.b)/(v.k-u.k);
}
set<P>st[N];
set<P>::iterator it,lt,it2;
void dfs1(int x){
	sz[x]=1;
	for(auto y:E[x]){
		dfs1(y);
		sz[x]+=sz[y];
		if(sz[sn[x]]<sz[y])sn[x]=y;
	}
}
LL getMin(int u,int x){
	P t={0,0,1.0*x};
	it=st[u].upper_bound(t);
	it--;
	return (it->k)*x+(it->b);
}
void Insert(set<P> &st,P t){
	if(st.empty()){
		st.insert(t);
		return;
	}
	it=st.upper_bound(t);
	if(it!=st.begin()){
		lt=it;lt--;
		if((lt->k)==(t.k))return;
		t.x=cross(*lt,t);
		if(it!=st.end()&&t.x>=(it->x))return;
	}
	
	if(it!=st.end()){
		P tmp=*it;
		tmp.x=cross(tmp,t);
		st.erase(it);
		st.insert(tmp);
	}
	st.insert(t);
	while(1){
		it=st.lower_bound(t);
		lt=it;lt++;
		if(lt==st.end())break;
		it2=lt;it2++;
		if(it2==st.end())break;
		if( (it2->x) > cross(*it,*it2))break;
		P tmp=*it2;
		tmp.x=cross(*it,*it2);
		st.erase(lt),st.erase(it2);
		st.insert(tmp);
	}
	while(1){
		it=st.lower_bound(t);
		if(it==st.begin())break;
		lt=it;lt--;
		if(lt==st.begin())break;
		it2=lt;it2--;
		if( (it->x) > cross(*it,*it2))break;
		t=*it;
		t.x=cross(*it,*it2);
		st.erase(lt),st.erase(it);
		st.insert(t);
	}
}
void Merge(int x,int y,int u){
	LL wy=getMin(y,a[u]),wx=getMin(x,a[u]),delta=wx-wy;
	C[x]+=C[y]+wy;
	for(auto g:st[y]){
		g.b+=delta;
		Insert(st[x],g);
	}
}
void dfs2(int x){
	int u=top[x];
	if(!sn[x]){
		st[u].insert((P){a[x],0,-inf});
		return;
	}
	dfs2(sn[x]);
	for(auto y:E[x])if(y!=sn[x]){
		dfs2(y);
		Merge(u,y,x);
	}
}
int main(){
	scanf("%d",&n);
	for(int i=2;i<=n;++i){
		scanf("%d",&f[i]);
		E[f[i]].push_back(i);
	}
	for(int i=1;i<=n;++i)scanf("%d",&a[i]);
	dfs1(1);
	top[1]=1;
	for(int i=2;i<=n;++i)top[i]= (sn[f[i]]==i)? top[f[i]]:i;
	dfs2(1);
	LL ans=getMin(1,a[1])+C[1];
	printf("%lld\n",ans);
	return 0;
}

```

### 解法2：李超树合并
和上面类似，但是直线都用李超树来存，合并直接线段树合并，细节比上面少很多，也更好写。缺点是只能查询线段树上出现的点。

##### 时间复杂度 $O(n \log^2 n)$
```cpp
#include<bits/stdc++.h>
typedef long long LL;
using namespace std;
const int N=2e5+5,D=1e6;
int n,a[N],f[N],cnt,rt[N];
LL C[N];
vector<int>E[N];
struct P{
	LL k,b;
	LL w(int x){return k*x+b;}
};
struct Node{
	P t;
	int lc,rc;
	LL A;
}tr[D*30];
void Down(int p){
	tr[p].t.b+=tr[p].A;
	int lc=tr[p].lc,rc=tr[p].rc;
	if(lc)tr[lc].A+=tr[p].A;
	if(rc)tr[rc].A+=tr[p].A;
	tr[p].A=0;
}
LL Qry(int p,int l,int r,int x){
	if(!p)return 4e18;
	if(tr[p].A)Down(p);
	LL rs=tr[p].t.w(x);
	int mid=(l+r)>>1;
	if(x<=mid)return min(rs,Qry(tr[p].lc,l,mid,x));
	else return min(rs,Qry(tr[p].rc,mid+1,r,x));
}
void Add(int &p,int l,int r,P t){
	int mid=(l+r)>>1;
	if(!p){
		p=++cnt;
		tr[p].t=t;
		return;
	}
	if(tr[p].A)Down(p);
	P &s=tr[p].t;
	if(s.w(mid)>t.w(mid))swap(s,t);
	if(s.w(l)>t.w(l))Add(tr[p].lc,l,mid,t);
	if(s.w(r)>t.w(r))Add(tr[p].rc,mid+1,r,t);
}
int Merge(int p,int q,int l,int r){
	if(!p||!q)return p|q;
	int mid=(l+r)>>1;
	if(tr[p].A)Down(p);
	if(tr[q].A)Down(q);
	tr[p].lc=Merge(tr[p].lc,tr[q].lc,l,mid);
	tr[p].rc=Merge(tr[p].rc,tr[q].rc,mid+1,r);
	Add(p,l,r,tr[q].t);
	return p;
}
void slv(int x,int y){
	LL wy=Qry(rt[y],1,D,a[x]),wx=Qry(rt[x],1,D,a[x]),delta=wx-wy;
	C[x]+=C[y]+wy;
	tr[rt[y]].A+=delta;
	rt[x]=Merge(rt[x],rt[y],1,D);
}
void dfs2(int x){
	if(!E[x].size()){
		rt[x]=++cnt;
		tr[rt[x]].t=(P){a[x],0};
		return;
	}
	for(auto y:E[x]){
		dfs2(y);
		if(!rt[x])rt[x]=rt[y],C[x]=C[y];
		else slv(x,y);
	}
}
int main(){
	scanf("%d",&n);
	for(int i=2;i<=n;++i){
		scanf("%d",&f[i]);
		E[f[i]].push_back(i);
	}
	for(int i=1;i<=n;++i)scanf("%d",&a[i]);
	dfs2(1);
	LL ans=Qry(rt[1],1,D,a[1])+C[1];
	printf("%lld\n",ans);
	return 0;
}
```