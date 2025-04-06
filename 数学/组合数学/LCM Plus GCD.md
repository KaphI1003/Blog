# [LCM Plus GCD](https://codeforces.com/gym/104396/problem/E)

### 简要题意

给你两个整数$x,k$，$1\leq x,k \leq 10^9$。问有多少组不同的集合$\{a_1,a_2,...,a_k\}$，满足元素两两不同，且$gcd\{a_1,a_2,...,a_k\}+lcm\{a_1,a_2,...,a_k\}=x$。答案对$1e9+7$取模。

### 问题分析

设满足$gcd=a,lcm=b$的集合的个数是$cnt(a,b)$，那么显然有$cnt(a,b)=cnt(1,\frac{b}{a})$。设$x$的因数集合是$S_x$，我们所要求的答案就是$\sum_{i}^{S_x-\{x\}}cnt(i,x-i)$，即 $\sum_{i}^{S_x-\{x\}}cnt(1,\frac{x}{i}-1)$。这个我们可以通过枚举$i$计算，那么问题就在如何快速计算$cnt(1,a),1\leq a \leq x$。

设$a$的因数集合是$S_a$，大小为$n$，所有$lcm=a$的集合的个数为$cnt2 (a)$ ，我们通过容斥可以得到：

**$cnt(1,a)=$ 所有集合的个数 - $(lcm \ne a)$的集合个数 -  $(lcm = a)$但$(gcd \ne 1 )$的集合个数。**

**所有集合的个数**显然就是从$n$的数中挑$k$的方案，即$C_n^k$。

**$(lcm \ne a)$的集合个数**等于$\sum_i^{S_a-\{x\}}cnt2(i)$。

**$(lcm = a)$但$(gcd \ne 1 )$的集合个数**等于$\sum_i^{S_a-\{1\}}cnt(i,a)$=$\sum_i^{S_a-\{1\}}cnt(1,\frac{a}{i})$=$\sum_i^{S_a-\{x\}}cnt(1,i)$。

所以

$$
cnt(1,a)=C_n^k-\sum_i^{S_a-\{x\}}cnt2(i)-\sum_i^{S_a-\{x\}}cnt(1,i)
$$

而显然我们有

$$
cnt2(a)=C_n^k-\sum_i^{S_a-\{x\}}cnt2(i)
$$

数学分析完我们考虑如何用代码实现，直接对$10^9$的数直接计算是复杂度无法承受的，我们再对$cnt(1,a),cnt2(a)$进一步观察，可以得到$cnt(1,a),cnt2(a)$只和$a$的质因数种类数和每种的个数有关，所以我们可以构造一个$Trans(a)$函数，将其转换成质因数种类数和每种的个数相同的最小整数，这可以极大的优化运行时间。

计算$cnt(1,a),cnt2(a)$可以直接使用记忆化搜索。

时间复杂度不会算，但是跑很快就是了。

##### code

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=1005,M=1e9+7;
int x,k,S[N],F[N],p[N];
map<int,int>trans,cnt,cnt2;
vector<int>V;
void calc(int x);
int Trans(int x){
    V.clear();
    for(int i=2;i*i<=x;++i){
        if(x%i==0){
            int s=1;
            x/=i;
            while(x%i==0)x/=i,s++;
            V.push_back(s);
        }
    } 
    if(x>1)V.push_back(1);
    sort(V.begin(),V.end());
    int rs=1;
    for(int i=V.size()-1;i>=0;--i){
        while(V[i]--)rs*=p[V.size()-i];
    }
    return rs;
}
int pw(int x,int t){
    int rs=1;
    for(;t;t>>=1,x=1ll*x*x%M)if(t&1)rs=1ll*rs*x%M;
    return rs;
}
int C(int x,int k){
    if(x<k)return 0;
    return 1ll*S[x]*F[k]%M*F[x-k]%M;
}
void ck(int &x){(x>=M)&&(x-=M);}
pair<int,int> dfs(int i,int w,int x){
    pair<int,int> rs={};
    if(x%p[i]!=0){
        if(x==w)return rs;
        w=Trans(w);
        if(cnt.find(w)==cnt.end())calc(w);
        rs.first=cnt[w],rs.second=cnt2[w];
        return rs;
    }
    while(x%w==0){
        pair<int,int> t=dfs(i+1,w,x);
        ck(rs.first+=t.first);
        ck(rs.second+=t.second);
        w*=p[i];
    }
    return rs;
}

void calc(int x){
    if(x==1)return;
    if(cnt.find(x)!=cnt.end())return;
    int s=1;
    for(int i=1,t=x;p[i]<=x&&t>1;++i)if(t%p[i]==0){
        int w=2;
        t/=p[i];
        while(t%p[i]==0)t/=p[i],w++;
        s*=w;
    }
    int rs=C(s,k);
    pair<int,int>v=dfs(1,1,x);
    ck(rs+=M-v.second);
    cnt2[x]=rs;
    ck(rs+=M-v.first);
    cnt[x]=rs;
}
vector<int>vec;
int DFS(int i,int w,int x){
    int rs=0;
    if(i==(int)vec.size()){
        if(x==w)return 0;
        int y=Trans((x-w)/w);
        if(cnt.find(y)==cnt.end())calc(y);
        return cnt[y];
    }
    while(x%w==0){
        ck(rs+=DFS(i+1,w,x));
        w*=vec[i];
    }
    return rs;
}
int main(){
    scanf("%d %d",&x,&k);
    p[1]=2,p[2]=3,p[3]=5,p[4]=7,p[5]=11,p[6]=13,p[7]=17,p[8]=19,p[9]=23,p[10]=29;
    S[0]=1;
    for(int i=1;i<=1000;++i)S[i]=1ll*S[i-1]*i%M;
    F[1000]=pw(S[1000],M-2);
    for(int i=1000;i;i--)F[i-1]=1ll*F[i]*i%M;

    cnt[1]=cnt2[1]=(k==1);
    cnt[2]=(k==2),cnt2[2]=(k<=2);
    
    int t=x;
    for(int i=2;i*i<=t;++i){
        if(t%i==0){
            int s=1;
            t/=i;
            while(t%i==0)t/=i,s++;
            vec.push_back(i);
        }
    } 
    if(t>1)vec.push_back(t);
    printf("%d\n",DFS(0,1,x));
}
```

