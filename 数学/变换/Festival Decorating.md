# [Festival Decorating](https://codeforces.com/gym/104976/problem/B)

### 简要题面
在一个数轴上有 $n$ 个路灯，第 $i$ 个路灯的坐标是 $x_i$，颜色为 $c_i$，一共有 $q$ 个询问，第 $i$ 个询问给一个参数 $d_i$ ，要求找到最小的路灯编号 $u$，满足 $x_u+d$ 处有一个路灯而且两盏灯的颜色不同。不过你给出的答案 $v$ 满足 $\dfrac{|u-v|}{u} \le 0.5$即可，也就是你给出的答案的绝对误差不大于$0.5$ 即可。

保证路灯坐标两两不同，所有参数的范围都是 $[1,250000]$，且 $c_i \le n$。

### 问题分析

#### 方法一：FFT

题目给了我们误差的空间，让我们不需要找出正确的答案而是一个近似解即可。容易发现，我们只需要 $O(\log n)$ 个答案就可以回答所有的询问。第 $i$ 个答案 $v_i$ 对应一段区间$[l_i,r_i]$，对于距离 $d$ ，如果满足条件的最小编号在$[l_i,r_i]$内，回答 $v_i$绝对没有问题。那么问题就转化成确定一个区间内的路灯中存不存在解，就不需要精确求解了。

离线询问，设 $A_i$表示坐标为 $i$ 且编号在 $[l,r]$中的灯的颜色，若不存在则为 $0$，设 $B_i$ 表示坐标为 $i$ 的灯的颜色，若不存在则为 $0$。

对于距离 $d$ 有解，当且仅当 $\sum_i[A_i>0][B_{i+d}>0](A_i-B_{i+d})^2$ 大于 $0$ 。将 $A_i$ 下标反转，平方拆开分三项分别FFT，即可在 $O(n\log n)$ 的时间复杂度内求解出对于区间$[l,r]$，所有的 $d$在其中有没有解。

一共$O(\log n)$个区间需要判断，故最终复杂度为 $O(n\log^2 n)$。
##### code
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=1e6+5,M=1004535809,up=524288,NN=250000;
int R[N];//偷懒使用 NTT ，正解请使用FFT，998244353模数会被卡
void ck(int &x){(x>=M)&&(x-=M);}
void kc(int &x){(x<0)&&(x+=M);}
int pw(int x,int t){
    int rs=1;
    for(;t;t>>=1,x=1ll*x*x%M)if(t&1)rs=1ll*rs*x%M;
    return rs;
}
void NTT(int *a,int tp,int up){
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
int n,q,x[N],c[N],b0[N],b1[N],b2[N],a0[N],a1[N],a2[N],rs[N];
struct Qry{
    int id,d;
}Q[N];
int ql,ans[N];
void slv(int r){
    if(!r)return;
    int mid=(r+1)>>1;
    int l=(mid*2-1)/3+1;
    for(int i=0;i<up;++i){
        a0[i]=a1[i]=a2[i]=0;
    }
    for(int i=1;i<l;++i){
        a0[NN-x[i]]=1;
        a1[NN-x[i]]=c[i];
        a2[NN-x[i]]=1ll*c[i]*c[i]%M;
    }
    NTT(a0,1,up),NTT(a1,1,up),NTT(a2,1,up);
    for(int i=0;i<up;++i)rs[i]=(1ll*a0[i]*b2[i]+1ll*a1[i]*b1[i]+1ll*a2[i]*b0[i])%M;
    NTT(rs,0,up);
    int nql=0;
    for(int i=1;i<=ql;++i){
        if(!rs[NN+Q[i].d]){
            ans[Q[i].id]=mid;
        }else Q[++nql]=Q[i];
    }
    ql=nql;
    slv(l-1);
}
int main(){
    scanf("%d %d",&n,&q);
    for(int i=1;i<=n;++i){
        scanf("%d %d",&x[i],&c[i]);
        b0[x[i]]=1;
        b1[x[i]]=M-2*c[i];
        b2[x[i]]=1ll*c[i]*c[i]%M;
    }
    for(int i=0;i<up;++i)R[i]=(R[i>>1]| ((i&1)? up:0))>>1;
    NTT(b0,1,up),NTT(b1,1,up),NTT(b2,1,up);
    for(int i=1;i<=q;++i){
        int d;
        scanf("%d",&d);
        Q[++ql]=(Qry){i,d};
    }
    for(int i=1;i<=n;++i){
        a0[NN-x[i]]=1;
        a1[NN-x[i]]=c[i];
        a2[NN-x[i]]=1ll*c[i]*c[i]%M;
    }
    NTT(a0,1,up),NTT(a1,1,up),NTT(a2,1,up);
    for(int i=0;i<up;++i)rs[i]=(1ll*a0[i]*b2[i]+1ll*a1[i]*b1[i]+1ll*a2[i]*b0[i])%M;
    NTT(rs,0,up);
    int nql=0;
    for(int i=1;i<=ql;++i){
        if(!rs[NN+Q[i].d]){
            ans[Q[i].id]=0;
        }else Q[++nql]=Q[i];
    }
    ql=nql;
    slv(n);
    for(int i=1;i<=q;++i)printf("%d\n",ans[i]);
    return 0;
}
```

#### 方法二：bitset

方法二虽然在复杂度上略逊一筹，但是可以准确求解出每个询问的答案 $u$。

对于路灯$i$，如果我们要找出所有的$d$满足$x_i+d$ 处有一个路灯而且两盏灯的颜色不同，直接做复杂度就是 $O(n)$。但是这等价于开一个 bitset $B$，然后对于所有颜色不同于路灯$i$的路灯 $j$，使$B[x_j-x_i] = 1$。若$d$满足上述条件，当且仅当$B[d]=1$。

我们先开一个 bitset $all$ 存所有路灯，让$all[x_i]=1，1\le i \le n$。但是我们在处理路灯$i$时，需要所有颜色为$c_i$的路灯从$all$中去除，如果我们对所有颜色分别开一个bitset $B_i$，就可以直接用$all$来异或$B_i$。但是空间不允许我们开$n$个bitset，所以我们使用根号分治。对于颜色$c$，如果有大于$\sqrt{n}$个路灯，那么开bitset处理；否则存进vector里，用的时候直接遍历整个vector，时间复杂度$O(\sqrt{n})$。两者取其大时间复杂度为$O(\dfrac{n}{w})$

我们把询问的 $d$ 都存入bitset $qry$ 中。然后从小到大枚举$i$，设$all$去除掉与$i$颜色相同的路灯后的 bitset 为$tmp$(在上面已证明可以在$O(\dfrac{n}{w})$时间内求得)，那么所有满足条件的$d$就是 ($qry$ AND $(tmp>>x[i])$ $)。我们遍历($$qry$ AND $(tmp>>x[i])$)，将答案记录下来，然后将$d$从 $qry$ 中去除，就可以做到均摊$O(\dfrac{n^2}{w})$的时间复杂度了。

遍历bitset 中的 $1$ 有专门的函数可用，详见代码。

##### 时间复杂度 $O(\dfrac{n^2}{w})$
##### code
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=250005,K=500;
bitset<N>all,ext[501],qry;
vector<int>V[N];
int n,q,x[N],c[N],Q[N],idx[N],cnt,mp[N],ans[N];
int main(){
    scanf("%d %d",&n,&q);
    for(int i=1;i<=n;++i){
        scanf("%d %d",&x[i],&c[i]);
        all[x[i]]=1;
        V[c[i]].push_back(x[i]);
    }
    for(int i=1;i<=n;++i){
        if(V[i].size()>K){
            idx[++cnt]=i;
            mp[i]=cnt;
            for(auto x:V[i])ext[cnt][x]=1;
        }
    }
    for(int i=1;i<=q;++i){
        scanf("%d",&Q[i]);
        qry[Q[i]]=i;
    }
    for(int i=1;i<=n;++i){
        bitset<N>tmp=all;
        if(V[c[i]].size()>K){
            tmp^=ext[mp[c[i]]];
        }else {
            for(auto x:V[c[i]]){
                tmp[x]=0;
            }
        }
        tmp=qry&(tmp>>x[i]);
        for(int x=tmp._Find_first();x<tmp.size();x=tmp._Find_next(x)){//_Find_next 函数没有找到时返回size，_Find_first同理。
            ans[x]=i;
            qry[x]=0;
        }
    }
    for(int i=1;i<=q;++i)printf("%d\n",ans[Q[i]]);
    return 0;
}
```

