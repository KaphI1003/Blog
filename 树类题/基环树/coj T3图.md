# [模拟赛](https://www.cometoj.com/problem/1797)
#### 【题目描述】
小 X 出了一场模拟赛，一共有 n 个人参加。比赛的满分是 m，并且每个人的分数都是非负整数。

比赛开始之前，这 n 个人聚在一起装弱。为了使自己的话更具有说服力，这 n 个人中的每一个人都立了一个形如某人一定会吊打我 x 分的狠话。注意，这里“甲吊打乙 x 分”是指，甲的分数至少比乙的分数高 x 分。

这样，一共就会有 n 句狠话。

作为出题人，小 X 想知道，无论比赛成绩如何，至少会有多少句狠话不成立。

$n\le 2·10^5，m\le 10^8$

### 分析：
首先，我们来考虑一下$O(n^2)$的做法。
#### $O(n^2)$
很明显，我们要做的就是在一片基环树中删去最少的边，让剩下的树中直径没有超过m的。那么我们可以直接进行拓扑来维护信息，每次当链的长度大于m时直接切，这个贪心比较简单这里就不再赘述。

环的情况我们可以枚举把环的那一条边断开，再当链处理。

##### 时间复杂度：$O(n^2)$

#### 100分

那么我们的复杂度就是累积在枚举环从哪断开，这里我们有一个可以只断一条边的办法。不过不是原创，是从[充电时刻表](https://hihocoder.com/problemset/problem/1544)中得到的。

这里可能讲得不是很清楚，因为本人理解官方含糊不清的题解用了很长时间...

我们可以单调队列维护从环上一个点出发，最远可以到哪里（可以见官方题解做法）。剩下就是只拎一条边断和正确性的证明。

我们对于每一个点跟它最远能到的点的下一个点(及下一条链的起点)，连一条边，这两个点记作i,f[i]，这样我们就有得到了一片新的基环树森林（基环树森林套基环树森林？！！）。我们就可以直接在任意一个基环树中的环上的任意一条边断开算就可以了。

##### 正确性证明：
我们先画一个圆，圆上顺时针排布 1,2,3...,环的总点数。然后我们把上面所得到的基环树森林给它放在这个圆中(如下图)。
![无标题.png](https://i.loli.net/2019/08/19/GQHwNCchYd2MxFK.png)

并且下面的这种情况是不会出现的，因为f[i]随i单调递增。
![无标题2.png](https://i.loli.net/2019/08/19/vZuSsx9ocVG57Ib.png)

可以发现，我们的答案就是沿边跑一次环所经过的边数，回到第一张图，我们可以发现图中的环是（1，3，5，6）。按照环上的点把图划分成4段弧(起点开终点闭)，接着我们就可以发现**所有的点沿边走一次只能从一段弧走向另一段弧或走不出原来的弧**，例如以圆中的弧（3,5]出发，只能到[5,6],(因为3 -> 5, 5 ->6 )，所以要遍历一次图也至少需要走环的边数次，但是我们最后是要回到或超过起点，所以我们任选一条边断开的结果可能是环的边数或环的边数+1(如从图中2出发)，所有的环算出来的答案都是一样的。

如果上面的的证明方法没有看懂，别担心，这还有一份：

设我们从$p_1$出发，依次经过$p_2,p_3,...,p_{k-1},p_k$,那么我们考虑一下$p_k$的范围是多少。由于我们只有在超过或达到起点时停止，所以$p_k$至少得是$p_1$,而$p_{k-1}$最远只能到$p_2$(不然就会和$p_1$产生上图的错误情况)，所以$p_k$的范围就是[p1,p2] 
(闭区间)。接下来我们再考虑从$p_2$出发，当$p_k$是$p_2$时，很明显，我们要跑得次数就是从$p_1$出发跑得次数-1;而其他情况答案与从$p_1$出发相同(从$p_k$总能一步到$p_2$)。

那么我们就又得出了一个结论：

**一个点所指向的点的答案不会比这个点差**

转换成数学公式就是：$times(f[i])\leq times(i)$

照这样我们选一个点就一直向前跑，在内向基环树中就会到环上。放在环上，我们就得到

$times(a1)\leq times(a_2)\leq times(a_3)...\leq times(a_{cnt})\leq times(a_1)$

所以

$times(a_1)= times(a_2)= times(a_3)...= times(a_{cnt})= times(a_1)$

那么我们随便选一个点跑就可以了。

但是可能就会有人认为**环的边数就是答案，这是错的**。下面这个图中就只用跑三次。

![6.png](https://i.loli.net/2019/08/19/dm3uB4sinEFwTHj.png)

如果要证这样的情况我们可以把点都复制一份加在后面，这样建出来的图就是一个五边形，只不过只跑半个环。证明就跟上面一样。

所以，结束了。

##### 时间复杂度：$O(n)$
##### 空间复杂度：$O(n)$
##### code：
```
#include<bits/stdc++.h>
using namespace std;
const int N=2e5+5;
int n,m,to[N],s[N],f[N],q[N<<1],ql,dp[N],w[N],ans,T,dl,hd,sr[N],Q[N<<1],Ql,nu[N];
long long su[N<<1],dq[N<<1];
const long long inf=-9e18;
void go(int st){
	int nw=st,nt,sum=0;
	while(1){
		ans++;nt=f[nw];
		if(nu[nt]<nu[nw])sum+=ql+nu[nt]-nu[nw];
		else sum+=nu[nt]-nu[nw];
		if(sum>=ql)return;
		nw=nt;
	}
}
void so(){
	Ql=0;
	for(int i=1;i<=ql;++i){
		dq[i]=dq[ql+i]=inf;
		nu[q[i]]=i;
		su[i]=w[q[i-1]]+su[i-1];
		sr[q[ql+i]=q[i]]=0;
	}
	su[ql+1]=su[ql]+w[q[ql]];
	for(int i=2;i<=ql;++i)su[ql+i]=w[q[i-1]]+su[ql+i-1];
	int z=1;hd=dl=1;
	dq[1]=dp[q[1]];
	long long g;
	for(int i=1;i<=ql;++i){//单调队列找f[i] 
		while(dq[hd]+su[z]<=m){
			z++;
			if(z==i+ql){ans++;return;}
			g=dp[q[z]]-su[z];
			while(dl>=hd&&dq[dl]<g)dq[dl--]=inf;
			dq[++dl]=g;
		}
		sr[f[q[i]]=q[z]]++; 
		if(dq[hd]==dp[q[i]]-su[i])hd++;
	}
	for(int i=1;i<=ql;++i){//拓扑搞环 
		if(!sr[q[i]])Q[++Ql]=q[i];
	}
	for(int i=1;i<=Ql;++i){
		int nw=Q[i];
		if(!(--sr[f[nw]]))Q[++Ql]=f[nw];
	}
	for(int i=1;i<=ql;++i){
		if(sr[q[i]]){//随便拿一个环上一个点跑 
			go(q[i]);
			return;
		}
	}
}
void mn(){
	memset(s,0,sizeof(s));
	memset(dp,0,sizeof(dp));
	ql=ans=0;
	scanf("%d %d",&n,&m);
	for(int i=1;i<=n;++i){
		scanf("%d %d",&to[i],&w[i]);
		s[to[i]]++;
	}
	for(int i=1;i<=n;++i){
		if(!s[i])q[++ql]=i;
	}
	for(int i=1;i<=ql;++i){
		int nw=q[i];
		if(dp[nw]+w[nw]>m)ans++;
		else dp[to[nw]]=max(dp[to[nw]],dp[nw]+w[nw]);
		s[to[nw]]--;
		if(!s[to[nw]])q[++ql]=to[nw];
	}
	for(int i=1;i<=n;++i){
		if(s[i]){//拿出原图中的环 
			ql=1;q[1]=i;
			int nw=i;s[i]--;
			while(s[to[nw]]){
				nw=to[nw];
				q[++ql]=nw;
				s[nw]--;
			}
			so();
		}
	}
	printf("%d\n",ans);
}
int main(){
	scanf("%d",&T);
	while(T--)mn();
}
```