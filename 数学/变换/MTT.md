# [MTT](https://www.luogu.com.cn/problem/P4245)

如果我们要求 $\mod M$ 的NTT，但是$M$没有限制，设卷积之后的一个数在没有取模的情况下最大为 $mx$，那么我们可以通过找几个可用的 NTT 模数来 NTT，使所有模数的$lcm$ 大于 $mx$，然后我们就可以使用中国剩余定理来反解出没取模的答案，最后在取模就行。

一般采用 469762049(2 ^ 26)，998244353(2 ^ 22)，1004535809(2 ^ 21)三个模数，三个数原根都是 3 。


```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=3e5+5;
const int M0=469762049,M1=998244353,M2=1004535809;
int n,m,p,a[N],b[N],A[3][N],B[3][N],R[N];
int pw(int x,int t,int M){
	int rs=1;
	for(;t;t>>=1,x=1ll*x*x%M)if(t&1)rs=1ll*rs*x%M;
	return rs;
}
void ck(int &x,int M){(x>=M)&&(x-=M);}
void kc(int &x,int M){(x<0)&&(x+=M);}
void NTT(int *a,int tp,int up,const int M){
	for(int i=1;i<up;++i)if(i<R[i])swap(a[i],a[R[i]]);
	for(int i=1;i<up;i<<=1){
		int Wn=pw(tp? 3:((M+1)/3),(M-1)/i/2,M);
		for(int o=0;o<up;o+=i){
			for(int w=1,U=o+i;o<U;++o,w=1ll*w*Wn%M){
				int x=a[o],y=1ll*w*a[o+i]%M;
				ck(a[o]=x+y,M);kc(a[o+i]=x-y,M);
			}
		}
	}
	if(!tp){
		int k=pw(up,M-2,M);
		for(int i=0;i<up;++i)a[i]=1ll*a[i]*k%M;
	}
}
typedef __int128 LL;
LL bb[10],mm[10];
LL ex_gcd(LL a,LL b,LL &x,LL &y){
	if(!b){
		x=1,y=0;
		return a;
	}
	LL g=ex_gcd(b,a%b,x,y);
	LL t=x;
	x=y,y=t-(a/b)*y;
	return g;
}
void Pt(LL x){
	int a[50]={},l=0,rs=0;
	while(x){
		a[++l]=x%10;
		x/=10;
	}
	for(int i=l;i;i--)rs=(rs*10ll+a[i])%p;
	printf("%d ",rs);
}

void ex_CRT(int n,LL *b,LL *m){//n跺 x Mod m[i] = b[i]
	LL B=b[1],M=m[1];
	for(int i=2;i<=n;++i){
		LL C=b[i]-B;
		LL x,y;
		LL g=ex_gcd(M,m[i],x,y);
		m[i]/=g;
		x=((x*C/g)%m[i]+m[i])%m[i];
		LL nM=M*m[i];
		B=((x*M+B)%nM+nM)%nM;
		M*=m[i];
	}
	Pt(B);
}
void MTT(int n,int *a,int *b){
	for(int i=0;i<n;++i){
		R[i]=(R[i>>1]| ((i&1)? n:0))>>1;
		A[0][i]=A[1][i]=A[2][i]=a[i];
		B[0][i]=B[1][i]=B[2][i]=b[i];
	}
	NTT(A[0],1,n,M0),NTT(A[1],1,n,M1),NTT(A[2],1,n,M2);
	NTT(B[0],1,n,M0),NTT(B[1],1,n,M1),NTT(B[2],1,n,M2);
	for(int i=0;i<n;++i){
		A[0][i]=1ll*A[0][i]*B[0][i]%M0;
		A[1][i]=1ll*A[1][i]*B[1][i]%M1;
		A[2][i]=1ll*A[2][i]*B[2][i]%M2;
	}
	NTT(A[0],0,n,M0),NTT(A[1],0,n,M1),NTT(A[2],0,n,M2);
	for(int i=0;i<=::n+::m;++i){
		mm[3]=M0,bb[3]=A[0][i];
		mm[1]=M1,bb[1]=A[1][i];
		mm[2]=M2,bb[2]=A[2][i];
		ex_CRT(3,bb,mm);
	}
}
int main(){
	scanf("%d %d %d",&n,&m,&p);
	for(int i=0;i<=n;++i)scanf("%d",&a[i]);
	for(int i=0;i<=m;++i)scanf("%d",&b[i]);
	MTT(262144,a,b);
	return 0;
}
```