# The Race

#### POJ - 2274

给出n(n<=250000)辆赛车，i赛车初始在xi(xi从小到大给出)，速度为vi，赛车在比赛时会发生超车（不会相撞）。求超车次数%1000000，并输出前10000次(不足10000次有几次输出几次)超车，格式为x,y，表示x超过y，时间靠前的先输出，时间相同位置靠前的先输出，保证没有时间相同位置也相同的超车。

#### Input

第一行一个整数N(0 < N <= 250 000) ，接下来N行，每个两个整数 Xi 和Vi, (0 <= Xi <= 1000 000, 0 < Vi < 100) X1 < X2 < . . . < XN.
Output

输出超车发生的次数模 1 000 000。
接下来输出前10000次超车时间的编号x和y,表示x超过y。不足10000次，输出全部。
时间靠前的先输出，时间相同位置靠前的先输出，保证没有时间相同位置也相同的超车。

#### Sample Input

4

0 2

2 1

3 8

6 3

#### Sample Output

2

3 4

1 2

### 分析：

第一问可以使用O(Vn)的单调队列求法。也可以使用O(nlog(n))求逆序对的写法（归并，树状数组...）。

第二问比较难求，需要挖掘题目中的隐藏条件——每一次超车一定是在相邻的两辆车中发生的，于是可以把那些相邻的可超车的车都存到一个堆(按超车的时间大小建)中，每次超车都加入新的可超车点，并删去旧的节点。时间复杂度（O(10000\*log(10000))）,非常可观。

###### 代码如下：
```cpp
#include<cstdio>
#include<cmath>
#include<queue>
using namespace std;
const int M=1000000;
int n,a[250005],b[250005],c[250005],d[250005],l[250005],r[250005];
struct P{
	int x,y;
	double s,t;
	bool operator < (const P& x1)const{
		if(abs(t-x1.t)<=1e-8)return s>x1.s;
		return t>x1.t;
	}
}k;
priority_queue<P>Q;
long long ans;
void S(int x,int y){
	r[l[x]]=y;
	l[r[y]]=x;
	l[y]=l[x];
	r[x]=r[y];
	l[x]=y;
	r[y]=x;
	if(b[r[x]]!=-1&&b[r[x]]<b[x]){
		double h=1.0*(a[r[x]]-a[x])/(b[x]-b[r[x]]);
		Q.push((P){x,r[x],1.0*a[x]+h*b[x],h});
	}
	if(b[l[y]]!=-1&&b[y]<b[l[y]]){
		double h=1.0*(a[y]-a[l[y]])/(b[l[y]]-b[y]);
		Q.push((P){l[y],y,1.0*a[y]+h*b[y],h});
	}
}
void gb(int x,int y){
	if(x==y)return;
	int mid=x+y>>1,l=x-1;
	gb(x,mid);gb(mid+1,y);
	for(int i=x,o=mid+1;;){
		if(c[i]<=c[o]){
			d[++l]=c[i++];
			if(i>mid){
				while(o<=y)d[++l]=c[o++];
				break;
			}
		}
		else{
			d[++l]=c[o++];
			ans+=mid-i+1;
			if(o>y){
				while(i<=mid)d[++l]=c[i++];
				break;
			}
		}
	}
	for(int i=x;i<=y;++i){
		c[i]=d[i];
	}
	return;
}
int main(){
	scanf("%d",&n);b[0]=-1;
	for(int i=1;i<=n;++i){
		scanf("%d %d",&a[i],&b[i]);
		c[i]=b[i];
	}
	gb(1,n);
	printf("%lld\n",ans%M);
	for(int i=1;i<n;i++){
		if(b[i]>b[i+1]){
			double h=1.0*(a[i+1]-a[i])/(b[i]-b[i+1]);
			Q.push((P){i,i+1,1.0*a[i]+h*b[i],h});
		}
		r[i-1]=l[i+1]=i;
	}
	r[n-1]=l[n+1]=n;
	for(int i=1;i<=10000;++i){
		while(1){
			if(Q.empty())return 0;
			k=Q.top();Q.pop();
			if(r[k.x]==k.y)break;
		}
		S(k.x,k.y);
		printf("%d %d\n",k.x,k.y);
	}
}
```
    

