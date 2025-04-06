
[一篇他人博客](https://www.cnblogs.com/cjyyb/p/9065615.html)

# 多项式的位运算卷积




```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=131100,M=998244353,p2=499122177;
int up,a[N],b[N],c[N];
void ck(int &x){x>=M&&(x-=M);}
void FWT_or(int *a,int tp){
	for(int i=1;i<up;i<<=1){
		for(int o=0,I=i<<1;o<up;o+=I){
			for(int u=o,U=o+i;u<U;++u){
				ck(a[u+i]+= (tp)? a[u]:M-a[u]);
			}
		}
	}
}
void FWT_and(int *a,int tp){
	for(int i=1;i<up;i<<=1){
		for(int o=0,I=i<<1;o<up;o+=I){
			for(int u=o,U=o+i;u<U;++u){
				ck(a[u]+= (tp)? a[u+i]:M-a[u+i]);
			}
		}
	}
}
void FWT_xor(int *a,int tp){
	for(int i=1;i<up;i<<=1){
		for(int o=0,I=i<<1;o<up;o+=I){
			for(int u=o,U=o+i;u<U;++u){
				int x=a[u],y=a[u+i];
				ck(a[u]=x+y);ck(a[u+i]=x+M-y);
				if(!tp)a[u]=1ll*a[u]*p2%M,a[u+i]=1ll*a[u+i]*p2%M;
			}
		}
	}
}
int main(){
	scanf("%d",&up);
	up=1<<up;
	for(int i=0;i<up;++i)scanf("%d",&a[i]);
	for(int i=0;i<up;++i)scanf("%d",&b[i]);
	FWT_or(a,1);FWT_or(b,1);
	for(int i=0;i<up;++i)c[i]=1ll*a[i]*b[i]%M;
	FWT_or(c,0);FWT_or(a,0);FWT_or(b,0);
	for(int i=0;i<up;++i)printf("%d ",c[i]);puts("");
	FWT_and(a,1);FWT_and(b,1);
	for(int i=0;i<up;++i)c[i]=1ll*a[i]*b[i]%M;
	FWT_and(c,0);FWT_and(a,0);FWT_and(b,0);
	for(int i=0;i<up;++i)printf("%d ",c[i]);puts("");
	FWT_xor(a,1);FWT_xor(b,1);
	for(int i=0;i<up;++i)c[i]=1ll*a[i]*b[i]%M;
	FWT_xor(c,0);FWT_xor(a,0);FWT_xor(b,0);
	for(int i=0;i<up;++i)printf("%d ",c[i]);puts("");
	return 0;
}
```
