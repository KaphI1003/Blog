# 树形背包DP
树形背包DP顾名思义就是在树上背包进行DP，一般来说都是解决有关于子树的最优无后性决策。一般的时间复杂度是O（n^2）， 虽然看着是O（n^3）的，但是树上每一对节点的值最多被算一次（因为算完之后他们就一体了）。
## 例题：苹果树
[题目](https://nanti.jisuanke.com/t/37250)

小 A 想从一棵有 n 个苹果（编号从 1 到 n）的树上摘苹果。第 i 个苹果的重量是 w_i 
​	
  ，小 A 拿得起的重量至多是 m，同时为了便于携带，他希望摘下来的苹果是连通的。

小 A 想带走尽可能多的苹果，请你帮他求出最多能带走多少苹果。

#### 输入格式
第一行两个整数 n,m，表示树上苹果的数量和小 A 最多能拿起的重量。

第二行 n 个整数，第 ii 个整数表示 w_iw 
i
​	
 。

接下来 n-1 行，每行两个整数 u_i,v_i 
​
​	
  ，表示 u_i
​	
  和 v_i
​	
  在同一条树枝上。

#### 输出格式
一行一个整数，表示小 A 最多能带走多少苹果。
对于 100% 的数据，保证 1≤n,m≤10^5
​
 1≤w_i≤10^5
  且 w_i
​	
  两两不同。

输出时每行末尾的多余空格，不影响答案正确性

##### 样例输入
5 8

5 2 3 4 1

1 2

1 3

2 4

2 5

##### 样例输出

3

对于这道题目，它的范围是超过正常树形DP的范围的，不过题目中还隐藏了一些条件，注意到w_i的范围是1e5且两两不相等，，m的也范围是1e5.那么计算之后可以 得出最多可以摘的苹果不超过447个，那么就可以只存摘前447个的最小花费，再DP复杂度就可以被接受了。

代码如下
```
#include<bits/stdc++.h>
using namespace std;
struct P{
	int l,to;
}e[200005];
const int k=447;
int n,m,d[100005][448],el,h[100005],sz[100005],w[100005],ans;
void Ad(int x,int y){
	e[++el].to=y;e[el].l=h[x];h[x]=el;
}
void se(int x,int f){
	d[x][1]=w[x];sz[x]=1;
	for(int i=h[x];i;i=e[i].l){
		if(e[i].to!=f){
			se(e[i].to,x);
			for(int o=min(sz[x],k);o;o--){
				for(int u=1;u<=min(sz[e[i].to],k);++u){
					if(o+u<=k)d[x][o+u]=min(d[x][o+u],d[x][o]+d[e[i].to][u]);
				}
			}
			sz[x]+=sz[e[i].to];
		}
	}
	for(int i=1;i<=min(sz[x],k);++i)if(d[x][i]<=m)ans=max(ans,i);
}
int main(){
	int x,y;
	scanf("%d %d",&n,&m);
	for(int i=1;i<=n;++i){
		scanf("%d",&w[i]);
	}
	for(int i=1;i<n;++i){
		scanf("%d %d",&x,&y);
		Ad(x,y);Ad(y,x);
	}
	memset(d,127,sizeof(d));
	se(1,0);
	printf("%d\n",ans);
}
```