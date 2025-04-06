# [Catering](https://www.luogu.com.cn/problem/UVA1711)
### 简要题面
有 $n+1$个点,给出$i$到$j$的花费$a_{i,j}$,$(i<j)$,有$k$个人从$1$出发,求出每个点被经过且只被经过一次的最小花费。

$n,k \le 100 ,\ a_{i,j} \le 10^6$

### 问题分析
本题可以通过费用流进行求解。我们将一个点拆成 $in_i$和$out_i$两个点，再额外设置一个终点，我们令 $in_1$向$out_1$连一条容量为 $k$,边权为 $0$的边。然后对剩下的$in_i$向$out_i$连一条容量为 $1$,边权为 $0$的边。再让 $out_i$向$in_j\ (i<j)$连一条容量为 $1$,边权为 $a_{i,j}$的边。让$out_1$向终点连一条容量为 $k$,边权为 $0$的边。再让$out_i$向终点连一条容量为 $1$,边权为 $0$的边。跑费用流即可。

不过额外的我们需要让$in_i$向$out_i$的容量全部吃满，为了避免上下界网络流，我们可以使用一种 Trick，将边的边权减去一个极大值 $P$，然后跑费用流，最后再在总答案上加上这个最大值即可。

##### code 
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=225,M=3e5+5,inf=1e9+7,P=1e7;
int n,k,h[N],ans,ret,cu[N],w[N],v[N],el=1,s,t;
struct E{
	int l,to,w,f;
}e[M],E[M];
queue<int>Q;
bool spfa(){
	for(int i=1;i<N;++i)w[i]=inf;
	v[s]=1;w[s]=0;Q.push(s);
	while(!Q.empty()){
		int x=Q.front();Q.pop();v[x]=0;
		for(int i=h[x];i;i=e[i].l)if(e[i].w){
			int to=e[i].to;
			if(w[to]>w[x]+e[i].f){
				w[to]=w[x]+e[i].f;
				if(!v[to]){Q.push(to);v[to]=1;}
			}
		}
	}
	return w[t]!=inf;
}
int dfs(int x,int fw){
	if(x==t)return fw;
	v[x]=1;
	int s=0;
	for(int &i=cu[x];i;i=e[i].l)if(e[i].w){
		int to=e[i].to;
		if(v[to]||w[to]!=w[x]+e[i].f)continue;
		int w=dfs(to,min(fw,e[i].w));
		if(w){
			e[i].w-=w;
			e[i^1].w+=w;
			ret+=e[i].f*w;
			s+=w;
			fw-=w;
		}
	}
	v[x]=0;
	return s;
}
void AddE(int x,int y,int w,int f){
    ++el;
    e[el].to=y;e[el].l=h[x];h[x]=el;e[el].w=w;e[el].f=f;
    ++el;
    e[el].to=x;e[el].l=h[y];h[y]=el;e[el].w=0;e[el].f=-f;
}
int main(){
	scanf("%d %d",&n,&k);
    AddE(1,1+105,k,0);
	for(int i=1;i<=n;++i){
        for(int j=i+1;j<=n+1;++j){
            int w;
            scanf("%d",&w);
            AddE(i+105,j,1,w);
        }
	}
    s=1,t=n+3;
    for(int i=2;i<=n+1;++i){
        AddE(i,i+105,1,-P);
        AddE(i+105,t,1,0);
    }
	AddE(1+105,t,k,0);
    while(spfa()){
        for(int i=1;i<N;++i)cu[i]=h[i];
        while(int rs=dfs(s,inf)){
			ans+=rs;
		}
    }
	printf("%d\n",ret+P*n);
}
```

```
stateDiagram-v2
    state if_state <>
    [*] --> IsPositive
    IsPositive --> if_state
    if_state --> False: if n < 0
    if_state --> True : if n >= 0
```
