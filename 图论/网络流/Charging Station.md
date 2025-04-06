# [Charging Station](https://codeforces.com/gym/105173/problem/B)
### 简要题意
有三组发电站，每组 $n$ 个。一个发电站有三种状态，分别是工作，辅助，停止。第 $i$ 个发电站工作时输出 $a_i$ 的能量，辅助时消耗 $b_i$ 的能量，停止不产生也不消耗能量。第一组中有些发电站对第二组的发电站有供求关系。一个供求关系可以表示为，想要 $x$ 工作，需要 $y$ 处于供能状态。其中 $x\in [1,n]$ ，$y\in[n+1,2n]$ 。同理第二组中有些发电站对第三组的发电站有供求关系。一共有 $m$ 组供求关系，问最大能输出多少能量。

$1 \le n,m \le 10^5$ ，$1 \le a_i,b_i \le 10^9$

### 问题分析
我们先考虑只有两组发电站怎么做。对于供求关系我们建图解决，第一组开始全部停止，点权为 $a_i$ ，第二组开始全部工作，点权为 $-a_i-b_i$ ，供求关系让 $x$ 向 $y$ 连边。最后求出图的最大权闭合子图的权值再加上初始值就是答案。

再考虑三层怎么做，二三组的供求关系可以转化为如果 $y$ 从辅助转为工作，$x$ 就不能工作。我们让第三组开始全部辅助，点权为$a_i+b_i$ 。此时我们需要把第二层的点拆成两组，一个代表第二层的点从工作到停止，一个代表从停止到辅助。像下图连边求出图的最大权闭合子图的权值再加上初始值就是答案。

例图：
![[Pasted image 20240711130011.png]]

##### code:
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=4e5+5,inf=2e9+7;
int n,m,a[N],b[N],h[N],cu[N],dp[N],q[N],ql,s,t,pcnt,el;
long long ans;
struct E{
    int l,to,w;
}e[N*12];
void AddE(int x,int y,long long w){
	++el;
	e[el<<1].to=y;e[el<<1].l=h[x];h[x]=el<<1;
	e[el<<1|1].to=x;e[el<<1|1].l=h[y];h[y]=el<<1|1;
	e[el<<1].w=w;
}
bool bfs(){
	q[ql=1]=s;
	for(int i=1;i<=pcnt;++i)dp[i]=0;
	dp[s]=1;
	for(int i=1;i<=ql;++i){
		int x=q[i];
		for(int o=h[x];o;o=e[o].l)if(e[o].w){
			int to=e[o].to;
			if(!dp[to])dp[q[++ql]=to]=dp[x]+1;
		}
	}
	return dp[t];
}
int dfs(int x,int fw){
	if(x==t)return fw;
	for(int &i=cu[x];i;i=e[i].l)if(e[i].w){//&号不能少!
		int to=e[i].to;
		if(dp[to]!=dp[x]+1)continue;
		int w=dfs(to,min(fw,e[i].w));
		if(w){
			e[i].w-=w;e[i^1].w+=w;
			fw-=w;
			return w;
		}
	}
	return 0;
}
int main(){
    scanf("%d",&n);
    for(int i=1;i<=3*n;++i)scanf("%d",&a[i]),ans+=a[i];
    for(int i=1;i<=3*n;++i)scanf("%d",&b[i]);
    scanf("%d",&m);
    for(int i=1;i<=m;++i){
        int x,y;
        scanf("%d %d",&x,&y);
        if(x<=n){
            AddE(x,y,inf),AddE(x,y+2*n,inf);
        }else {
            AddE(y,x,inf);
        }
    }
	pcnt=4*n;
	s=++pcnt,t=++pcnt;
	for(int i=1;i<=n;++i)AddE(s,i,a[i]);
	for(int i=2*n+1;i<=3*n;++i)AddE(s,i,a[i]+b[i]);
	for(int i=n+1;i<=2*n;++i)AddE(i,t,a[i]),AddE(i+2*n,t,b[i]);
	while(bfs()){
		for(int i=1;i<=pcnt;++i)cu[i]=h[i];
		while(int rs=dfs(s,inf))ans-=rs;
	}
	printf("%lld\n",ans);
}
```