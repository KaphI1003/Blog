# [Triangle Platinum](https://codeforces.com/contest/1847/problem/E)

### 简要题意
交互题，让你去猜一个长度为$n$的序列$a$，保证$1 \le  a_i \le 4$，每次查询你可以查询 $3$ 个互不相同的数 $i,j,k$，如果$a_i,a_j,a_k$可以构成三角形，返回这个三角形的面积，否则返回 $0$，请你在 $5500$ 次询问中得到答案。
 $n \le 5000$
### 问题分析
除了 (1,4,4) 和 (2,3,3) 相同外，其它均不同，当$n \le 8 $时，直接暴力枚举即可，当$n > 8$ 时，我们在前 $9$ 个中总能找到 $3$ 个相同的元素。我们把它暴力找出来，如果这 3 个不等于 1 ，那么我们就可以选其中两个跟剩下来的全部做查询得到唯一的答案。如果找到的是 3 个 1 ，那么我们跟剩下的也做查询，如果查出来是 $3$，那说明查询的位置也是 $1$，否则它大于 1 ，我们拿它和 前面的一个大于 1 的位置 和一个 1 做查询，如果查出来有值，就说明我们找到了两个相同且大于 $1$ 的元素，剩下的未确定的数跟它俩做查询即可。在大于 $1$ 的数超过 3 个的时候，我们一定可以找到一对符合条件的数，所以次数都不会超过 5500。

##### code 
```
#include<bits/stdc++.h>
using namespace std;
int n,s[10][10][10],a[5005],rs[5005];
namespace slv1{
	int w[10][10][10],fl;
	void dfs(int id){
		if(id>n){
			for(int i=1;i<n-1;++i){
				for(int j=i+1;j<n;++j){
					for(int k=j+1;k<=n;++k){
						if(s[a[i]][a[j]][a[k]]!=w[i][j][k])return;
					}
				}
			}
			if(fl){
				puts("! -1");
				exit(0);
			}
			fl=1;
			for(int i=1;i<=n;++i)rs[i]=a[i];
			return;
		}
		for(int i=1;i<=4;++i){
			a[id]=i;
			dfs(id+1);
		}
	}
	void go(){
		fl=0;
		for(int i=1;i<n-1;++i){
			for(int j=i+1;j<n;++j){
				for(int k=j+1;k<=n;++k){
					printf("? %d %d %d\n",i,j,k);
					fflush(stdout);
					scanf("%d",&w[i][j][k]);
				}
			}
		}
		dfs(1);
	}
}
namespace slv2{
	int q[10],ql;
	void go(){
		int I=0,J=0,K=0,p=0,w;
		for(int i=1;i<8&&!p;++i){
			for(int j=i+1;j<9&&!p;++j){
				for(int k=j+1;k<=9&&!p;++k){
					printf("? %d %d %d\n",i,j,k);
					fflush(stdout);
					scanf("%d",&w);
					if(w==3){I=i,J=j,K=k,p=1;break;}
					else if(w==48){I=i,J=j,K=k,p=2;break;}
					else if(w==243){I=i,J=j,K=k,p=3;break;}
					else if(w==768){I=i,J=j,K=k,p=4;break;}
				}
			}
		}
		if(!I){
			puts("! -1");
			exit(0);
		}
		rs[I]=rs[J]=rs[K]=p;
		if(p==1){
			for(int i=1;i<=n;++i){
				if(i==I||i==J||i==K)continue;
				printf("? %d %d %d\n",i,J,K);
				fflush(stdout);
				scanf("%d",&w);
				if(w==3)rs[i]=1;
				else {
					q[++ql]=i;
					for(int j=1;j<ql;++j){
						printf("? %d %d %d\n",I,q[j],i);
						fflush(stdout);
						scanf("%d",&w);
						if(w==15){
							J=q[j],K=i,p=2;
							rs[J]=rs[K]=2;
							break;
						}else if(w==35){
							J=q[j],K=i,p=3;
							rs[J]=rs[K]=3;
							break;
						}else if(w==63){
							J=q[j],K=i,p=4;
							rs[J]=rs[K]=4;
							break;
						}
					}
					if(p!=1)break;
				}
			}
		}
		if(p>1){
			for(int i=1;i<=n;++i)if(!rs[i]){
				printf("? %d %d %d\n",i,J,K);
				fflush(stdout);
				scanf("%d",&w);
				if(w==s[1][p][p])rs[i]=1;
				else if(w==s[2][p][p])rs[i]=2;
				else if(w==s[3][p][p])rs[i]=3;
				else rs[i]=4;
			}
		}
		for(int i=1;i<=n;++i)if(!rs[i]){
			puts("! -1");
			exit(0);
		}
	}
}
int main(){
	for(int i=1;i<=4;++i){
		for(int j=1;j<=4;++j){
			for(int k=1;k<=4;++k){
				int x=max(max(i,j),k);
				if(i+j+k-x>x)
				s[i][j][k]=(i+j+k)*(i+j-k)*(i+k-j)*(j+k-i);
			}
		}
	}
	scanf("%d",&n);
	if(n<9)slv1::go();
	else slv2::go();
	printf("!");
	for(int i=1;i<=n;++i)printf(" %d",rs[i]);
	return 0;
}
```