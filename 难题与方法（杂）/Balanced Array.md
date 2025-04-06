# [Balanced Array](https://codeforces.com/gym/104857/problem/D)
### 简要题意
给你一个长为 $n$ 的序列 $a$ ，称一个长为 $l$ 序列是好的，当且仅当存在一个 $k$ 满足 $3 \le 2k+1 \le l$ ，且 $a_i + a_{i+2k} = 2a_{i+k}$ 对每一个 $i \in [1,l-2k]$ 成立。问 $a$ 的每个前缀是不是好序列。
$1 \le n \le 2*10^6$，$1 \le a_i \le 2*10^8$ 。
### 问题分析
模拟就是 $O(n^2)$ 。

考虑如何对于一个确定的 $k$ ，快速判断长为 $l$ 的序列是不是一个好序列。我们要快速判断 $l-2k$ 个等式是不是都成立。观察式子可以发现，式子相同的位置的变量的下标是连续变化的，于是我们可以考虑哈希 $O(1)$ 判断。

对于每个 $k$ ，显然它对应的合法前缀的下标也是一段连续的区间，通过二分或者双指针即可通过此题。

##### code:
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=2e6+5;
int n,a[N];
int clc(char c){
    if('0'<=c&&c<='9')return c-'0';
    else if('a'<=c&&c<='z')return c-'a'+10;
    else if('A'<=c&&c<='Z')return c-'A'+36;
    return -1;
}
void Rd(int &x){
    x=0;
    char c=getchar();
    while(clc(c)==-1)c=getchar();
    do {x=x*62+clc(c);} while(clc(c=getchar())!=-1);
}
void Rd_int(int &x){
    x=0;
    char c=getchar();
    while(clc(c)==-1)c=getchar();
    do {x=x*10+clc(c);} while(clc(c=getchar())!=-1);
}
const int M1=998244353,M2=1e9+7,W=19260817;
struct P{
    int w1,w2;
    bool operator == (const P&t)const{
        return w1==t.w1&&w2==t.w2;
    }
}h[N],inv[N];
P times(P x,int y){
    return (P){1ll*x.w1*y%M1,1ll*x.w2*y%M2};
}
P times(P x,P y){
    return (P){1ll*x.w1*y.w1%M1,1ll*x.w2*y.w2%M2};
}
P add(P x,P y){
    return (P){(1ll*x.w1+y.w1)%M1,(1ll*x.w2+y.w2)%M2};
}
P _minus(P x,P y){
    return (P){(1ll*x.w1+M1-y.w1)%M1,(1ll*x.w2+M2-y.w2)%M2};
}
int pw(int x,int t,int M){
    int rs=1;
    for(;t;t>>=1,x=1ll*x*x%M)if(t&1)rs=1ll*rs*x%M;
    return rs;
}
void Hash(){
    inv[0]=(P){1,1};
    inv[1]=(P){pw(W,M1-2,M1),pw(W,M2-2,M2)};
    P t = (P){1,1};
    for(int i=1;i<=n;++i){
        h[i]= add( times((P){a[i],a[i]},t) , h[i-1]);
        inv[i+1] = times(inv[i],inv[1]);
        t=times(t,W);
    }
}
P slv(int l,int r){
    return times(_minus(h[r],h[l-1]),inv[l-1]);
}
char ans[N];
int main(){
    Rd_int(n);
    for(int i=1;i<=n;++i){
        Rd(a[i]);
        ans[i]='0';
    }
    Hash();
    int p=0;
    for(int k=1;2*k+1<=n;++k){
        p=max(p,2*k+1);
        while(add(slv(1,p-2*k),slv(2*k+1,p)) == times(slv(k+1,p-k),2)){
            ans[p++]='1';
        }
    }
    puts(ans+1);
}
```