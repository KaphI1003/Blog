# SZP

[题目描述](http://122.228.121.148:8222/judge/problem.php?cid=1112\&pid=3)

### 题目描述

Byteotian 中央情报局 (BIA) 雇佣了许多特工. 他们每个人的工作就是监视另一名特工. Byteasar 国王需要进行一次秘密行动,所以他要挑选尽量多的信得过的特工. 但是这项任务是如此的机密以至于所有参加行动的特工都必须至少被另一名没有参加任务的特工所监视( 就是说如果某个特工参加了行动,那么原先监视他的那些特工中至少要有一个没有参加进行动). 给出监视任务的详情,要求计算最多能有多少个特工参与其中.

#### 输入格式

第一行只有一个整数, n – 特工的总数, 2 <= n <= 1000000. 特工从1 到 n编号. 接下来n行每行一个整数ak 表示特工k将要监视特工ak , 1 <= k <= n, 1 <= ak <= n, ak <> k.

#### 输出格式

打印一个数,最多能有多少特工参加入这个任务.

#### 样例输入

6

2

3

1

3

6

5

##### 样例输出

3

#### 分析：

题目中给出的图是内向基环树森林，那么我们可以从叶子节点出发逐渐向内延伸。开两维的DP数组，分别记录该点选和不选的情况下算上子树的最大值。最后状态就都会汇集到圈上。然后就要考虑圈上的情况了。

在圈上时，我们就要先贪一波心，当环上一个点的father的两个DP值相同时明显这个点就一定要选。接着再来一次遍历，把还没选的在环上的点都挑出来，选一半，就可以了。

#### code：

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=1e6+5;
int n,to[N],s[N],h[N],e[N],dp[2][N],w[N],ans,rs;
bool v[N];
void dfs(int x){
    v[x]=1;
    if(!w[x]&&!v[to[x]]&&!w[to[x]]){
	    ans++;
	    w[to[x]]=1;
	}
    if(!v[to[x]])dfs(to[x]);
}
void se(int x){
    v[x]=1;s[to[x]]--;
    bool fl=0;
    for(int i=h[x];i;i=e[i]){
	    if(dp[0][i]==dp[1][i])fl=1;
	    dp[0][x]+=dp[1][i];
	}
    dp[1][x]=dp[0][x];
    dp[1][x]+=fl;
    if(!s[to[x]])se(to[x]);
}
int main(){
    scanf("%d",&n);
    for(int i=1;i<=n;++i){
	    scanf("%d",&to[i]);
	    s[to[i]]++;
	    e[i]=h[to[i]];
	    h[to[i]]=i;
	}
    for(int i=1;i<=n;++i){
	    if(!s[i]&&!v[i]){
		    se(i);
		}
	}
    for(int i=1;i<=n;++i){
	    if(!v[i]){
		    bool fl=0;
		    for(int o=h[i];o;o=e[o]){
			    if(!v[o]){continue;}
			    if(dp[0][o]==dp[1][o])fl=1;
			    dp[0][i]+=dp[1][o];
			}
		    if(fl)w[i]=1;
		    ans+=dp[0][i]+fl;
		}
	}
    for(int i=1;i<=n;++i){
	    if(!v[i]&&w[i])dfs(i);
	}
    for(int i=1;i<=n;++i){
	    if(!v[i])dfs(i);
	}
    printf("%d",ans);
}
```

