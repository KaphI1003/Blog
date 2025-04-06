# [Fancy Stack](https://qoj.ac/contest/988/problem/4572)

### 简要题意
给你 $n$ 个数 $a_i$ ，$n$ 是偶数。现在你需要重新排列 $a_i$ 为 $b_i$ 。使 $b_1 < b_2 > b_3 < b_4 > ... > b_{n-1} < b_n$ 。且 $b_2 < b_4 < b_6 < ... < b_n$ 。问有多少种不同的方案，模 $998244353$ 。

$2 \le n \le 5000$，$1 \le a_i \le n$ 。

### 问题分析
先考虑 $a_i$ 两两不同的情况该如何计算。把 $b_i$ 在偶数位上的数成为“大数“，奇数位上的数成为“小数“，$dp[i][j]$ 表示已经选了 $i$ 个小数，$j$ 个大数的情况。

从大到小放入 $a_i$ ，显然如果选择当”大数“，显然只有最靠后的位置可以放，$dp[i][j+1] += dp[i][j]$ 。如果选择当小数，简单推到一下可以发现有 $\max(j-1,0) - i + [j==\frac{n}{2}]$ 个位置可以放。直接转移即可。答案是 $dp[\frac{n}{2}][\frac{n}{2}]$时间复杂度 $O(n^2)$。

有数字相同的情况的解法跟上面非常相似。还是从大到小考虑，相同的数中最多只能选一个数来当“大数”。“小数”的填空方案用组合数就可以解决。

##### code：
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=5005,M=998244353;
int T,n,cnt[N],C[N][N],dp[N][N];
void ck(int &x){(x>=M)&&(x-=M);}
void slv(){
    scanf("%d",&n);
    for(int i=1;i<=n;++i)cnt[i]=0;
    for(int i=1;i<=n;++i){
        int x;
        scanf("%d",&x);
        cnt[x]++;
    }
    for(int i=0;i<=n;++i)for(int j=0;j<=n;++j)dp[i][j]=0;
    int m=0;
    dp[0][0]=1;
    for(int i=n;i;i--)if(cnt[i]){
        for(int j=m;j>=m-j;--j){
            int p=max(j-1,0)+((j<<1)==n)-(m-j);
            if(p<0)continue;
            dp[m-j+cnt[i]][j]=(dp[m-j+cnt[i]][j]+1ll*dp[m-j][j]*C[p][cnt[i]])%M;
            dp[m-j+cnt[i]-1][j+1]=(dp[m-j+cnt[i]-1][j+1]+1ll*dp[m-j][j]*C[p][cnt[i]-1])%M;
        }
        m+=cnt[i];
    }
    printf("%d\n",dp[n/2][n/2]);
}
int main(){
    scanf("%d",&T);
    int n=5000;
    for(int i=0;i<=n;++i){
        C[i][0]=1;
        for(int j=1;j<=i;++j)ck(C[i][j]=C[i-1][j]+C[i-1][j-1]);
    }
    while(T--)slv();
}
```