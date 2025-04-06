# [# Casual Dancers](https://qoj.ac/contest/825/problem/2618)
### 简要题意
数轴上有三个点 $x_1,x_2,x_3$。有 $k$ 次操作，每次操作从三个点中均匀随机选一个点，有 $\frac{p}{100}$ 的概率该点坐标加一，否则坐标减一。问最后三个点中的最远两个点的距离期望。

$-10^5 \le x_i \le 10^5$ ，$1 \le k \le 2 \cdot 10^5$，$0 \le  p \le 100$。

### 简要题意
遇到 $\max/\min$ 的题，可以考虑将 $\max/\min$ 拆成绝对值来做。

$\max\{x_1,x_2,x_3\} - \min\{x_1,x_2,x_3\}$ = $\frac{1}{2} \cdot (|x_1-x_2|+|x_2-x_3|+|x_3-x_1|)$。

我们只需要求出两个点最后的期望距离即可。

对于两个点之间的距离，我们可以发现一次操作它有 $\frac{1}{3}$ 概率加一， $\frac{1}{3}$ 概率减一， $\frac{1}{3}$ 概率不变。我们需要 $k$ 次操作之后的结果。考虑使用生成函数，一次操作就是乘上 $\frac{1}{3} x^{-1} + \frac{1}{3} + \frac{1}{3} x$。因为负数项不好处理，我们改写为 $x(\frac{1}{3} + \frac{1}{3} x +\frac{1}{3} x^2)$。将括号中的式子用分治NTT乘上 $k$ 遍即可。

这样我们就求出两个点之间距离变化量的概率了，不过距离可能变成负数，需要加上 abs 。

本题与 $p$ 无关。

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=4e6+5,M=998244353;
int x,y,z,k,p,R[N],ans;
void ck(int &x){(x>=M)&&(x-=M);}
void kc(int &x){(x<0)&&(x+=M);}
int pw(int x,int t){
    int rs=1;
    for(;t;t>>=1,x=1ll*x*x%M)if(t&1)rs=1ll*rs*x%M;
    return rs;
}
void NTT(vector<int> &a,int tp){
	int up=a.size();
	for(int i=0;i<up;++i)R[i]=(R[i>>1]| ((i&1)? up:0))>>1;
	for(int i=1;i<up;++i)if(i<R[i])swap(a[i],a[R[i]]);
	for(int i=1;i<up;i<<=1){
		int Wn=pw(tp? 3:((M+1)/3),(M-1)/i/2);
		for(int o=0;o<up;o+=i){
			for(int w=1,U=o+i;o<U;++o,w=1ll*w*Wn%M){
				int x=a[o],y=1ll*w*a[o+i]%M;
				ck(a[o]=x+y);kc(a[o+i]=x-y);
			}
		}
	}
	if(!tp){
		int k=pw(up,M-2);
		for(int i=0;i<up;++i)a[i]=1ll*a[i]*k%M;
	}
}
vector<int> slv(int l,int r){
    // printf("__%d %d\n",l,r);
	if(l==r){
		vector<int>A;
		A.push_back(332748118);
		A.push_back(332748118);
        A.push_back(332748118);
        A.push_back(0);
		return A;
	}
	int mid=(l+r)>>1;
	vector<int> x=slv(l,mid),y=slv(mid+1,r);
	int sz=max(x.size(),y.size())<<1;
	x.resize(sz),y.resize(sz);

	NTT(x,1),NTT(y,1);
	for(int i=0;i<sz;++i)x[i]=1ll*x[i]*y[i]%M;
	NTT(x,0);
	return x;
}
vector<int>rs;
void clc(int d){
    for(int i=0;i<=2*k;++i){
        ans=(ans+1ll*abs(d+i-k)*rs[i])%M;
    }
}
int main(){
    scanf("%d %d %d %d %d",&x,&y,&z,&k,&p);
    // puts("___");
    rs=slv(1,k);
    clc(abs(x-y)),clc(abs(y-z)),clc(abs(z-x));
    // puts("___");
    ans=1ll*ans*pw(2,M-2)%M;
    printf("%d\n",ans);
}
```
