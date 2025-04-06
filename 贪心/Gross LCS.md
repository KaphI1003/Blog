# [Gross LCS](https://qoj.ac/contest/825/problem/2622)
### 简要题意
有两组数列 $A,B$ ，长分别为 $n,m$ 。定义 $A+x$ 为 $<a_1+x,a+2+x,...,a_n+x>$ 。
对所有的 $x \in [-10^{100},10^{100}]$ 。求 $A+x$ 和 $B$ 的 LCS 的长度。输出所有 LCS 的长度和。

本题内存只有 32 MB，开不下 $n*m$ 的数组。

$1 \le n,m \le 4000$ ，$-10^8 \le a_i,b_i \le 10^8$。
### 问题分析
抛开内存限制，想想如何解决。

只有在 $x = b_j - a_i$ 时，$(i,j)$ 才会匹配上。那我们求出所有 $n*m$ 组 $(b_j-a_i,i,j)$ 。将 $(b_j - a_i)$ 相同的都放在同一组。对于一组，我们只需要找到最长的序列 $(i_k,j_k)$，满足 $i_{k-1} \le i_k$ 且 $j_{k-1 }\le j_k$ 即可。那么我们以 $i$ 为第一关键字升序，$j$ 为第二关键字降序排序后找到的最长上升子序列的长度就是对应的 $x=b_j-a_i$ 的答案。时间复杂度为 $O(n*m \log n)$

现在考虑内存限制，我们设 $P$ 是一个长为 $m$ 的排列，满足 $b_{p_1} \le b_{p_2} \le ...\le b_{p_m}$。当 $b_{p_x} = b_{p_y}$ 时，$x > y$。那么我们有 $b_{p_1}-a_i \le b_{p_2}-a_i \le ...\le b_{p_m}-a_i$ 。我们开一个小根堆优先队列，对于每个 $i$ ，先将 $(b_{p_1}-a_i,i,-p_1)$ 放入优先队列中。每次取出队首后，将 $i$ 对应的下一个 $(b_{p_j}-a_i,i,-p_j)$ 放入优先队列中（如果有的话）。这样我们就可以保证优先队列中不会超过 $n$ 个元素，且取出的 $x$ 单调递增。如果 $x$ 的值跟上次相同，就直接二分更新 LIS ；否则给最终答案添加上个 $x$ 的LIS长度，把 LIS 清空再做。空间复杂度 $O(n+m)$。

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N=4005;
int n,m,a[N],v[N],len,ans;
struct P{
    int x,i,j;
    bool operator < (const P&t)const{
        return (x!=t.x)? x > t.x : i > t.i;
    }
};
priority_queue<P>Q;
struct K{
    int w,id;
    bool operator < (const K&t)const{
        return (w!=t.w)? w < t.w : id > t.id;
    }
}b[N];
int main(){
    scanf("%d %d",&n,&m);
    for(int i=1;i<=n;++i)scanf("%d",&a[i]);
    for(int j=1;j<=m;++j){
        scanf("%d",&b[j].w);
        b[j].id=j;
        v[j]=m+1;
    }
    sort(b+1,b+m+1);
    for(int i=1;i<=n;++i)Q.push((P){b[1].w-a[i],i,1});
    int lx=1e9;
    while(!Q.empty()){
        P t=Q.top();
        Q.pop();
        if(t.j!=m){
            Q.push((P){b[t.j+1].w-a[t.i],t.i,t.j+1});
        }
        if(t.x!=lx){
            ans+=len;
            while(len)v[len--]=m+1;
        }
        lx=t.x;
        int w=b[t.j].id,l=1,r=len,rs=0;
        while(l<=r){
            int mid=(l+r)>>1;
            if(v[mid]<w){
                rs=mid,l=mid+1;
            }else r=mid-1;
        }
        v[rs+1]=w;
        len+=(len==rs);
    }
    printf("%d\n",ans+len);
    return 0;
}
```