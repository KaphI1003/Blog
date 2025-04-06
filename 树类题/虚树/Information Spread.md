# [Information Spread](https://codeforces.com/gym/104857/problem/L)

### 简要题面
~~简要不了一点~~
给你一个 $n$ 个点 $m$ 条有向边的图，问执行下面的代码后，每个点的 $aware$ 值得期望。
$1 \le n \le 10^5$ ，$n-1 \le m \le 3*10^5$
```cpp
void dfs(int x){
	if(vis[x])return;
	vis[x]=1;
	for(auto [to,w]:E[x]){
		if(aware[x]&&!aware[to])
			if(uniformly_rand(0,1)<=w)aware[to]=1;
		dfs(to);
	}
}
int main(){
	...//read graph
	aware[1]=1;
	dfs(1);
}
```
### 问题分析
首先我们可以建出 DFS 树，考虑如何计算一个点 $x$ 的答案。显然我们可以通过树形 DP 做。首先有$dp[x] = 1$ ，其它店 DP 值为 $0$ 。设点 $u$ 在合并儿子 $v$ ，我们有 $dp'[u] = dp[u] + (1-dp[u]) * dp[v]$ 。答案就是 $dp[1]$ ，我们得到了 $O(n^2)$ 的做法。

然后我们发现对于 $x$ ，我们只需要 $1$ , $x$ , 以及所有跟 $x$ 直接相连的点构成的虚树即可。剩下的信息可以压缩。所有的虚树的点的数量是 $O(n+m)$ 级别的，照上面的直接做即可。

##### 时间复杂度 $O(m\log n)$
##### code：
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=3e5+5,M=998244353;
int pw(int x,int t){
    int rs=1;
    for(;t;t>>=1,x=1ll*x*x%M)if(t&1)rs=1ll*rs*x%M;
    return rs;
}
struct E{int to,w;};
vector<E>V[N],Ef[N];
int n,m,dp[N],F[N][20],W[N][20],df[N],dfn;
void dfs(int x){
    df[x]=++dfn;
    for(int i=1;(1<<i)<=dp[x];++i){
        F[x][i]=F[F[x][i-1]][i-1];
        W[x][i]=1ll*W[x][i-1]*W[F[x][i-1]][i-1]%M;
    }
    for(auto t:V[x]){
        int to=t.to;
        if(!df[to]){
            F[to][0]=x;
            W[to][0]=t.w;
            dp[to]=dp[x]+1;
            dfs(to);
        }
        else Ef[to].push_back((E){x,t.w});
    }
}
int lca(int x,int y){
    if(dp[x]<dp[y])swap(x,y);
    for(int i=0;dp[y]-dp[x];++i){
        if(((dp[y]-dp[x])>>i)&1){
            x=F[x][i];
        }
    }
    if(x==y)return x;
    for(int i=19;~i;i--){
        if(dp[x]>=(1<<i)&&F[x][i]!=F[y][i]){
            x=F[x][i],y=F[y][i];
        }
    }
    return F[x][0];
}
bool cmp(int x,int y){
    return df[x]<df[y];
}
int a[N],l,st[N],tp;
stack<E>e[N];
void AddE(int x,int y){
    int w=1,z=y;
    for(int i=0;dp[y]-dp[x];++i){
        if(((dp[y]-dp[x])>>i)&1){
            w=1ll*w*W[y][i]%M;
            y=F[y][i];
        }
    }
    e[x].push((E){z,w});
}
int slv(int x,int s){
    if(x==s)return 1;
    int w=0;
    while(!e[x].empty()){
        int tmp=1ll*slv(e[x].top().to,s)*e[x].top().w%M;
        e[x].pop();
        w=(w+(M+1ll-w)*tmp)%M;
    }
    return w;
}
void void_tree(int s){
    st[tp=1]=a[1];
    while(!e[a[1]].empty())e[a[1]].pop();
    for(int i=2;i<=l;++i){
        int c=lca(st[tp],a[i]);
        while(tp>1&&dp[st[tp-1]]>=dp[c]){
            AddE(st[tp-1],st[tp]);
            tp--;
        }
        if(st[tp]!=c){
            while(!e[c].empty())e[c].pop();
            AddE(c,st[tp]);
            st[tp]=c;
        }
        st[++tp]=a[i];
        while(!e[a[i]].empty())e[a[i]].pop();
    }
    while(tp>1){
        AddE(st[tp-1],st[tp]);
        tp--;
    }
    for(auto t:Ef[s]){
        e[t.to].push((E){s,t.w});
    }
    printf("%d\n",slv(1,s));
}
int main(){
    scanf("%d %d",&n,&m);
    for(int i=1;i<=m;++i){
        int x,y,p,q;
        scanf("%d %d %d %d",&x,&y,&p,&q);
        p=1ll*p*pw(q,M-2)%M;
        V[x].push_back((E){y,p});
    }
    dfs(1);
    for(int i=1;i<=n;++i){
        l=0;
        a[++l]=1,a[++l]=i;
        for(auto j:Ef[i])a[++l]=j.to;
        sort(a+1,a+l+1,cmp);
        l=unique(a+1,a+l+1)-a-1;
        void_tree(i);
    }
}
```
