# [图 (graph)](http://10.220.121.203/judge/problem.php?id=3962)
### 简要题面

一个$n$个点，$m$条边的无向简单图，从中选出一些点并只保留这些点之间的边，问度数最小的节点的度数最大是多少？
$n,m \le 10^5$

### 1.二分+拓扑：
我们可以发现，只要二分答案，一个一个删点，直到无边可删，这时判一下还有没有点在图上就可以了。但是用前向星就会常数极大T成铁掏，不过可以通过在二分成功时保存新图的做法过掉此题。
##### 时间复杂度：$O(m \log n)$
##### code：
```
#include<bits/stdc++.h>
using namespace std;
const int N=1e5+5;
int n,m,h[N],q[N],a[N],s[N],p[N],pl;
bool v[N];
struct E{
	int l,to;
}e[N*20];
bool ck(const int W){
	int ql=0;
	for(int i=1;i<=pl;++i){
		v[p[i]]=0;
		if(s[p[i]]<W)q[++ql]=p[i];
		else a[p[i]]=s[p[i]];
	}
	for(int i=1;i<=ql;++i){
		int nw=q[i];v[nw]=1;
		for(int i=h[nw];i;i=e[i].l){
			int to=e[i].to;
			if(!v[to]){
				if(a[to]==W){q[++ql]=to;v[to]=0;}
				a[to]--;
			}
		}
		if(ql==pl)return 0;
	}
	int nl=0;
	for(int i=1;i<=pl;++i){
		if(!v[p[i]]){p[++nl]=p[i];s[p[nl]]=a[p[nl]];}
	}
	pl=nl;
	return 1;
}
void rd(int &x){
	x=0;char C=getchar();
	while(!isdigit(C))C=getchar();
	while(isdigit(C)){x=(x<<3)+(x<<1)+(C^48);C=getchar();}
}
int main(){
	int x,y;
	rd(n),rd(m);
	if(n==1){puts("0");return 0;}
	for(int i=1;i<=m;++i){
		rd(x),rd(y);
		e[i<<1].to=x;e[i<<1].l=h[y];h[y]=i<<1;
		e[i<<1|1].to=y;e[i<<1|1].l=h[x];h[x]=i<<1|1;
		s[x]++;s[y]++;
	}
	pl=n;
	for(int i=1;i<=n;++i)p[i]=i;
	int l=1,r=0,mid,ans;
	for(int i=1;i<=n;++i)r=max(r,s[i]);
	while(l<=r){
		if(ck(mid=l+r>>1)){
			ans=mid;
			l=mid+1;
		}else r=mid-1;
	}
	printf("%d\n",ans);
	return 0;
}
```
### 堆或线段树
再仔细想一想，发现我们每一次只要删掉点数最小的点并更新答案及可，用堆或者线段树就可以了。
#####　code：
```
#include<bits/stdc++.h>
using namespace std;
const int N=1e5+5;
int n,m,ans,h[N],s[N];
struct E{
    int l,to;
}e[N*20];
struct P{
    int id,w;
    bool operator < (const P&t)const{
        return w>t.w;
    }
}k;
priority_queue<P>Q;
void rd(int &x){
    x=0;char C=getchar();
    while(!isdigit(C))C=getchar();
    while(isdigit(C)){x=(x<<3)+(x<<1)+(C^48);C=getchar();}
}
int main(){
    int x,y;
    rd(n),rd(m);
    for(int i=1;i<=m;++i){
        rd(x),rd(y);
        e[i<<1].to=y;e[i<<1].l=h[x];h[x]=i<<1;
        e[i<<1|1].to=x;e[i<<1|1].l=h[y];h[y]=i<<1|1;
        s[x]++;s[y]++;
    }
    for(int i=1;i<=n;++i){
        Q.push((P){i,s[i]});
    }
    while(!Q.empty()){
        k=Q.top();Q.pop();
        if(s[k.id]==-1)continue;
        s[k.id]=-1;ans=max(ans,k.w);
        for(int i=h[k.id];i;i=e[i].l){
            int to=e[i].to;
            if(s[to]>0){
                s[to]--;
                Q.push((P){to,s[to]});
            }
        }
    }
    printf("%d\n",ans);
}
```
### 链表
由于每一次操作只会掉1，所以直接链表操作代替即可。
```
#include<bits/stdc++.h>
using namespace std;
const int N=1e5+5,inf=2e9;
int n,m,ans,h[N],s[N],up,L[N*3],R[N*3],l[N],r[N];
bool v[N];
struct E{
    int l,to;
}e[N*20];
void rd(int &x){
    x=0;char C=getchar();
    while(!isdigit(C))C=getchar();
    while(isdigit(C)){x=(x<<3)+(x<<1)+(C^48);C=getchar();}
}
int main(){
    int x,y;
    rd(n),rd(m);
    for(int i=1;i<=m;++i){
        rd(x),rd(y);
        e[i<<1].to=y;e[i<<1].l=h[x];h[x]=i<<1;
        e[i<<1|1].to=x;e[i<<1|1].l=h[y];h[y]=i<<1|1;
        s[x]++;s[y]++;
    }
    for(int i=0;i<n;++i){
        const int z=N+i,y=z+N;
        R[z]=y;L[y]=z;
    }
    r[n]=n+1;l[n+1]=n;
    for(int i=1;i<=n;++i){
        const int y=s[i]+N+N,z=L[y];
        R[z]=L[y]=i;
        L[i]=z;R[i]=y;
    }
    for(int i=0;i<n;++i){
        if(R[N+i]!=N+N+i){
            const int y=n+1,z=l[y];
            r[z]=l[y]=i;
            l[i]=z;r[i]=y;
        }
    }
    for(int I=n;I;I--){
        const int w=r[n],k=R[w+N];
        v[k]=1;
        ans=max(ans,w);
        R[L[k]]=R[k];
        L[R[k]]=L[k];
        if(R[N+w]==N+N+w){
            r[l[w]]=r[w];
            l[r[w]]=l[w];
        }
        for(int i=h[k];i;i=e[i].l){
            int to=e[i].to,y=s[to]+N+N-1,z=L[y];
            if(v[to])continue;
            R[L[to]]=R[to];
            L[R[to]]=L[to];
            R[z]=L[y]=to;
            L[to]=z;R[to]=y;
            if(y-z==N){
                y=s[to];z=l[y];
                r[z]=l[y]=s[to]-1;
                l[s[to]-1]=z;
                r[s[to]-1]=y;
            }
            if(R[N+s[to]]==N+N+s[to]){
                r[l[s[to]]]=r[s[to]];
                l[r[s[to]]]=l[s[to]];
            }
            s[to]--;
        }
    }
    printf("%d\n",ans);
}
```