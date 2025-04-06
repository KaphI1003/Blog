# [Guess the number](https://ac.nowcoder.com/acm/contest/44007/E)

### 简要题意
有一个未知数$x,x \in [0,5*10^{15}]$。把它转换成$2,3,5,7,11$进制后得到A，B，C，D，E。从第到高第 i 位分别为 $A_i,B_i,C_i,D_i,E_i$

设$F_i=A_i+B_i+C_i+D_i+E_i$,现在告诉你$F_0,F_1,F_2...F_{36}$的值，求最小的符合条件的$x$。

多组数据,$1\leq T\leq 100$。

### 分析
DFS 剪枝
按二进制从高到低枚举，先0后1。dfs时我们可以知道可能答案x的下限和上限。F的值也是随x增大而增大。通过计算我们也可以得到对应的$A_i,B_i,C_i,D_i,E_i$的取值范围，进而得到$F_i$的范围。只要$F_i$不在取值范围内就说明当前范围内无答案。就可以剪了，效率极高。

```
#include<bits/stdc++.h>
using namespace std;
const int N=105,D[]={2,3,5,7,11};
const int L=15,R=15;
int n,f[N],Up[N],Dw[N];
long long rs;
bool ck(long long l,long long r){//剪枝 
	for(int i=0;i<37;++i){
		Up[i]=(r&(1ll<<i))? 1:0;
		Dw[i]=(l&(1ll<<i))? 1:0;
	}//Up[i]是第 i 位的上限，Dw[i]是第 i 位的下限。 
	for(int i=1;i<5;++i){/ 
		long long x=l,y=r;
		for(int o=0,fl=0;o<37&&y;++o){//范围差值增量从低位到高位是 1,1,....,1,x,D[i]-1,D[i-1]...D[i]-1。 
			if(fl){
				long long k=x%D[i];
				if(fl==1&&k==D[i]-1){//dw为D[i]-1,up为0的情况，虽然只有两个数但是占了整个区间，也不好单独拿出来特判 
					Up[o]+=D[i]-2;
					Up[o+1]++;
				}else {
					Up[o]+=k,Dw[o]+=k;
					fl=2;
				}
			}else if(y-x+1<D[i]){
				long long a=x%D[i],b=y%D[i];
				if(a<=b){
					Up[o]+=b;
					Dw[o]+=a;
					fl=2;
				}else Up[o]+=D[i]-1,Up[o+1]++,fl=1;//有进位的情况 
			}else Up[o]+=D[i]-1;
			x/=D[i],y/=D[i];
		}
	}
	for(int i=0;i<37;++i)if(!(Dw[i]<=f[i]&&f[i]<=Up[i]))return 0;
	return 1;
}
long long dfs(int x){
	if(x==-1)return ck(rs,rs)? rs:-1;
	if(!ck(rs,rs-1+(2ll<<x)))return -1;//不知道为什么不判x范围直接剪效率特高 
	long long k;
	if((k=dfs(x-1))!=-1)return k;
	rs^=1ll<<x;
	if((k=dfs(x-1))!=-1)return k;
	rs^=1ll<<x;
	return -1;
}
void slv(){
	rs=0;
	for(int i=0;i<37;++i)scanf("%d",&f[i]);
	printf("%lld\n",dfs(52));
}
int main(){
	int T;
	scanf("%d",&T);
	while(T--)slv();
	return 0;
}
```