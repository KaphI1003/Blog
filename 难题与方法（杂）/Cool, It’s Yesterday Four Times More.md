# [Cool, It’s Yesterday Four Times More](https://codeforces.com/gym/104821)

### 简要题面
给定一张 n 行 m 列的网格，每个格子要么是洞，要么是空地。每个空地都恰好有一只袋鼠。
相似地，袋鼠可以被键盘上的 U，D，L，R 键控制。所有袋鼠会同时根据按下的按键移动。具体来说，对于一只位于第 i 行第 j 列的格子（用 (i, j) 表示）上的袋鼠：
1. 按键 U：它会移动到 (i − 1, j)。
2. 按键 D：它会移动到 (i + 1, j)。
3. 按键 L：它会移动到 (i, j − 1)。
4. 按键 R：它会移动到 (i, j + 1)。
如果一只袋鼠踩到了洞或者移动到了网格外面，它将被从网格上移除。如果完成一系列操作后（操作序列可以为空）恰有一只袋鼠留在网格上，那么这只袋鼠就是赢家。您需要解决的问题是：对于每只袋鼠，判断是否存在一个操作序列使得它成为赢家。输出可能成为赢家的袋鼠总数。

n × m 之和不超过 5000。
### 问题分析
设布尔参数 $b(x,y,x',y')$ ，为1表示位置在 (x,y) 的袋鼠可以在存活的情况下干掉 (x',y') 处的地鼠或(x',y')为边界或空洞。$b(x,y,x',y')$可以从$b(x±1,y±1,x'±1,y'±1)$转移过来，使用DFS或BFS即可。

##### 时间复杂度 $O(n^2m^2)$
##### code
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=1005,dir[4][2]={1,0,0,1,-1,0,0,-1};
int T,n,m;
char c[N][N];
struct P{
    int x,y,a,b;
};
queue<P>Q;
void slv(){
    scanf("%d %d",&n,&m);
    vector<vector<vector<vector<bool> > > >b(n+2,vector<vector<vector<bool> > >(m+2,vector<vector<bool> >(n+2,vector<bool>(m+2,0))));
    for(int i=1;i<=n;++i){
        scanf("%s",c[i]+1);
    }
    for(int i=1;i<=n;++i){
        for(int j=1;j<=m;++j)if(c[i][j]=='.'){
            for(int x=1;x<=n;++x){
                for(int y=1;y<=m;++y)if(c[x][y]=='O'){
                    b[i][j][x][y]=1;
                    Q.push((P){i,j,x,y});
                }
            }
            for(int x=1;x<=n;++x){
                b[i][j][x][0]=1;
                Q.push((P){i,j,x,0});
                b[i][j][x][m+1]=1;
                Q.push((P){i,j,x,m+1});
            }
            for(int y=1;y<=m;++y){
                b[i][j][0][y]=1;
                Q.push((P){i,j,0,y});
                b[i][j][n+1][y]=1;
                Q.push((P){i,j,n+1,y});
            }
        }
    }
    while(!Q.empty()){
        P t=Q.front();
        Q.pop();
        for(int i=0;i<4;++i){
            int nx=t.x+dir[i][0];
            int ny=t.y+dir[i][1];
            int na=t.a+dir[i][0];
            int nb=t.b+dir[i][1];
            if(nx>n||nx<1||ny>m||ny<1)continue;
            if(c[nx][ny]!='.')continue;
            if(na>n||na<1||nb>m||nb<1)continue;
            if(!b[nx][ny][na][nb]){
                b[nx][ny][na][nb]=1;
                Q.push((P){nx,ny,na,nb});
            }
        }
    }
    int ans=0;
    for(int i=1;i<=n;++i){
        for(int j=1;j<=m;++j)if(c[i][j]=='.'){
            bool fl=1;
            for(int x=1;x<=n&&fl;++x){
                for(int y=1;y<=m&&fl;++y){
                    if(i==x&&j==y)continue;
                    fl&=b[i][j][x][y];
                }
            }
            ans+=fl;
        }
    }
    printf("%d\n",ans);
}
int main(){
    scanf("%d",&T);
    while(T--){
        slv();
    }
    return 0;
}
```
