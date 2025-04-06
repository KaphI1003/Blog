# TopCoder 7684 FastPostman

## 题意

[VJudge 链接](https://vjudge.net/problem/TopCoder-7684)

## 想法

（非唯一解法）

观察到邮递员的行动并不受每个位置上具体的信约束，因此直接计算每个位置所能接受的最晚到达时间。

由于 n 的范围达到了 50，因此不可能状压，只能考虑邮递员的行动是否存在规律。

一个很明显的结论：邮递员所新走到的端点一定处于他所走过的区间的边缘。因此，邮递员所走过的路径具有以下特点：他一直朝某个方向前进，一旦掉头就要一直走到原有端点之外。

由此得到 dp 的状态：用 $dp\left[i\right]\left[j\right]\left[k\right]$ 表示邮递员所经过的区间为 $\left[i,j\right]$，最后站在的位置如果是左端点 k 为 0，为右端点则 k 为 1 且沿途都遵守了时间所需的最少时间。

根据上面的讨论，可知每个状态要么是直接由之前的方向继续前进得到，要么是在某处掉头后得到。

如何处理题目中对邮递员的时间限制？一旦区间端点的时间限制小于当前状态的最优解，就意味着邮递员不能这么走，需要重新赋为无穷大。

## 代码

（代码中把 k 的一维放到了最前面）

```cpp
#include <bits/stdc++.h>

#define reint register int

using namespace std;

#define debug(x) cout<<#x<<" = "<<x<<endl;

typedef long long ll;

//ll powmod(ll a,ll b){ll res=1;a%=mod;for(;b;b>>=1){if(b&1)res=res*a%mod;a=a*a%mod;}return res;}

const int SIZE = 50 + 5;
const int INF  = 1 << 30;

int dp[2][SIZE][SIZE];
int tli[SIZE], mintime[SIZE];

inline void solve(int &a, int b) { // 因为是遗留代码改过来的所以其实完全没有必要
    if (a > b) a = b;
    return;
}

inline int dis(int a, int b) {
    return abs(tli[a - 1] - tli[b - 1]);
}

class FastPostman {
    public:
        int minTime(vector <int> address, vector <int> maxTime, int initialPos) {
            int n = address.size();

            // 离散化
            // 裆燃（划去）当然，也可以不离散化，只需要把原数组排序一下就行了
            for (reint i = 0; i < n; ++i) tli[i] = address[i];
            tli[n] = initialPos;
            sort(tli, tli + n + 1);
            int tcnt = unique(tli, tli + n + 1) - tli;
            initialPos = lower_bound(tli, tli + tcnt, initialPos) - tli + 1;
            for (reint i = 0; i <= n + 1; ++i) mintime[i] = INF;
            for (reint i = 0; i < n; ++i)
                address[i] = lower_bound(tli, tli + tcnt, address[i]) - tli + 1,
                mintime[address[i]] = min(mintime[address[i]], maxTime[i]);

            dp[0][initialPos][initialPos] = dp[1][initialPos][initialPos] = 0;
            for (reint i = initialPos; i; --i)
                for (reint j = initialPos; j <= tcnt; ++j) {
                    if (i != initialPos || j != initialPos) dp[0][i][j] = dp[1][i][j] = INF;

                    if (i < initialPos && dp[0][i + 1][j] + dis(i, i + 1) <= mintime[i])
                        solve(dp[0][i][j], dp[0][i + 1][j] + dis(i, i + 1));
                    if (i < initialPos && dp[1][i + 1][j] + dis(i, j) <= mintime[i])
                        solve(dp[0][i][j], dp[1][i + 1][j] + dis(i, j));

                    if (j > initialPos && dp[0][i][j - 1] + dis(i, j) <= mintime[j])
                        solve(dp[1][i][j], dp[0][i][j - 1] + dis(i, j));
                    if (j > initialPos && dp[1][i][j - 1] + dis(j - 1, j) <= mintime[j])
                        solve(dp[1][i][j], dp[1][i][j - 1] + dis(j - 1, j));
                }
            int ans = dp[0][1][tcnt];
            solve(ans, dp[1][1][tcnt]);
            return ans == INF ? -1 : ans;
        }
};
```
