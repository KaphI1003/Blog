# 小w的仙人掌

####  source：2019五校联考-镇海day2 T2

### 分析：
~~第一，这题跟仙人掌没有一点关系。~~

我们先要发现其中的**单调性**，对于一个符合条件的区间，包含它的区间一定也符合条件。那么对于这类题目一般都使用尺取来解决。但是,这道题中我们不能直接进行尺取，因为**直接DP是无法进行回撤操作的**，背包只能放不能拿...

那么我们就要换思路或想个办法来进行删除操作。这里就介绍如何进行删除。方法就是以“放”代“拿”。

开两个栈来模拟一个双端队列，插入就加到右边，删除就删左边，当左边空了的时候，就把右边的栈倒扣进去，重新DP，判断有无时直接O(w)扫描两个DP数组即可。

##### 时间复杂度：O(nw)
##### 空间复杂度：O(nw)

##### code:

```
#include<bits/stdc++.h>
#define min(x,y) ((x)<(y)? (x):(y))//卡常 
#define gc getchar()
using namespace std;
const int N=10005,inf=1e9+7;
int n,m,dp1[N][5005],dp2[5005],z1,z2,w,a[N],b[N],ans=inf;
bool pd(){
    const int cu=z2-z1+1;
    for(int i=0;i<=m;++i)if(dp1[cu][i]+dp2[m-i]<=w)return 1;
    return 0;
}
void rd(int &x){
    x=0;char c=gc;
    while(!isdigit(c))c=gc;
    while(isdigit(c)){x=(x<<1)+(x<<3)+c-48;c=gc;}
}
int main(){
    rd(n),rd(m),rd(w);
    for(int o=1;o<=m;++o)dp2[o]=dp1[0][o]=dp1[1][o]=inf;
    z1=z2=1;
    for(int i=1;i<=n;++i){
        rd(a[i]),rd(b[i]);
        for(int o=m;o>=a[i];--o)dp2[o]=min(dp2[o],dp2[o-a[i]]+b[i]);
        while(pd()){
            ans=min(ans,i-z1+1);
            if(z1==z2){//左边空了，把右边倒扣进去。 
                z1=z2+1;
                z2=i;
                for(int o=z2;o>=z1;--o){
                    const int cu=z2-o+1,uc=cu-1;
                    for(int u=a[o];u<=m;u++)dp1[cu][u]=min(dp1[uc][u],dp1[uc][u-a[o]]+b[o]);
                    for(int u=1;u<a[o];++u)dp1[cu][u]=dp1[uc][u];
                }
                for(int o=1;o<=m;++o)dp2[o]=inf;
            }
            else z1++;
        }
    }
    printf("%d",ans==inf? -1:ans);
}
```
