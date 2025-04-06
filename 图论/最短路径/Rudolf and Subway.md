# [Rudolf and Subway](https://vjudge.net.cn/problem/CodeForces-1941G)
### 简要题意
$n$ 个点 $m$ 条边的图，每条边有一个颜色 $c_i$ 。如果走的边的颜色和上一条边颜色相同，则没有花费，否则花费 $1$ 点体力。问最少需要多少体力从 $st$ 到 $ed$ 。

$2\le n, m\le 2e5$ 
### 问题分析
新建一个图，开始时只有原来 $n$ 个点没有边。

我们把颜色相同的边组成的连通块都拉出来当做一个节点，向连通块中的点连一条权为 $0$ 的边，以及一条权为 $1$ 的反向边。这样跑最短路就可以得到答案。连通块的个数小于等于 $m$ ，所以最多只有 $4e5$ 个点。

##### 时间复杂性 $O(m\log{m})$
##### code：
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=2e5+5;
int T,n,m,mp[N],cnt,c[N],vis[N],pcnt,f[N],w[N];
deque<int>Q;
struct P{int x,y;};
vector<P>V[N];
struct D{int to,w;};
vector<D>E[N<<1];
int Find(int x){
    return (x==f[x])? x:f[x]=Find(f[x]);
}
void Edge_Add(int x,int y,int w){
    E[x].push_back((D){y,w});
}
void slv(){
    scanf("%d %d",&n,&m);
    pcnt=n;
    cnt=0;
    for(int i=1;i<=m;++i){
        int x,y;
        scanf("%d %d %d",&x,&y,&c[i]);
        if(!mp[c[i]]){
            mp[c[i]]=++cnt;
            V[cnt].clear();
        }
        V[mp[c[i]]].push_back((P){x,y});
    }
    for(int i=1;i<=n;++i)f[i]=i;
    for(int i=1;i<=cnt;++i){
        for(auto t:V[i]){
            f[Find(t.y)]=Find(t.x);
        }
        for(auto t:V[i]){
            int x=Find(t.x);
            if(!vis[x]){
                vis[x]=++pcnt;
            }
            Edge_Add(vis[x],t.x,0),Edge_Add(t.x,vis[x],1);
            Edge_Add(vis[x],t.y,0),Edge_Add(t.y,vis[x],1);
        }
        for(auto t:V[i]){
            vis[f[t.x]]=0;
            f[t.x]=t.x,f[t.y]=t.y;
        }
    }
    int st,ed;
    scanf("%d %d",&st,&ed);
    Q.clear();
    for(int i=1;i<=pcnt;++i)w[i]=1e9;
    w[st]=0;
    Q.push_back(st);
    while(!Q.empty()){
        int x=Q.front();
        Q.pop_front();
        for(auto t:E[x]){
            if(w[t.to]>w[x]+t.w){
                w[t.to]=w[x]+t.w;
                if(t.w)Q.push_back(t.to);
                else Q.push_front(t.to);
            }
        }
    }
    printf("%d\n",w[ed]);
    for(int i=1;i<=m;++i)mp[c[i]]=0;
    for(int i=1;i<=pcnt;++i)E[i].clear();
}
int main(){
    scanf("%d",&T);
    while(T--)slv();
}
```
