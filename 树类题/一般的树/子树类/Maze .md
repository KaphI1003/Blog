### 题目描述：
有n个房间，由n-1条隧道连通起来，从结点1出发，开始走，在每个结点i都有3种可能： 
1.被杀死，回到结点1处（概率为ki） 
2.找到出口，走出迷宫 （概率为ei） 
3.和该点相连有m条边，随机走一条 求：走出迷宫所要走的边数的期望值。

#### 输入
第一行是一个整数T（T<=30），表示有T组测试数据 
在每一组测试数据的开头有一个整数N(2<=N<=10000)，表示这个测试数据的房间数。 
然后有N-1对整数X,Y，(1<=X,Y<=N,X!=Y)，表示X号房间和Y号房间有一条路。最后，有N对整数ki和ei(0<=ki,ei<=100,ki+ei<=100,k1=e1=0),表示在第i号房间，被杀死回到1号房间或者是走出迷宫的概率。
#### 输出
对于每组测试数据，应输出一行“Case k:”，k是这组测试数据的编号。 
然后输出在他走出迷宫前，走过的道路预计数量。 
误差可以在0.0001之内。如果他不能从迷宫中逃出，输出“impossible”
##### Sample Input
3

3

1 2

1 3

0 0

100 0

0 100

3

1 2

2 3

0 0

100 0

0 100

6

1 2

2 3

1 4

4 5

4 6

0 0

20 30

40 30

50 50

70 10

20 60

##### Sample Output
Case 1: 2.000000

Case 2: impossible

Case 3: 2.895522

### 分析：
我们可以马上推出它的动态转移方程为
$$
dp[i]=k[i]*dp[1]+(1-k[i]-e[i])*({dp_{i_{to}}}+1)
$$
其中$ dp_{i_{to}} $表示i点可以去到的点的dp值的平均数。

但是，我们虽然有n个n个未知数的dp方程，但是我们还是无法记录和处理，那么我们就就要柿子就挑软的捏，叶子节点的状态转移方程是最简单的，只有四个量要记——**自己dp值的系数，自己父亲dp值的系数，根dp值的系数，常数**。

接下来，我们就可以直接把叶子节点的信息转移到父亲节点上，然后这个点就可以忽略了。当一个节点的所有子节点都转移了，就可以把它也当做一个子节点对待，最后在根处解决。时间复杂度O（n）;

#### 代码如下：
```
#include<bits/stdc++.h>
using namespace std;
bool fl;
struct P{
	int l,to;
}e[20005];
const double J=1e-10;
int T,n,s[10005],h[10005],el;
double k[10005],c[10005],dp[10005][3],g[10005];
void Ad(int x,int y){
	e[++el].to=y;
	e[el].l=h[x];
	h[x]=el;
	s[x]++;
}
void se(int x,int f){
	double z=dp[x][2]=c[x]/s[x];
	dp[x][1]=k[x];g[x]=c[x];
	for(int i=h[x];i;i=e[i].l){
		if(e[i].to==f)continue;
		se(e[i].to,x);
		if(fl)return;
		g[x]+=z*g[e[i].to];
		dp[x][1]+=z*dp[e[i].to][1];
		dp[x][0]+=z*dp[e[i].to][2];
	}
	if(fabs(1.0-dp[x][0])<J){
		fl=1;
		return;
	}
	dp[x][1]/=(1-dp[x][0]);
	dp[x][2]/=(1-dp[x][0]);
	g[x]/=(1-dp[x][0]);
}
int main(){
	int x,y;
	scanf("%d",&T);
	for(int I=1;I<=T;++I){
		fl=0;
		memset(h,0,sizeof(h));
		memset(s,0,sizeof(s));
		el=0;
		scanf("%d",&n);
		for(int i=1;i<n;++i){
			scanf("%d %d",&x,&y);
			Ad(x,y);Ad(y,x);
		}
		for(int i=1;i<=n;++i){
			scanf("%lf %lf",&k[i],&c[i]);
			dp[i][0]=dp[i][1]=dp[i][2]=g[i]=0;
			k[i]/=100.0;c[i]/=100.0;
			c[i]=1.0-k[i]-c[i];
		}
		se(1,0);
		if(fl){
			puts("impossible");
			continue;
		}
		dp[1][0]=1-dp[1][1];
		printf("Case %d: ",I);
		if(fabs(dp[1][0])<J)puts("impossible");
		else printf("%f\n",g[1]/dp[1][0]);
	}
}
```