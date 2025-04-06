# [Opportunity Cost(G)](https://codeforces.com/gym/104633)

### 简要题面
有 $n$ 组数，每组三个，分别为 $x_i,y_i,z_i$，一组数的价值是
$$
val_i = \max_{1\le j \le n}({\max{(x_j-x_i,0)}+\max{(y_j-y_i,0)}+\max{(z_j-z_i,0)}})
$$
问价值最小是多少，以及对应的是第几组数。
### 问题分析
~~一个签到题写成树套树，傻逼！艹！！！~~
max 相加很显然的一个转化：
$$
\max{(a,b)}+\max{(c,d)}=\max{(a+c,b+c,a+d,b+d)}
$$

懂了吧，~~写尼玛树套树，傻逼。~~