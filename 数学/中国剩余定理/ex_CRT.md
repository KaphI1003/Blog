~~不会证明，只会套版~~

```cpp
#include<bits/stdc++.h>
typedef long long LL;
typedef __int128 LLL;
using namespace std;
const int N=1e5+5;
int n;
LL a[N],b[N];
LL ex_gcd(LL a,LL b,LLL &x,LLL &y){// 要配 exgcd 一起用
	if(!b){
		x=1,y=0;
		return a;
	}
	LL g=ex_gcd(b,a%b,x,y);
	LLL t=x;
	x=y,y=t-(LLL)(a/b)*y;
	return g;
}
void Pt(LLL x){
	int a[50]={},l=0;
	while(x){
		a[++l]=x%10;
		x/=10;
	}
	for(int i=l;i;i--)printf("%d",a[i]);
	printf(" ");
}
LL ex_CRT(int n,LL *b,LL *m){//n个 x Mod m[i] = b[i]
	LL B=b[1],M=m[1];
	for(int i=2;i<=n;++i){
		LL C=b[i]-B;
		LLL x,y;
		LL g=ex_gcd(M,m[i],x,y);
		if(C%g)return -1;
		m[i]/=g;
		x=((x*C/g)%m[i]+m[i])%m[i];
		LL nM=M*m[i];
		B=((x*M+B)%nM+nM)%nM;
		M*=m[i];
	}
	return B;
}
int main(){
	scanf("%d",&n);
	for(int i=1;i<=n;++i){
		scanf("%lld %lld",&a[i],&b[i]);
	}
	printf("%lld\n",ex_CRT(n,b,a));
	return 0;
}
```