# 基础fake练习题
#### source: 2019五校联考-成七day2 T2

### 分析：
首先,成都七中的大佬不仅FAKE，还喜欢嘲讽蒟蒻(官方题解用什么超纲的可并堆吓唬我们)...

但是，线段树不仅可以替换堆，还可以替换可并堆(但是不一定所有情况都适用)！！！

第一我们先考虑链的情况。

联想一下借教室(Noip2016)这道题的O(n)做法，我们同样模拟这个做法来做，先把所有的区间全部放上，维护差分，然后再从高到低枚举点，如果当前点不满足条件的话，我们就选还没有用过的左端点是他祖宗它，右端点最远的一条区间去掉，因为它的祖宗一定已经符合条件了，但是它的儿子却不一定，所以我们就要贪心选最远的。

那么维护时我们就直接把左端点为i的区间扔到一个堆里，每次取出最远的就可以。

然后，我们再把这个做法搬到树上。

不过直接搬的话，再按上面的办法我们就会遇到一个问题——该往哪个儿子走呢？(陷入沉思...)

不过我们可以反过来，先算儿子再算父亲(儿子有一堆，但爸爸只有一个(感动))。这样我们就可以避免陷入选择恐惧症啦。

那么我们就按回溯序（！！？）来确定顺序，先遍历一次把每一个点的deep和回溯序求出来，然后我们再把区间按右端点的回溯序从小到大排序。然后再重新遍历一次，对当前点有影响的区间就是连着的一块。然后我们通过树上差分就可以算出当前点被几条路径所包含。

##### 时间复杂度：$O(n\log n)$

##### code：
```
#include<bits/stdc++.h>
using namespace std;
const int N=3e5+5;
int n,m,li[N],h[N],dp[N],z,ans,l,r,tr[N<<2],df[N],dfn,su,s[N],rs,q[N],ql,w[N<<2],nu[N];
struct E{
	int l,to;
}e[N<<1];
struct P{
	int x,y;
	bool operator < (const P&t)const{
		return df[y]<df[t.y];
	}
}a[N];
int cmp(int x,int y){
	return dp[w[x]]<dp[w[y]]? x:y;
}
void js(int z,int y,int c){//这里的线段树记录的是当前的路径是否存在 
	if(z==y){
		tr[c]=c;
		w[c]=a[z].x;
		nu[z]=c;
		return;
	}
	int mid=z+y>>1;
	js(z,mid,c<<1);js(mid+1,y,c<<1|1);
	tr[c]=cmp(tr[c<<1],tr[c<<1|1]);
}
void Ad(int c,int W){
	tr[c]=W;
	for(c>>=1;c;c>>=1){
		tr[c]=cmp(tr[c<<1],tr[c<<1|1]);
	}
}
void Qr(int z,int y,int c){
	if(y<l||z>r)return;
	if(z>=l&&y<=r){
		q[++ql]=c;
		return;
	}
	int mid=z+y>>1;
	if(l<=mid)Qr(z,mid,c<<1);
	if(r>mid)Qr(mid+1,y,c<<1|1);
}
void se(int x,int f){
	dp[x]=dp[f]+1;
	for(int i=h[x];i;i=e[i].l){
		int to=e[i].to;
		if(to==f)continue;
		se(to,x);
	}
	df[x]=++dfn;
}
void dfs(int x,int f){
	int lsu=su,ll=z;
	for(int i=h[x];i;i=e[i].l){
		int to=e[i].to;
		if(to==f)continue;
		dfs(to,x);
	}
	while(z<=m&&a[z].y==x){su++;s[a[z++].x]--;}//通过差分确定被几条路径覆盖 
	l=ll;r=z-1;
	if(su-lsu>li[x]){//某DFS做差 
		ql=0;
		Qr(1,m,1); 
		while(su-lsu>li[x]){
			rs=tr[q[1]];
			for(int i=2;i<=ql;++i)rs=cmp(rs,tr[q[i]]);
			Ad(rs,0);ans--;
			su--;s[w[rs]]++;
			 
		}
	}
	su+=s[x];//树上差分 
}
int main(){
	int x,y;
	scanf("%d %d",&n,&m);
	for(int i=1;i<=n;++i)scanf("%d",&li[i]);
	for(int i=1;i<n;++i){
		scanf("%d %d",&x,&y);
		e[i<<1].to=x;e[i<<1].l=h[y];h[y]=i<<1;
		e[i<<1|1].to=y;e[i<<1|1].l=h[x];h[x]=i<<1|1;
	}
	se(1,0);
	for(int i=1;i<=m;++i){
		scanf("%d %d",&a[i].x,&a[i].y);
		if(dp[a[i].x]>dp[a[i].y])swap(a[i].x,a[i].y);
	}
	sort(a+1,a+m+1);
	dp[0]=1e9;
	ans=m;z=1;
	js(1,m,1);
	dfs(1,0);
	printf("%d\n",ans);
	return 0;
}
```