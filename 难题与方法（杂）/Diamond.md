# [Diamond](https://codeforces.com/gym/105173/problem/G)
### 简要题意
给你一个长为 $n$ 的序列 $a$ ，每次给出 $l,r,p,q$ 。问只看区间 $[l,r]$ 内的 $a_i$ 等于 $p$ 或 $q$ 的数。逆序对一共有多少个。
$1 \le n \le 10^5$ ，$1 \le a_i,l,r,p,q \le n$
### 问题分析
考虑根号分治，如果 $p,q$ 都小于 $\sqrt n$  ，则直接双指针扫一遍得到答案。

预处理所有个数大于 $\sqrt n$ 的数 $x$，记录每个位置前有多少个 $x$ 并做前缀和。作差计算。

~~懒得写了，摸了~~
##### code：
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=1e5+5;
int n,m,cnt[N],a[N],id[N],tot,l[N],h[N];
unsigned int pre[300][N],val[300][N];
vector<int>V[N];
int main(){
	scanf("%d %d",&n,&m);
	for(int i=1;i<=n;++i){
		scanf("%d",&a[i]);
		V[a[i]].push_back(i);
		cnt[a[i]]++;
		l[i]=h[a[i]];
		h[a[i]]=i;
	}
	for(int i=1;i<=n;++i)if(cnt[i]>350){
		id[i]=++tot;
		int w=0;
		for(int j=1;j<=n;++j){
			if(a[j]==i)w++;
			pre[tot][j]=w+pre[tot][l[j]];
			val[tot][j]=w;
		}
	}
	for(int i=1;i<=m;++i){
		int l,r,p,q;
		scanf("%d %d %d %d",&l,&r,&p,&q);
		if(id[p]){
			int x=lower_bound(V[q].begin(),V[q].end(),l)-V[q].begin();
			int y=upper_bound(V[q].begin(),V[q].end(),r)-V[q].begin();
			y--;
			if(x>y){
				puts("0");
				continue;
			}
			int pcnt=val[id[p]][r]-val[id[p]][l-1],qcnt=y-x+1;
			int w=pre[id[p]][V[q][y]]-pre[id[p]][::l[V[q][x]]]-qcnt*val[id[p]][l-1];
			printf("%lld\n",1ll*pcnt*qcnt-w);
		}else if(id[q]){
			int x=lower_bound(V[p].begin(),V[p].end(),l)-V[p].begin();
			int y=upper_bound(V[p].begin(),V[p].end(),r)-V[p].begin();
			y--;
			if(x>y){
				puts("0");
				continue;
			}
			int pcnt=y-x+1;
			int w=pre[id[q]][V[p][y]]-pre[id[q]][::l[V[p][x]]]-pcnt*val[id[q]][l-1];
			printf("%d\n",w);
		}else {
			int u=0,v=0,ans=0;
			while(u<(int)V[p].size()){
				if(V[p][u]<l)++u;
				else break;
			}
			while(v<(int)V[q].size()){
				if(V[q][v]<l)++v;
				else break;
			}
			int vv=v;
			while(u<(int)V[p].size()&&v<(int)V[q].size()){
				if(V[p][u]>r||V[q][v]>r)break;
				if(V[p][u]>V[q][v]){
					v++;
				}else {
					ans+=v-vv;
					u++;
				}	
			}
			while(u<(int)V[p].size()){
				if(V[p][u]<=r)++u,ans+=v-vv;
				else break;
			}
			printf("%d\n",ans);
		}
	}
}
```