# [Grand Finale](https://codeforces.com/gym/104821/problem/K)

### 简要题意
你现在有 $n$ 张手牌和 $m$ 张牌的牌堆。牌分为 $4$ 种：‘G’ 和 'W' 不能打出，'Q' 打出后从牌堆顶摸一张牌，'B' 打出后从牌堆顶摸两张牌。牌堆中从上到下每张牌的种类是确定的。有一个待求的手牌上限 $k$ 。如果手牌已满仍然摸牌，摸的牌会直接消失。求最小的 $k$ 满足有一种出牌顺序可以把牌堆摸完。显然 $n \le k$ 。

$1 \le n,m \le 2500$ 。满数据十组多测。
### 问题分析
明显的 DP 味扑面而来。但是一看变量有 $4$ 个：当前牌堆中牌的数量，当前手中 'Q' 和 ‘B’ 的数量，手牌上限。直接 DP 至少是 $O(n^3)$ 的。用了 $k$ 的可信单调性也是 $O(n^2 \log n)$ 的，通过不了。

因为求的是最小的 $k$ ，所以最后手牌一定达到上限，不然 $k$ 还可以更小。将 DP 分成两个阶段，手牌满了和手牌没满。

先考虑手牌满了的情况。设 $dp[i][j]$ 表示当前已经从牌堆中抽了 $i$ 张牌，手中有 $j$ 张 'B' 时最少需要几张 ‘Q’ 才能打完。这是 $O(n^2)$ 就可以解决的 DP 。

然后考虑手牌没满，设 $dp'[i][j]$ 表示当前已经从牌堆中抽了 $i$ 张牌，手中有 $j$ 张 'B' 时最多手上能有几张 'B' 。这是 $O(n^2)$ 就可以解决的 DP 。

然后我们考虑如何求得答案，显然当 $dp[i][j] \le dp'[i][j]$ 时，存在一个合法方案。而手牌没满时，只有打出 'B' 时手牌数才会发生改变。通过 $O(1)$ 计算到达 $dp'[i][j]$ 一共打出了几张 'B' ，即可得到此时的手牌上限 $k'$ 。在所有 $k'$ 中取最小就是题目的答案。

##### 时间复杂度 $O(nm)$
##### code:
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=5005;
int T,n,m,dp[N][N],DP[N][N],pre2[N],ans;
char a[N],b[N];
void Max(int &x,int y){(x<y)&&(x=y);}
void Min(int &x,int y){(x>y)&&(x=y);}
void slv(){
    scanf("%d %d",&n,&m);
    scanf("%s %s",a+1,b+1);
    int cnt1=0,cnt2=0;
    for(int i=1;i<=n;++i){
        if(a[i]=='Q')cnt1++;
        if(a[i]=='B')cnt2++;
    }
    ans=1e9;
    pre2[0]=cnt2;
    for(int i=1;i<=m;++i)pre2[i]=pre2[i-1]+(b[i]=='B');
    for(int i=0;i<=n+m;++i){
        DP[m][i]=DP[m+1][i]=0;
    }
    for(int i=m-1;~i;i--){//满手牌
        for(int j=0;j<=n+m;++j){
            DP[i][j]=m;
            if(b[i+1]=='Q'){
                Min(DP[i][j],max(DP[i+1][j],1));
                if(j)Min(DP[i][j],max(0,DP[i+2][j-1]-1));
            }else if(b[i+1]=='B'){
                Min(DP[i][j],DP[i+1][j+1]+1);
                if(j)Min(DP[i][j],DP[i+2][j]);
            }else {
                Min(DP[i][j],DP[i+1][j]+1);
                if(j)Min(DP[i][j],DP[i+2][j-1]);
            }
        }
    }
    for(int i=0;i<=m;++i){
        for(int j=0;j<=n+m;++j){
            dp[i][j]=-1;
        }
    }
    dp[0][cnt2]=cnt1;
    for(int i=0;i<m;++i){//没满手牌
        for(int j=0;j<=n+m;++j)if(~dp[i][j]){
            int w1=dp[i][j],w2=j;
            if(w1){
                cnt1=w1-1,cnt2=w2;
                if(b[i+1]=='Q')cnt1++;
                else if(b[i+1]=='B')cnt2++;
                Max(dp[i+1][cnt2],cnt1);
            }
            if(w2){
                cnt1=w1,cnt2=w2-1;
                if(b[i+1]=='Q')cnt1++;
                else if(b[i+1]=='B')cnt2++;
                if(b[i+2]=='Q')cnt1++;
                else if(b[i+2]=='B')cnt2++;
                Max(dp[min(m,i+2)][cnt2],cnt1);
            }
            if(dp[i][j]>=DP[i][j]){
                ans=min(ans,n+pre2[i]-j);
            }
        }
    }
    if(ans>1e8)puts("IMPOSSIBLE");
    else printf("%d\n",ans);
}
int main(){
    scanf("%d",&T);
    while(T--)slv();
}
```