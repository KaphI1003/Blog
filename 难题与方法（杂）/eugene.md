# eugene
#### source: 2019长沙中学模拟考

#### 分析：
我们先来分析一下只有边权1的图该怎么解决。

观察一个环，只要我们让所有的边都顺时针或逆时针指向，就可以忽略这个环的影响。同时去掉这个环不会影响答案（因为去环后剩下来的只能是一片森林，而森林必有解）。但是这方法并不好实现，而且容易写崩，所以我们要考虑换个思路或进一步挖掘如何使用环的性质。

假如我们把环上一条边去掉，按照原来环的性质，**我们可以把这个断裂的环，等价成那条被去掉的边**，方向取反。但我们是无向图，所以就可以暂时不考虑方向。


一个三角形也是一个环，**那么当我们有两条有共同顶点边的时候，我们可以把它们合成一条边**，然后继续合并。最后一就是一个二分图的完全匹配，这就一定有解，接着我们再把边全部还原就可以了。

![无标题.png](https://i.loli.net/2019/10/13/JKrTmz9Zj2a8hYD.png)

接下来我们在考虑有边权2的图，相同边权的边和并之后，边权为1的边构造出来的还是一个二分图完全匹配。而边权2构造出来的图只是一个普通的二分图匹配。然后考虑把两个图合并，合并之后的图中就只有环和链，而环也只有偶环（不然就可以合并）。那么我们随意按一个方向捋顺就可以了。

所以这道题输出-1是没有分的（~~滑稽~~）。

##### 时间复杂度 $O(n+m)$
##### code :
```
#include<bits/stdc++.h>
using namespace std;
const int N=1e6+5;
int n,m,to[2][N],g,el;
bool v[N<<1];
struct P{
	int x,y,f,di,sn1,sn2;
}e[N<<1];
void dw(const int nw){//把新边还原成旧边 
	const int sn1=e[nw].sn1,sn2=e[nw].sn2,di=e[nw].di;
	if(!sn1)return;
	e[sn1].di=(e[nw].x==e[sn1].x)? di:(!di);
	e[sn2].di=(e[nw].y==e[sn2].y)? di:(!di);
	dw(sn1);dw(sn2);
}
void Ad(const int nw){//利用二叉树来保存边 
	const int x=e[nw].x,y=e[nw].y;
	if(to[g][x]){
		const int v=to[g][x],p2=(e[v].x==x)? e[v].y:e[v].x;
		to[g][x]=to[g][p2]=0;
		if(y==p2){//成环直接不继续考虑 
			e[nw].di=1;
			e[v].di=(e[v].x==x)? 0:1;
		}else {
			e[++el]=(P){y,p2,0,0,nw,v};
			e[nw].f=e[v].f=el;
			Ad(el);
		}
	}
	else if(to[g][y]){
		const int v=to[g][y],p2=(e[v].y==y)? e[v].x:e[v].y;
		to[g][y]=to[g][p2]=0;
		if(x==p2){
			e[nw].di=1;
			e[v].di=(e[v].y==y)? 0:1;
		}else {
			e[++el]=(P){x,p2,0,0,nw,v};
			e[nw].f=e[v].f=el;
			Ad(el);
		}
	}else to[g][x]=to[g][y]=el;
}
void dfs(int nw,int f,int d){//捋顺边 
	v[nw]=1;
	e[nw].di=(e[nw].x==f)? d:!d;
	const int p=(e[nw].x==f)? e[nw].y:e[nw].x,sn1=to[0][p],sn2=to[1][p];
	if(!v[sn1])dfs(sn1,p,d);
	else if(!v[sn2])dfs(sn2,p,d);
}
int main(){
	int x,y;
	scanf("%d %d",&n,&m);
	for(int i=1;i<=m;++i){
		scanf("%d %d %d",&x,&y,&g);
		e[++el]=(P){x+1,y+1,0,0,0,0};
		g--;Ad(el);
	}
	v[0]=1;
	for(int i=1;i<=n;++i){
		if(!v[to[0][i]]){
			dfs(to[0][i],i,1);
			if(!v[to[1][i]])dfs(to[1][i],i,0);
		}else if(!v[to[1][i]])dfs(to[1][i],i,1);
	}
	for(int i=1;i<=el;++i)if(!e[i].f)dw(i);
	for(int i=1;i<=el;++i)if(!e[i].sn1)printf("%d",e[i].di);
}
```