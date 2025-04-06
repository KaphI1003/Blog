# [[HDU 5471]Count the Grid](https://vjudge.net/problem/HDU-5471)

这可是容斥神题！—————————小C

### 分析：

如果我们就表面的理解题目，只考虑一个矩阵，要对最大值进行操作，那么就是至少要有一个值为最大值，直接求满足的方案数，那么就会直接暴毙。所以我们要换一个角度去看，如果我们求的是不满足的方案数，那么就是让矩阵中所有的值都小于最大值。明显，求不满足的方案更加简单，也更快，所以我们考虑从求不满足的方案来容斥得到满足的方案数。

各种解法：

#### 1.自己的较low做法

首先，我们可以把坐标全部离散化，把可以一起考虑的方格全都放在一起考虑，那么每一个方格可以取得最大值就是覆盖在上面的矩阵的值中的最小者，于是我们可以预处理出每一个方格的最大值是多少，然后我们选一个矩阵作为源矩阵，然后就可以通过求让源矩阵满足条件而其他一些矩阵一定不满足（让一个矩阵一不满足的办法就是让这个矩阵覆盖到的方格都取不到矩阵的权值，即用矩阵最大值-1去更新方格图）的方案数。设$x$为一个二进制状态数，$S_{x}$为满足原矩阵且不满足$x$中0位的矩阵的方案数。那么我们就可以直接求出每一个$S$来求($S$就是上面让源矩阵满足条件而其他一些矩阵一定不满足的方案数)，接着我们就可以通过容斥来求答案了。设$dp_{x}$为满足$x$中1位且不满足0位的方案数，那么我们就可以得到

$$
dp_x=S_x-\sum dp_i,i\in x
$$

最后的答案就是$dp_{1111....}$

时间复杂度：$O(n^2 2^n)$

code：
```
#include<bits/stdc++.h>
using namespace std;
const int N=22,M=1e9+7;
int n,m,h,w,t,x[N],y[N],xl,yl,f1;
int mp[N][N],Mp[N][N],g[N][N],dp[1024];
void ck(int &x){if(x>=M)x-=M;}
void kc(int &x){if(x<0)x+=M;}
struct P{
	int x1,x2,y1,y2,v;
}a[15];
int pw(int a,int b){
	int rs=1;
	for(int i=1;i<=b;i<<=1){
		if(i&b)rs=1ll*rs*a%M;
		a=1ll*a*a%M;
	}
	return rs;
}
void Calc(int nw){
	int q[15],l=0;
	for(int i=2,o=2;i<=n;++i,o<<=1){
		if(!(o&nw))q[++l]=i;
	}
	for(int i=1;i<=xl;++i)for(int o=1;o<=yl;++o)Mp[i][o]=mp[i][o];
	for(int I=1;I<=l;++I){
		int i=q[I];
		for(int o=a[i].x1;o<=a[i].x2;++o){
			for(int u=a[i].y1;u<=a[i].y2;++u){
				Mp[o][u]=min(Mp[o][u],a[i].v-1);
			}
		}
	}
	int sum=1,rs=0;
	for(int i=1;i<=xl;++i){
		for(int o=1;o<=yl;++o){
			sum=1ll*sum*pw(Mp[i][o],g[i][o])%M;
		}
	}
	for(int i=a[1].x1;i<=a[1].x2;++i){
		for(int o=a[1].y1;o<=a[1].y2;++o){
			if(Mp[i][o]==a[1].v){
				rs+=g[i][o];
			}
		}
	}
	int ka=1ll*f1*(a[1].v-1)%M;
	kc(ka=1-pw(ka,rs));
	ka=1ll*ka*sum%M;
	dp[nw]=ka;
}
int main(){
	scanf("%d",&t);
	for(int ca=1;ca<=t;++ca){
		memset(dp,0,sizeof(dp));
		int l=0,L=0;
		scanf("%d %d %d %d",&h,&w,&m,&n);
		for(int i=1;i<=n;++i){
			scanf("%d %d %d %d %d",&a[i].x1,&a[i].y1,&a[i].x2,&a[i].y2,&a[i].v);
			a[i].x1--;a[i].y1--;
			if(a[i].x1)x[++l]=a[i].x1;
			if(a[i].y1)y[++L]=a[i].y1;
			x[++l]=a[i].x2;y[++L]=a[i].y2;
		}
		x[++l]=h;y[++L]=w;
		sort(x+1,x+l+1);sort(y+1,y+L+1);
		xl=unique(x+1,x+l+1)-x-1;yl=unique(y+1,y+L+1)-y-1;
		for(int i=1;i<=xl;++i){
			for(int o=1;o<=yl;++o){
				g[i][o]=(x[i]-x[i-1])*(y[o]-y[o-1]);
				mp[i][o]=m;
			}
		}
		f1=pw(a[1].v,M-2);
		for(int i=1;i<=n;++i){
			a[i].x1=lower_bound(x,x+xl+1,a[i].x1)-x;
			a[i].y1=lower_bound(y,y+yl+1,a[i].y1)-y;
			a[i].x2=lower_bound(x,x+xl+1,a[i].x2)-x;
			a[i].y2=lower_bound(y,y+yl+1,a[i].y2)-y;
			a[i].x1++;a[i].y1++;
			for(int o=a[i].x1;o<=a[i].x2;++o){
				for(int u=a[i].y1;u<=a[i].y2;++u){
					mp[o][u]=min(mp[o][u],a[i].v);
				}
			}
		}
		const int k=1<<n;
		for(int i=1;i<k;i+=2){
			Calc(i);
			for(int o=(i-1)&i;o;o=(o-1)&i){
				kc(dp[i]-=dp[o]);
			}
		}
		printf("Case #%d: %d\n",ca,dp[k-1]);
	}
}

```
#### 2.来自[CalvinJin](https://blog.csdn.net/qq_39992190/article/details/79049336)学长的超快解法

当我到网上搜刮优质做法时发现了学长的超快解法，发现自己原来那个源矩阵其实只是用来增加复杂度用的，而且也发现学长的做法不是用矩阵去找点，而是直接用点找矩阵，求出每一个点可以控制哪些矩阵，可以控制的矩阵的权值就是相同的，所以就在权值相同的矩阵中容斥，不过原理是一样的，通过求不满足得到满足，以统计单块得到交集，再由交集容斥得到并集，接着直接计算不满足的方案数就可以了。最主要的是下面的式子：

**AB 同时满足的方案数=全部方案数-A不满足的方案数-B不满足的方案数+AB同时不满足的方案数**

##### 时间复杂度: $O(3^n+n^3)$
##### code : 
```
#include<bits/stdc++.h>
using namespace std;
const int N=22,M=1e9+7;
int n,m,h,w,t,x[N],y[N],xl,yl,v[N],ans,j[N][2048],b[N][2048],id[N],nu[2048],sz[N];
void ck(int &x){if(x>=M)x-=M;}
void kc(int &x){if(x<0)x+=M;}
struct P{
	int x1,x2,y1,y2,v;
}a[15];
int pw(int a,int b){
	int rs=1;
	for(int i=1;i<=b;i<<=1){
		if(i&b)rs=1ll*rs*a%M;
		a=1ll*a*a%M;
	}
	return rs;
}
bool ck(P a,P b){
	return a.x1<=b.x1&&a.x2>=b.x2&&a.y1<=b.y1&&a.y2>=b.y2;
}
int main(){
	scanf("%d",&t);
	for(int ca=1;ca<=t;++ca){
		memset(j,0,sizeof(j));
		memset(b,0,sizeof(b));
		memset(sz,0,sizeof(sz));
		int l=0,L=0;ans=1;
		scanf("%d %d %d %d",&h,&w,&m,&n);
		for(int i=1;i<=n;++i){
			scanf("%d %d %d %d %d",&a[i].x1,&a[i].y1,&a[i].x2,&a[i].y2,&a[i].v);
			a[i].x1--;a[i].y1--;
			if(a[i].x1)x[++l]=a[i].x1;
			if(a[i].y1)y[++L]=a[i].y1;
			x[++l]=a[i].x2;y[++L]=a[i].y2;
			v[i]=a[i].v;
		}
		x[++l]=h;y[++L]=w;
		sort(x+1,x+l+1);sort(y+1,y+L+1);sort(v+1,v+n+1);
		xl=unique(x+1,x+l+1)-x-1;yl=unique(y+1,y+L+1)-y-1;
		int vl=unique(v+1,v+n+1)-v-1;
		for(int i=1;i<=n;++i){
			a[i].v=lower_bound(v+1,v+vl+1,a[i].v)-v;
			id[i]=sz[a[i].v]++;
		}
		for(int i=1;i<=xl;++i){
			for(int o=1;o<=yl;++o){
				int nw=m+1,d=0;
				P g=(P){x[i-1],x[i],y[o-1],y[o],0};
				for(int u=1;u<=n;++u){
					if(ck(a[u],g)){
						if(a[u].v<nw){
							nw=a[u].v;
							d=1<<id[u];
						}
						else if(nw==a[u].v)d|=1<<id[u];
					}
				}
				if(nw>m){
					ans=1ll*ans*pw(m,(x[i]-x[i-1])*(y[o]-y[o-1]))%M;
				}
				else j[nw][d]+=(x[i]-x[i-1])*(y[o]-y[o-1]);
			}
		}
		const int nk=(1<<n)-1;
		nu[0]=-1;
		for(int i=1;i<=nk;++i)nu[i]=-nu[i^(i&-i)];
		for(int i=1;i<=vl;++i){
			const int up=(1<<sz[i])-1;
			int al=0;
			for(int o=1;o<=up;++o){
				al+=j[i][o];
				for(int u=o;u;u=(u-1)&o){
					b[i][u]+=j[i][o];
				}
			}
			int rs=0;
			for(int o=up;o>=0;o--){
				j[i][o]=0;
				for(int u=o;u;u=(u-1)&o){
					ck(j[i][o]+=nu[u]*b[i][u]);
				}
				kc(rs-=1ll*nu[o]*pw(v[i]-1,j[i][o])*pw(v[i],al-j[i][o])%M);
				ck(rs);
			}
			ans=1ll*ans*rs%M;
		}
		printf("Case #%d: %d\n",ca,(ans+M)%M);
	}
}
```