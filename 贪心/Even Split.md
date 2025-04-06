# [# Even Split](https://qoj.ac/contest/988/problem/4571)
### 简要题意
有一个长 $l$ 的线段上有 $n$ 个定点，第 $i$ 个点的坐标为 $a_i$ ，你需要将线段剪成 $n$ 段，每段正好一个点（如果剪在点上，点可以分配给两条线段中的任意一条）。现在请设计一种方案，使剪出来的最长的线段和最短的线段的差最小，输出方案。

$2\le l \le 10^9$，$1 \le n \le 10^5$，$0 < a_1 <a_2 < ... < a_n < l$

### 问题分析
我们先找到最长线段的最小值。假设现在选出来的 $n$ 条线段长都为 $x$ 可以相交，从左到右覆盖点，那么我们就可以贪心的每次将右端点尽可能往右伸，最后判断能不能覆盖整条线段来确定 $x$ 合不合法。如此二分 $x$ 找到的最小值就是最长线段的最小值 $l_{max}$。

类似的，我们寻找最短线段的最大值。假设我们现在可以不用完全覆盖整个线段，从左到右覆盖点，那么我们就可以贪心的每次将右端点尽可能往**左**靠。最后判断能不能不相交将点全部覆盖。如此二分 $x$ 找到的最大值就是最短线段的最大值 $l_{min}$。

我们先计算一个数组 $c_i = \max\{c_{i-1} + l_{min}, a_i\}$ 表示第 i 条线段的下界（不是下确界）。然后倒推分割点。

设最后一个分割点为 $ans_n = l$ 。那么 $ans_{i-1} = \max\{ans_i-l_{max},c_i\}$ 。这样我们就可以保证线段的最小值最大同时最大值最小，也就是差最小。

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=1e5+5;
int n,L,a[N],c[N],d[N],ans[N];
bool ck_mx(int k){
    int nw=0;
    for(int i=1;i<=n;++i){
        if(nw+k<a[i])return 0;
        nw=min(nw,a[i])+k;
    }
    return nw>=L;
}
bool ck_mn(int k){
    int nw=0;
    for(int i=1;i<=n;++i){
        if(a[i]<nw)return 0;
        nw=max(a[i],nw+k);
    }
    return nw<=L;
}
int main(){
    scanf("%d %d",&L,&n);
    for(int i=1;i<=n;++i)scanf("%d",&a[i]);
    a[n+1]=L;
    int l=0,r=L,mx=L,mn=0;
    while(l<=r){
        int mid=(l+r)>>1;
        if(ck_mx(mid)){
            mx=mid,r=mid-1;
        }else l=mid+1;
    }
    l=0,r=L;
    while(l<=r){
        int mid=(l+r)>>1;
        if(ck_mn(mid)){
            mn=mid,l=mid+1;
        }else r=mid-1;
    }
    // printf("   __%d %d\n",mx,mn);
    for(int i=1;i<=n;++i){
        c[i]=max(c[i-1]+mn,a[i]);
        d[i]=min(d[i-1]+mx,a[i+1]);
        // printf("___%d %d\n",c[i],d[i]);
    }
    ans[n]=L;
    for(int i=n-1;i;i--){
        int l=max(c[i],ans[i+1]-mx),r=min(d[i],ans[i+1]-mn);
        ans[i]=l;
    }
    for(int i=0;i<n;++i)printf("%d %d\n",ans[i],ans[i+1]);
}
```
