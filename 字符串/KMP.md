
大体就是通过减少无用匹配来达到O(n)。
具体以后回来补

```
#include<bits/stdc++.h>
using namespace std;
const int N=1000005;
int n,m,nt[N];
char a[N],b[N];
void kmp(char *a,int n){
	int z=0;
	for(int i=2;i<=n;++i){
		while(z&&a[i]!=a[z+1])z=nt[z];
		if(a[i]==a[z+1])nt[i]=++z;
	}
}
int main(){
	scanf("%s %s",a+1,b+1);
	n=strlen(a+1);m=strlen(b+1);
	kmp(b,m);
	for(int i=1,z=0;i<=n;++i){
		while(z&&a[i]!=b[z+1])z=nt[z];
		if(a[i]==b[z+1])z++;
		if(z==m){
			printf("%d\n",i-m+1);
			z=nt[z];
		}
	}
	for(int i=1;i<=m;++i)printf("%d ",nt[i]);
	return 0;
}
```
