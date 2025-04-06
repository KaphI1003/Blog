# [Mex Tree](https://codeforces.com/contest/1830/problem/D)

### 简要题面

一棵$n$个点的树，每个点可以填$0$或$1$，问所有的路径(包括一个点)的 mex 的和的最大值时多少？
$1\le n \le 2\cdot 10^5$

### 问题分析

有一种贪心就是直接$0/1$交替填，这个贪心时错误的，可以被下面的情况卡掉：
[![pCWI0Z6.png](https://s1.ax1x.com/2023/07/11/pCWI0Z6.png)](https://imgse.com/i/pCWI0Z6)

所以我们考虑采用树形DP的办法来解决此题，但是直接 DP 时间复杂度和空间复杂度都是 $O(n^2)$的，不能够接受。
不过我们可以通过 0/1交替的贪心得到答案一定大于$n*(n+1)-2n$，而在 DP 过程中一个大小为$k$的同色块造成的亏损至少是$\dfrac{k(k+1)}{2}$，所以我们需要$\dfrac{k(k+1)}{2} < 2n$，可以推得$k < 2\sqrt n$。
每个块的大小小于$\sqrt n$，可以让时间复杂度和空间复杂度优化到$O(n \sqrt n)$，但是空间还是太大了，所以我们使用重剖内存优化来满足内存的要求。

##### code

    #include<bits/stdc++.h>
    using namespace std;
    const int N=2e5+5,inf=1e9;
    int T,n,m,f,h[N],sz[N],sn[N];
    struct E{
    	int l,to;
    }e[N<<1];
    void dfs1(int x,int f){
    	sz[x]=1,sn[x]=0;
    	for(int i=h[x];i;i=e[i].l){
    		int to=e[i].to;
    		if(to==f)continue;
    		dfs1(to,x);
    		sz[x]+=sz[to];
    		if(sz[to]>sz[sn[x]])sn[x]=to;
    	}
    }
    struct DP{
    	int *dp[2];
    	int sz;
    	void init(){
    		sz=1;
    		dp[0]=(int*)malloc(sizeof(int)*(m+1));
    		dp[1]=(int*)malloc(sizeof(int)*(m+1));
    		dp[0][1]=1;
    		dp[1][1]=2;
    	}
    };
    void Min(int &x,int y){(x>y)&&(x=y);}
    void merge(DP &a,DP b){
    	for(int i=min(m,a.sz+b.sz);i>a.sz;i--)a.dp[0][i]=a.dp[1][i]=inf;
    	for(int i=a.sz;i;i--){
    		int w0=a.dp[0][i],w1=a.dp[1][i];
    		a.dp[0][i]=a.dp[1][i]=inf;
    		for(int j=b.sz;j;j--){
    			if(i+j<=m)Min(a.dp[0][i+j],w0+b.dp[0][j]+i*j);
    			Min(a.dp[0][i],w0+b.dp[1][j]);
    			if(i+j<=m)Min(a.dp[1][i+j],w1+b.dp[1][j]+2*i*j);
    			Min(a.dp[1][i],w1+b.dp[0][j]);
    		}
    	}
    	a.sz=min(m,a.sz+b.sz);
    }
    DP dfs2(int x,int f){
    	DP res;
    	if(!sn[x]){
    		res.init();
    		return res;
    	}else {
    		DP tmp=dfs2(sn[x],x);
    		res.init();
    		merge(res,tmp);
    		free(tmp.dp[0]);
    		free(tmp.dp[1]);
    	}
    	for(int i=h[x];i;i=e[i].l){
    		int to=e[i].to;
    		if(to==f||to==sn[x])continue;
    		DP tmp=dfs2(to,x);
    		merge(res,tmp);
    		free(tmp.dp[0]);
    		free(tmp.dp[1]);
    	}
    	return res;
    }
    void slv(){
    	scanf("%d",&n);
    	m=sqrt(n);
    	for(int i=1;i<=n;++i)h[i]=0;
    	for(int i=1;i<n;++i){
    		int x,y;
    		scanf("%d %d",&x,&y);
    		e[i<<1].to=x;e[i<<1].l=h[y];h[y]=i<<1;
    		e[i<<1|1].to=y;e[i<<1|1].l=h[x];h[x]=i<<1|1;
    	}
    	dfs1(1,0);
    	DP res=dfs2(1,0);
    	int ans=inf;
    	for(int i=1;i<=m;++i)Min(ans,min(res.dp[0][i],res.dp[1][i]));
    	printf("%lld\n",1ll*n*(n+1)-ans);
    	free(res.dp[0]);
    	free(res.dp[1]);
    }
    int main(){
    	scanf("%d",&T);
    	while(T--)slv();
    	return 0;
    }

