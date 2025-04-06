# BOSS战
[BZOJ2964](http://10.220.121.203/judge/problem.php?id=6964)

### 题目描述
某RPG游戏中，最后一战是主角单挑Boss，将其简化后如下：

主角的气血值上限为HP，魔法值上限为MP，愤怒值上限为SP；Boss仅有气血值，其上限为M。

现在共有N回合，每回合都是主角先行动，主角可做如下选择之一：

1. 普通攻击：减少对方X的气血值，并增加自身DSP的愤怒值。（不超过上限）

2. 法术攻击：共有N1种法术，第i种消耗Bi的魔法值，减少对方Yi的气血值。（使用时要保证MP不小于Bi）

3. 特技攻击：共有N2种特技，第i种消耗Ci的愤怒值，减少对方Zi的气血值。（使用时要保证SP不小于Ci）

4. 使用HP药水：增加自身DHP的气血值。（不超过上限）

5. 使用MP药水：增加自身DMP的魔法值。（不超过上限）

之后Boss会攻击主角，在第i回合减少主角Ai的气血值。

刚开始时气血值，魔法值，愤怒值都是满的。当气血值小于等于{0}时死亡。

如果主角能在这N个回合内杀死Boss，那么先输出“Yes”，之后在同一行输出最早能在第几回合杀死Boss。（用一个空格隔开）

如果主角一定会被Boss杀死，那么输出“No”。

其它情况，输出“Tie”。


#### 输入
输入的第一行包含一个整数T，为测试数据组数。 
接下来T部分，每部分按如下规则输入： 
第一行九个整数N,M,HP,MP,SP,DHP,DMP,DSP,X。 
第二行N个整数Ai。 
第三行第一个整数N1，接下来包含N1对整数Bi,Yi。 
第四行第一个整数N2，接下来包含N2对整数Ci,Zi。

#### 输出
输出共包含T行，每行依次对应输出一个答案。

##### 样例输入
2
5 100 100 100 100 50 50 50 20
50 50 30 30 30
1 100 40
1 100 40
5 100 100 100 100 50 50 50 10
50 50 30 30 30
1 100 40
1 100 40
##### 样例输出
Yes 4
Tie
### 提示
对于10%的数据：N≤10，N1=N2=0。 
对于30%的数据：N≤10，N1=N2=1。 
对于60%的数据：N≤100，M≤10000，HP,MP,SP≤70。 
对于100%的数据：$1≤N≤1000,1≤M≤1000000，1≤HP,MP,SP≤1000,N1,N2≤10,DHP,Ai≤HP，D,Bi≤MP，DSP,Ci≤SP,X,Yi,Zi≤10000，1≤T≤10。$
#### 总思路:  分类DP
### 分析：
这道题中DP的因素有MP，HP，SP，以及回合数。如果把三个因素放在一起考虑，那么不仅时间不满足，空间也不允许。再仔细思考，我们就可以发现，
**HP，MP，SP三者所造成的贡献是互不干涉。** HP决定你可以有多少个回合可以进行攻击/嗑药，SP跟MP造成的伤害也是独立的（可以把普通攻击也看成SP的药，同时对BOSS造成伤害），所以我们可以分别求出到第i个回合主角最多可以攻击/嗑药几次；分配给MP i个回合最大可以造成多少伤害，分配给SP i个回合最大可以造成多少伤害。然后再将三者合并，即可求出答案。
code:
```
#include<bits/stdc++.h>
using namespace std;
const int N=1005;
int n,m,Hp,Mp,Sp,dh,dm,ds,x,sdp[N][N],mdp[N][N],hdp[N][N];
int a[N],n1,n2,ll,Ma,T,ma[N],mMa[N],sMa[N];
inline void Max(int &x,int y){if(y>x)x=y;}
struct P{
    int c,w;
    bool operator < (const P&t)const{
        if(c!=t.c)return c<t.c;
        return w>t.w;
    }
}tx[15],fs[15];
void so(){
    int ti=0;
    scanf("%d %d %d %d %d %d %d %d %d",&n,&m,&Hp,&Mp,&Sp,&dh,&dm,&ds,&x);
    for(int i=2;i<=n+1;++i){
        for(int o=1;o<=Hp;++o)hdp[i][o]=0;
        ma[i]=0;
    }
    hdp[1][Hp]=1;
    for(int i=1;i<=n;++i){
        scanf("%d",&a[i]);
        for(int o=1;o<=Hp;++o){
            if(!hdp[i][o])continue;
            Max(ma[i],hdp[i][o]);
            if(o-a[i]>0)Max(hdp[i+1][o-a[i]],hdp[i][o]+1);
            if(min(o+dh,Hp)-a[i]>0)Max(hdp[i+1][min(o+dh,Hp)-a[i]],hdp[i][o]);
        }
    }
    for(int i=1;i<=n;++i)Max(ti,ma[i]);
    scanf("%d",&n1);
    for(int i=1;i<=n1;++i){
        scanf("%d %d",&fs[i].c,&fs[i].w);
    }
    sort(fs+1,fs+n1+1);
    ll=0;Ma=x;
    for(int i=1;i<=n1;++i){
        if(fs[i].w>Ma){
            Ma=fs[i].w;
            fs[++ll]=fs[i];
        }
    }n1=ll;
    scanf("%d",&n2);
    for(int i=1;i<=n2;++i){
        scanf("%d %d",&tx[i].c,&tx[i].w);
    }
    sort(tx+1,tx+n2+1);
    ll=0;Ma=x;
    for(int i=1;i<=n2;++i){
        if(tx[i].w>Ma){
            Ma=tx[i].w;
            tx[++ll]=tx[i];
        }
    }n2=ll;
    for(int i=1;i<=ti;++i){
        for(int o=0;o<=Mp;++o)mdp[i][o]=0;
        for(int o=0;o<=Sp;++o)sdp[i][o]=0;
        mMa[i]=sMa[i]=0;
    }
    for(int i=1;i<=ti;++i){
        for(int o=0;o<=Mp;++o)Max(mdp[i][min(o+dm,Mp)],mdp[i-1][o]);
        for(int o=0;o<=Sp;++o)Max(sdp[i][min(o+ds,Sp)],sdp[i-1][o]+x);
        for(int o=1;o<=n1;++o){
            for(int u=Mp-fs[o].c;u>=0;--u){
                Max(mdp[i][u],mdp[i-1][u+fs[o].c]+fs[o].w);
            }
        }
        for(int o=1;o<=n2;++o){
            for(int u=Sp-tx[o].c;u>=0;--u){
                Max(sdp[i][u],sdp[i-1][u+tx[o].c]+tx[o].w);
            }
        }
        for(int o=0;o<=Mp;++o)Max(mMa[i],mdp[i][o]);
        for(int o=0;o<=Sp;++o)Max(sMa[i],sdp[i][o]);
    }
    for(int i=1;i<=ti;++i){
        int W=0;
        for(int o=0;o<=i;++o){
            W=max(W,mMa[o]+sMa[i-o]);
        }
        if(W>=m){
            for(int o=1;o<=n;++o){
                if(ma[o]>=i){
                    printf("Yes %d\n",o);
                    return;
                }
            }
        }
    }
    for(int i=1;i<=Hp;++i)Max(ma[n+1],hdp[n+1][i]);
    puts(ma[n+1]? "Tie":"No");
    hdp[1][Hp]=0;
}
int main(){
    scanf("%d",&T);
    while(T--)so();
}
```