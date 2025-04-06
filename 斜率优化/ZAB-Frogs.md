# ZAB-Frogs

[题目链接](http://122.228.121.148:8222/judge/problem.php?cid=1114\&pid=3)

#### 题目描述

Task: Frogs 一群青蛙正在摧毁Byteotia所有的庄稼. 一个叫Byteasar的农夫决定使用一种放在田里的奇特的"scarefrogs"来吓跑他们, 所有的青蛙在跳跃过程中都尽量使自己离他们越远越好, 即是让自己离最近的scarefrog越远越好. Byteasar的田是块矩形的土地. 青蛙们跳跃的方向平行于坐标轴并且每次跳跃的距离为1. 一条跳跃路线的scarefrogs-距离为路径上所有点距离所有scarefrogs的最近距离. Byteasar已经知道青蛙们的出发位置和目的地位置, 所以他在田里放置了若干个scarefrogs. 他请求你帮忙写一个程序找一条路径使得该路径的scarefrogs-距离最大.

#### 输入格式

第一行包含两个整数: wx 和 wy 分别表示田地的宽和长( 2 <= wx, wy<= 1 000). 第二行包含四个整数: px, py, kx 和 ky 其中 (px, py) 为青蛙的起始位置, (kx, ky) 为目的地位置( 1 <= px, kx <= wx, 1<= py, ky<= wy). 第三行一个整数 n – 表示scarefrogs的总数( 1<= n<= wx . wy). 接下来n 行描述所有scarefrogs的坐标. 坐标用xi 和 yi 来描述第ith个 scarefrog的位置 ( 1 <= xi<= wx, 1<= yi<= wy). 没有两个scarefrogs在同一个位置出现并且不会出现在(px, py) nor (kx, ky)上.

#### 输出格式

一个整数代表目标路径scarefrog距离的平方. 如果青蛙不可避免遇到某些scarefrog那么答案为0.

##### 样例输入

5 5

1 1 5 5

2

3 3

4 2

##### 样例输出

4

#### 分析：

一个惊蛙（xa,ya）对一个坐标（xb,yb）的影响是·$（xa-xb）^2+(ya-yb)^2$
那么如果一个惊蛙比另一个(xc,yc)要更优，就需要满足：

$$
xb<\dfrac{xc^2-xa^2+(yc-yb)^2-(ya-yb)^2}{2xc-2xa}
$$

然后用按斜率单调的队列来维护就行.

时间复杂度： $O(n^2log_2(n))$

#### code:

```cpp
#include<bits/stdc++.h>
using namespace std;
int n,m,g[1005][1005],q[1005],b[1005],s[1005][1005],sx,sy,ex,ey,W,L,z[1005],k[4][2]={1,0,0,1,-1,0,0,-1};
vector<int>V[1005];
bool v[1005],vi[1005][1005];
bool zc(int x,int y){
	return x>0&&x<=n&&y>0&&y<=m&&!vi[x][y];
}
struct P{
	int x,y;
}a;
queue<P>Q;
bool ck(int w){
	memset(vi,0,sizeof(vi));
	while(!Q.empty())Q.pop();
	Q.push({sx,sy});
	vi[sx][sy]=1;
	if(vi[ex][ey])return 1;
	while(!Q.empty()){
		a=Q.front();Q.pop();
		for(int i=0;i<4;++i){
			if(zc(k[i][0]+a.x,k[i][1]+a.y)&&s[k[i][0]+a.x][k[i][1]+a.y]>=w){
				vi[k[i][0]+a.x][k[i][1]+a.y]=1;
				if(vi[ex][ey])return 1;
				Q.push({k[i][0]+a.x,k[i][1]+a.y});
			}
		}
	}
	return 0;
}
int main(){
	int x,y;
	scanf("%d %d %d %d %d %d %d",&n,&m,&sx,&sy,&ex,&ey,&W);
	for(int i=1;i<=W;++i){
		scanf("%d %d",&x,&y);
		V[x].push_back(y);
	}
	for(int i=1;i<=n;++i){
		if(!V[i].size())v[i]=1;
		else {
			sort(V[i].begin(),V[i].end());
			b[++L]=i;
		}
	}
	for(int o=1;o<=m;++o){
		for(int i=1;i<=L;++i){
			while(z[b[i]]!=V[b[i]].size()-1&&abs(V[b[i]][z[b[i]]]-o)>=abs(V[b[i]][z[b[i]]+1]-o))z[b[i]]++;
			g[b[i]][o]=b[i]*b[i]+(V[b[i]][z[b[i]]]-o)*(V[b[i]][z[b[i]]]-o);
		}
	}
	for(int o=1;o<=m;++o){
		int l=1,sz=0,r=0;
		for(int i=1;i<=n;++i){
			if(!v[i]){
				while(sz>1){
					if(1ll*(g[q[r]][o]-g[q[r-1]][o])*(i-q[r])>=1ll*(g[i][o]-g[q[r]][o])*(q[r]-q[r-1])){
						r--;sz--;
					}else break;
				}
				q[++r]=i;sz++;
			}
			while(sz>1){
				if(g[q[l]][o]-2*i*q[l]>=g[q[l+1]][o]-2*i*q[l+1]){
					l++;sz--;
				}else break;
			}
			if(sz){
				s[i][o]=g[q[l]][o]-2*i*q[l]+i*i;
			}
			else s[i][o]=1e9;
		}
		l=1;sz=r=0;
		for(int i=n;i;--i){
			if(!v[i]){
				while(sz>1){
					if(1ll*(g[q[r]][o]-g[q[r-1]][o])*(i-q[r])<=1ll*(g[i][o]-g[q[r]][o])*(q[r]-q[r-1])){
						r--;sz--;
					}else break;
				}
				q[++r]=i;sz++;
			}
			while(sz>1){
				if(g[q[l]][o]-2*i*q[l]>=g[q[l+1]][o]-2*i*q[l+1]){
					l++;sz--;
				}else break;
			}
			if(sz)s[i][o]=min(g[q[l]][o]-2*i*q[l]+i*i,s[i][o]);
		}
	}
	int l=0,r=min(s[sx][sy],s[ex][ey]),mid,ans=0;
	while(l<=r){
		int mid=l+r>>1;
		if(ck(mid)){
			ans=mid;
			l=mid+1;
		}
		else r=mid-1;
	}
	printf("%d\n",ans);
}
```

