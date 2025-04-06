# The King’s Problem

[题目](https://cn.vjudge.net/contest/294254#problem/C)

#### 题目描述

在一个王国里面, 国王有一个新的问题.

皇城中有N个城市M条单行路，为了让他的王国更加高效，国王想要将他的王国划分成几个州，每个城市必须属于一个州。对于两个城市（u，v），必须满足以下3个条件：

1、如果有一条从u到v的路，也有一条从v到u的路，那么u、v必须属于同一个州；

2、对于每一个州里的任何两个城市u、v，至少要有一方能到达另一方（必须经过同一个州的点到达）。

3、一个城市只能属于一个州。

现在国王想要知道他的王国最少可以划分成多少个州。

##### Input

第一行是一个数字T，代表测试组数，接下来是T组测试。
每组测试数据的第一行包含两个整数n、m(0 < n <= 5000,0 <= m <= 100000),分别指n个城市，m条单行路，接下来m行，每行包含两个数字a、b，表示有一条从城市a到b的单行路。

##### Output

输出包含T行。

每组测试数据输出一行。

##### Sample Input

1

3 2

1 2

1 3

##### Sample Output

2

第一个条件是让我们把所有环缩点，第二个条件就是
求出这张图的最小路径覆盖，所以直接解决就可以了。

##### 代码如下：

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<queue>
using namespace std;
struct P{
	int l,to;
}e[100005],E[100005];
queue<int>Q;
int n,m,y,d[5005],l[5005],sk[5005],tp,t,L,h[5005],H[5005],nu,b[5005],T,a,s[5005],ans,de[5005];
bool is[5005];
void se(int x){
	d[x]=l[x]=++t;sk[++tp]=x;is[x]=1;
	for(int i=h[x];i;i=e[i].l){
		if(!d[e[i].to]){
			se(e[i].to);
			l[x]=min(l[x],l[e[i].to]);
		}
		else if(is[e[i].to])l[x]=min(l[x],l[e[i].to]);
	}
	if(l[x]==d[x]){
		++nu;
		do{
			y=sk[tp--];
			b[y]=nu;
			is[y]=0;
		}while(x!=y);
	}
}
void dfs(int x){
	is[x]=1;
	for(int i=H[x];i;i=E[i].l){
		if(!is[E[i].to]&&de[E[i].to]==de[x]+1){
			dfs(E[i].to);break;
		}
	}
	for(int i=H[x];i;i=E[i].l){
		s[E[i].to]--;
		if(!is[E[i].to]&&!s[E[i].to]){
			Q.push(E[i].to);
		}
	}
}
void D(int x){
	for(int i=H[x];i;i=E[i].l){
		if(is[E[i].to])continue;
		de[E[i].to]=max(de[E[i].to],de[x]+1);
		D(E[i].to);
	}
}
int main(){
	int x,y;
	scanf("%d",&T);
	while(T--){
		memset(d,0,sizeof(d));
		memset(is,0,sizeof(is));
		memset(H,0,sizeof(H));
		memset(h,0,sizeof(h));
		memset(de,0,sizeof(de));
		memset(s,0,sizeof(s));
		ans=L=t=nu=0;
		scanf("%d %d",&n,&m);
		for(int i=1;i<=m;++i){
			scanf("%d %d",&a,&e[i].to);
			e[i].l=h[a];h[a]=i;
		}
		for(int i=1;i<=n;++i)if(!d[i])se(i);
		for(int i=1;i<=n;++i){
			x=b[i];
			for(int o=h[i];o;o=e[o].l){
				y=b[e[o].to];
				if(x!=y){
					E[++L].to=y;E[L].l=H[x];H[x]=L;s[y]++;
				}
			}
		}
		for(int i=1;i<=nu;++i){
			if(!s[i])Q.push(i);
		}
		while(!Q.empty()){
			D(Q.front());
			dfs(Q.front());
			ans++;
			Q.pop();
		}
		printf("%d\n",ans);
	}
}

```

