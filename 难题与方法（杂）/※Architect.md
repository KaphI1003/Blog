# [Architect](https://codeforces.com/gym/104396/problem/L)
### 简要题面
给你一个左下角为$(0,0,0)$，右上角为$(W,H,L)$的三维长方体。以及$n$个小长方体，每个小长方体的左下角和右上角均给出。问这$n$个小长方体不移动是否图案和大长方体重合，即这$n$个小长方体是大长方体的一个划分。

$1 \le n \le 10^5, 0 \le W,H,L \le 10^9$

### 解法一：线段树扫描线
先按 z 轴从小到大扫描，将小正方体的下表面面积视为正，上表面视为负；大正方体的下表面面积视为负，上表面视为正。
那么对于任意的 z ，长方体的面上的每一块都要是 0 。所以我们每次移动 z 时，再做一次二维的扫描线，保证线段树的每一个点任意时刻都是0 即可。

##### 时间复杂度 $O(n \log n)$
##### code :
```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N = 2e6 + 5;
struct SegmentTree {
    struct Node {
        int l, r, lazy, max, min;
    }t[N << 2];
    #define ls(x) ((x) << 1)
    #define rs(x) (((x)<<1)|1)
    #define mid(x) ((t[x].l + t[x].r) >> 1)
    #define len(x) (t[x].r - t[x].l + 1)
    void build(int x, int l, int r) {
        t[x].l = l;
        t[x].r = r;
        t[x].max = 0;
        t[x].min = 0;
        t[x].lazy = 0;
        if(l == r) {
            return;
        }
        int mid = mid(x);
        build(ls(x), l, mid);
        build(rs(x), mid+1, r);
    }
    void push_down(int x) {
        if(t[x].lazy) {
            t[ls(x)].lazy += t[x].lazy;
            t[rs(x)].lazy += t[x].lazy;
            t[ls(x)].max += t[x].lazy;
            t[ls(x)].min += t[x].lazy;
            t[rs(x)].max += t[x].lazy;
            t[rs(x)].min += t[x].lazy;
            t[x].lazy = 0;
        }
    }
    void push_up(int x) {
        t[x].min = min(t[ls(x)].min, t[rs(x)].min);
        t[x].max = max(t[ls(x)].max, t[rs(x)].max);
    }
    void update(int x, int l, int r, int op) {
        // cout<<"!!!" << x <<" "<< l <<" "<< r <<" " << op << "\n";
        if(l > r) return;
        if(l <= t[x].l && t[x].r <= r) {
            t[x].lazy += op;
            t[x].max += op;
            t[x].min += op;
            return;
        }
        push_down(x);
        int mid = mid(x);
        if(l <= mid) update(ls(x), l, r, op);
        if(r >  mid) update(rs(x), l, r, op);
        push_up(x);
    }
    bool ask() {
        return t[1].max == t[1].min && t[1].max == 0;
    }
}seg;
struct plane {
    int x1, y1, x2, y2, z, op;
    bool operator < (const plane a) const {
        return z < a.z;
    }
};
struct Seg {
    int y1, y2, x, op;
    bool operator < (const Seg a) const {
        return x < a.x;
    }
};
int main() {
    #ifdef _DEBUG
        freopen("data.txt", "r", stdin);
    #endif
    ios::sync_with_stdio(false);
    cin.tie(0);
    int T;
    cin>>T;
    while(T--) {
        int W, H, L;
        int n;
        cin>>W>>H>>L>>n;
        __int128_t V = 0;
        vector<plane> p;
        vector<int> valx, valy, valz;
        for(int i=1;i<=n;i++) {
            plane top, bottom;
            cin>>bottom.x1>>bottom.y1>>bottom.z>>top.x2>>top.y2>>top.z;
            valx.push_back(bottom.x1);
            valx.push_back(top.x2);
            valy.push_back(bottom.y1);
            valy.push_back(top.y2);
            valz.push_back(bottom.z);
            valz.push_back(top.z);
            bottom.x2 = top.x2;
            bottom.y2 = top.y2;
            bottom.op = -1;
            top.x1 = bottom.x1;
            top.y1 = bottom.y1;
            top.op = 1;
            p.push_back(top);
            p.push_back(bottom);
            V += (__int128_t)(bottom.y2 - bottom.y1) * (bottom.x2 - bottom.x1) * (top.z - bottom.z);
        }
        if(V != (__int128_t) W * H * L) {
            cout<<"No\n";
            continue;
        }
        valx.push_back(0);
        valy.push_back(0);
        valz.push_back(0);
        valx.push_back(W);
        valy.push_back(H);
        valz.push_back(L);
        sort(valx.begin(), valx.end());
        valx.erase(unique(valx.begin(), valx.end()), valx.end());
        sort(valy.begin(), valy.end());
        valy.erase(unique(valy.begin(), valy.end()), valy.end());
        sort(valz.begin(), valz.end());
        valz.erase(unique(valz.begin(), valz.end()), valz.end());
        p.push_back((plane){0, 0, W, H, 0, 1});
        p.push_back((plane){0, 0, W, H, L, -1});
        vector<vector<int>> Z(valz.size()+1);
        for(auto &i:p) {
            i.x1 = lower_bound(valx.begin(), valx.end(), i.x1) - valx.begin();
            i.x2 = lower_bound(valx.begin(), valx.end(), i.x2) - valx.begin();
            i.y1 = lower_bound(valy.begin(), valy.end(), i.y1) - valy.begin();
            i.y2 = lower_bound(valy.begin(), valy.end(), i.y2) - valy.begin();
            i.z  = lower_bound(valz.begin(), valz.end(), i.z) - valz.begin();
        }
        for(int i = 0; i < (int)p.size(); i++) {
            Z[p[i].z].push_back(i);
        }
        int flag = 1;
        seg.build(1, 0, valy.size());
        for(int i = 0; i < (int)valz.size(); i ++) {
            vector<Seg> tmp;
            for(auto j:Z[i]) {
                tmp.push_back({p[j].y1, p[j].y2, p[j].x1, p[j].op});
                tmp.push_back({p[j].y1, p[j].y2, p[j].x2, -1 * p[j].op});
            }
            sort(tmp.begin(), tmp.end());
            int pre = -1;
            for(auto j:tmp) {
                // cout<<j.x << " "<<j.y1<<" "<<j.y2<<" "<<j.op<<"\n";
                if(j.x != pre) {
                    if(seg.ask() == false) {
                        // cout<<seg.t[1].max<<" "<<seg.t[1].min<<"\n";
                        flag = 0;
                        break;
                    }
                }
                seg.update(1, j.y1, j.y2 - 1, j.op);
                pre = j.x;
            }
            if(flag == 0) break;
        }
        if(flag == 0) cout<<"No\n";
        else cout<<"Yes\n";
    }
}
```

### 解法二：随机化

先把所有点全部离散化，设右上角为$(W',H',L')$，那么我们就可以得到$W'+H'+L'$个单位小段，如果小长方形的并等于大长方形，那么无论怎么拉伸单位小段的长度，两者的体积算出来都应该是相同的，而其他情况下则不然。

所以我们随机每条小段的长度，计算大长方体体积和小长方体体积和，如果两者不同输出"No"，如果多次重复随机两者一直相同，则两者相同的概率极大，可以输出 "Yes"。

##### 时间复杂度 $O(n \log n + kn)$

### 解法三：点的度数

这该是最简单的算法，除去四个顶角要出现恰好一次外，其它的顶点在小长方体中都应该要出现偶数次，同时验算一下两者体积是否相同防止重合即可。