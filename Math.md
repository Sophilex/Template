# 矩阵与高斯消元

## 方程组消元

### $guass$消元（异或方程版）

```c++
const ll N=110;
//S:方程组，用bitsit存储
//all:每一个方程组的系数所在的位置
//len：方程组个数 
//now：当前处理到的方程 
//行列都是1索引
//n:变元个数
bitset<N> S[N];
ll o=0;
ll n=30;
int guass(ll all,ll len)
{
	int now=1,last=0;
	int cnt=0;
	for(int i=1;i<=n&&now<=len;++i)
	{
		int j=now;
		while(j<=len&&!S[j][i]) j++;
		if(j>len) continue;
		else cnt++;
		if(j!=now) swap(S[j],S[now]);
		for(int k=1;k<=len;++k)
		{
			if(k==now) continue;
			if(S[k][i]==0) continue;
			S[k]^=S[now];
		}
		now++; 
	}
//	for(int i=0;i<len;++i)
//	{
//		cout<<S[i]<<endl;
//	}
	for(int i=now;i<=len;++i)
	{
		if(S[i][all]==1) return 0;//方程无解
	}
	return 1;
}
```

$eg:$

5*6矩阵，每一个格子有亮暗初始状态，按下一个按钮会使自身以及周围四个格子改变亮暗状态。求使所有格子为暗的方案

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define endl '\n'
const ll N=110;
//S:方程组，用bitsit存储
//all:每一个方程组的系数所在的位置
//len：方程组个数 
//now：当前处理到的方程 
//行列都是1索引
//n:变元个数
bitset<N> S[N];
ll o=0;
ll n=30;
int guass(ll all,ll len)
{
	int now=1,last=0;
	int cnt=0;
	for(int i=1;i<=n&&now<=len;++i)
	{
		int j=now;
		while(j<=len&&!S[j][i]) j++;
		if(j>len) continue;
		else cnt++;
		if(j!=now) swap(S[j],S[now]);
		for(int k=1;k<=len;++k)
		{
			if(k==now) continue;
			if(S[k][i]==0) continue;
			S[k]^=S[now];
		}
		now++; 
	}
//	for(int i=0;i<len;++i)
//	{
//		cout<<S[i]<<endl;
//	}
	for(int i=now;i<=len;++i)
	{
		if(S[i][all]==1) return 0;//方程无解
	}
	return 1;
}
ll cn=0;
ll dir[][4]={{1,0},{-1,0},{0,1},{0,-1}};
bool inmap(ll x,ll y)
{
	return x>=1&&y>=1&&x<=5&&y<=6;
}
ll gt(ll x,ll y)
{
	return y+(x-1)*6;
}
void solve()
{
	memset(S,0,sizeof S); 
	cn=0;
	for(int i=1;i<=5;++i)
	{	
		for(int j=1;j<=6;++j)
		{
			ll a;cin>>a;
			S[++cn][31]=a;
			S[cn][gt(i,j)]=1;
			for(int k=0;k<4;++k)
			{
				ll xx=i+dir[k][0];
				ll yy=j+dir[k][1];
				if(!inmap(xx,yy)) continue;
				S[cn][gt(xx,yy)]=1;
			}
		}
	}
	
    cout<<"PUZZLE #"<<++o<<endl;
	guass(31,30);

	for(int i=1;i<=cn;++i)
	{
		cout<<S[i][31]<<" ";
		if((i)%6==0) cout<<endl;
	}
	
}
int main()
{
//	ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);
	ll t;cin>>t;while(t--)
	solve();
}
```



### $Guass$消元（普通方程版）

时间复杂度n^3 

可以判断是无解还是无数解 

0索引

```c++
#include<bits/stdc++.h>
using namespace std;
#define endl '\n'
#define ll int
const double eps=1e-6;
const ll N=110;
double S[N][N];
double x[N];
ll n;
int guass(int n, int m)//方程数，未知数个数 
{
    int c=0,r,maxr; 
    for(r=0;r<n&&c<m;r++,c++)
	{
        maxr=r;
        for(int i=r+1;i<n;i++)
		{
            if(abs(S[i][c])>abs(S[maxr][c]))
                maxr=i;
        }
        if(maxr!=r) swap(S[r], S[maxr]);
        if(fabs(S[r][c])<eps)
		{
            r--;
            continue;
        }
        for(int i=r+1;i<n;i++)
		{
            if(fabs(S[i][c])>eps)
			{
                double k=S[i][c]/S[r][c];
                for(int j=c;j<m+1;j++) S[i][j]-=S[r][j]*k;
                S[i][c]=0;
            }
        }
    } 
    for(int i=r;i<m;i++)
	{
    	if(fabs(S[i][c])>eps) return -1;//无解
    }    
    if(r<m) return m-r;//返回自由元个数
    for(int i=m-1;i>=0;i--)
	{
        for(int j=i+1;j<m;j++) S[i][m]-=S[i][j]*x[j];
        x[i]=S[i][m]/S[i][i];
    }
    return 0;//有唯一解
}
void solve()
{
	cin>>n;
	for(int i=0;i<n;++i)
	{
		for(int j=0;j<n+1;++j)
		{
			cin>>S[i][j];
		}
	}
	int ans=guass(n,n);
	if(ans!=0) cout<<"No Solution"<<endl;
	else for(int i=0;i<n;++i) cout<<fixed<<setprecision(2)<<x[i]<<endl;
}
int main()
{
	ios::sync_with_stdio(false);cin.tie(0);cout.tie(0);
	solve();
	return 0;
}
```

### 高斯-约旦消元

复杂度n^3

只能判断是否有唯一解，无法判断究竟是无解还是无数解

但是精度高 

1索引

```c++
#include<bits/stdc++.h>
using namespace std;
#define endl '\n'
#define ll int
const double eps=1e-6;
const ll N=110;
double S[N][N];
ll n;
int pl;
int guass(int n, int m)//方程数，未知数个数 
{
    for(int i=1;i<=n;i++)
    {
        pl=i;
        for(int j=i;j<=m;j++) {
        	if(S[j][i]>S[pl][i]) pl=i;
		}                                     
        if(S[pl][i]==0) {return 0;} //无解或有自由元   
        for(int j=1;j<=m+1;j++)
        swap(S[i][j],S[pl][j]);
        double k=S[i][i];
        for(int j=1;j<=m+1;j++)
        S[i][j]=S[i][j]/k; 
        for(int j=1;j<=n;j++)
        {
            if(i!=j)
            { 
                double ki=S[j][i];
                for(int w=1;w<=m+1;w++)
                S[j][w]=S[j][w]-ki*S[i][w];
            }
        }
    }
}
void solve()
{
	cin>>n;
	for(int i=1;i<=n;++i)
	{
		for(int j=1;j<=n+1;++j) cin>>S[i][j];
	}
	int ans=guass(n,n);
	if(ans==0) cout<<"No Solution"<<endl;
	else for(int i=1;i<=n;++i) cout<<fixed<<setprecision(2)<<S[i][n+1]<<endl;
}
int main()
{
ios::sync_with_stdio(false);cin.tie(0);cout.tie(0);
	solve();
	return 0;
}
```

### Gauss消元求行列式

复杂度$n^3$

```c++
int z[N][N];int n;

inline int guass(){
	int x=1;
	for(int a=1;a<=n;a++)
		for(int b=a+1;b<=n;b++)
			while(z[b][a]!=0){
				int k=z[a][a]/z[b][a];
				for(int c=1;c<=n;c++) z[a][c]-=z[b][c]*k;
				for(int c=1;c<=n;c++) swap(z[a][c],z[b][c]);
				x=-x;
			}
	for(int i=1;i<=n;i++) x*=z[i][i];
	return x;
}

```



## 矩阵快速幂

```c++
const ll mod=1e9+7;
ll n,t;
struct mat
{
	ll m[110][110];//结构体存矩阵 
};
mat a,e;//输入输出矩阵
mat mul(mat x,mat y,ll n) //矩阵乘法，n代表矩阵的规模 
{
	mat c;
	for(int i=1;i<=n;++i)
	{
		for(int j=1;j<=n;++j)
		{
			c.m[i][j]=0;//初始化 
		}
	}
	for(int i=1;i<=n;++i)
	{
		for(int j=1;j<=n;++j)
		{
			for(int k=1;k<=n;++k)
			{
				c.m[i][j]=c.m[i][j]%mod+x.m[i][k]%mod*y.m[k][j]%mod;
			}
		}
	}
	return c;
} 

ll qpow(mat x,ll y,ll mod)
{
	mat ans=e;//一般是一个对角为1的初始矩阵 ，记得初始化 
	//x是变换矩阵 
	while(y)
	{
		if(y&1) ans=mul(ans,x,6);
		x=mul(x,x,6);
		y>>=1;
	}
	return (ans.m[1][1]+ans.m[1][3]*8+ans.m[1][4]*4+ans.m[1][5]*2+ans.m[1][6])%mod;
    //之前得到的是变换矩阵的y次幂，最后将其与初始矩阵相乘即可 
}
```

# 容斥原理

设$S$是一个有限集，$A_1,A_2...A_n是S的n个子集$，则

$|S-\bigcup_{i=1}^{n}A_i|=\sum_{i=0}^{n}(-1)^i\sum_{1\leq j_1< j_2...<j_i \leq n}|\bigcap_{k=1}^{i}A_{j_k}|$

## 基本应用：

---

m件不同的物品，分给n个人，要求每一个人至少分得一件物品，求不同的分配方案数

令$A_i$表示第i个人没有物品，S表示m个物品分给n个人的总方案数

则$|S-\bigcup_{i=1}^{n}A_i|=\sum_{i=0}^{n}(-1)^i\sum_{1\leq j_1< j_2...<j_i\leq n}|\bigcap_{k=1}^{i}A_{j_k}|$

$=\sum_{i=0}^{n}(-1)^i\binom{n}{i}(n-i)^m$

---

有 $2n 个元素 a_1,a_2, ...,a_n 和 b_1,b_2, ...,b_n$，求有多少个它们的全排列，满足任意的

$1 ≤i≤n， a_i 和 b_i$ 都不相邻。

同样的，令$A_i$表示$a_i和b_i$相邻，

则$|S-\bigcup_{i=1}^{n}A_i|=\sum_{i=0}^{n}(-1)^i\sum_{1\leq j_1< j_2...j_i \leq n}|\bigcap_{k=1}^{i}A_{j_k}|$

$=\sum_{i=0}^{n}(-1)^i\binom{n}{i}|有i对相邻的方案数|$

$=\sum_{i=0}^{n}(-1)^i\binom{n}{i}2^i*(2n-i)!$

---

[23HDU Cargo]: http://acm.hdu.edu.cn/contest/problem?cid=1102&pid=1011

大意：

n家店，每家店卖一种商品，总共有m种商品在卖。k次操作，每次随机选择一家店并且买下对应种类的商品。对于每一种商品i，记ci为售卖该种商品的店数，如果k次操作手里刚好有ci种i商品，且分别来自不同的店，则该商品合法。问k次操作之后所有种类的商品都不合法的方案数%998244353

$n,m\leq 1e5,k<998244353$

思路：

可能数据范围并不是很支持直接容斥，但是可以发现基本上就是容斥的样子，所以试试看优化容斥。

令$A_i$表示第种商品合法的方案数，

则$|S-\bigcup_{i=1}^{m}A_i|=\sum_{i=0}^{m}(-1)^i\sum_{1\leq j_1< j_2...j_i}|\bigcap_{k=1}^{i}A_{j_k}|$

只考虑$A_i$，方案数为$A_{n}^{c_i}(n-c_i)^{k-c_i}$,意义显然。多个$A_i$取交的话，方案数就是$A_{n}^{\sum c_i}(n-\sum c_i)^{k-\sum c_i}$

发现这个方案数只与$\sum c_i$有关，而与顺序，某一个具体值无关，所以我们可以改变一下思路，转为枚举$\sum c_i$

具体来说，对于每一个$\sum c_i=j$,它的贡献就是用偶数个$c_i$累加得到j的方案数减去用奇数个$c_i$累加得到j的方案数，（这一步其实就是容斥原理公式的转化）

既然如此，我们考虑生成函数$1-x^{c_i}$，表示对于每一个$c_i$，取一个数得到和为$c_i$的方案数为-1，这是因为1是一个奇数，那么显然，在$\prod (1-x^{c_i})$中，如果某一个指数是由偶数个数累加得到的，其系数自然会是正数，奇数同理。由此该生成函数就满足了我们的条件，我们只要对每一项$[x_j](\prod (1-x^{c_i}))$乘上对应的方案数系数$A_{n}^{\sum c_i}(n-\sum c_i)^{k-\sum c_i}$即可.

个人感觉这种题目应该还是有点套路可循的，因为如果数据范围小一点的话，就是明显显的容斥，然后找到性质用生成函数来优化就不是那么难想到的了





[18青岛G](https://codeforces.com/gym/104270/problem/G)

大意：
给定一个由0,1，2组成的长度为n的数列，每次操作可以选择一个不含2的连续区间，将其中所有元素置为0.求**恰好操作m次**并且所有2都被消除的方案数,$n\leq 100,m\leq 10^9$
思路：

不妨记一个不含1的连续区间是一个合法区间，那么仔细观察一下，对于一个长度为$len$的合法区间，其操作的合法区间数是$\binom{len}{2}+len=\frac{len(len+1)}{2}$，所以其答案就是$(\frac{len(len+1)}{2})^m$,是可以$O(1)$计算得到的。这启示我们使用容斥来处理该问题 ，令$A_i$表示从左往右数第$i$个2没有消除的方案数，要求$ans=|S-A_1\bigcup A_2...\bigcup A_x|=\sum_{i=0}^{x}(-1)^i|A_{j_1}\bigcap A_{j_2}...\bigcap A_{j_i}|$
但是显然数据范围并不允许我们直接暴力枚举，所以考虑dp优化（很常见的思路）。

显然$|A_{j_1}\bigcap A_{j_2}...\bigcap A_{j_i}|$前面的系数由$i$，也就是不消除的2的个数决定,如果是奇数，系数就是-1，并且此时其他的2是不能消除的。所以不妨记$dp_{i,k,0/1}$表示前i个位置，合法操作区间数为k，总共有奇数/偶数个不能消除的2

为了方便计算，令$a_0=a_{n+1}=1$,这不会影响结果

那么最终我们的答案就是$\sum_{j=1}^{n(n+1)/2}(dp_{n+1,j,0}-dp_{n+1,j,1})j^m$,也就是枚举合法操作方案数为j的情况数

考虑转移，对于当前位置i，我们枚举上一个不消除的位置j，$pre_i\leq j<i,a_j=2$,其中$pre_i$表示i之前的第一个1的位置，因为1是一定不能消除的。此时$\large dp_{i,k+\frac{(i-j)(i-j+1)}{2},0/1}=\sum_{j=pre_i}^{i-1}dp_{j,k,1/0}$

最后总体复杂度$O(n^4+log(m))$

最后要特判一下1的个数，如果是奇数的话，显然改变了实际2的个数，所以答案要取负

```c++
ll dp[N][N*N][3];
ll n,m;
ll mas[N];
ll lim[N];//前i个点的最多的合法区间数
void solve()
{
    cin>>n>>m;
    for(int i=1;i<=n;++i) cin>>mas[i];
    mas[0]=mas[n+1]=1;
    dp[0][0][1]=1;dp[0][0][0]=0;lim[0]=0;
    ll pre=0;//上一个1的位置
    for(int i=1;i<=n+1;++i)
    {
        if(mas[i]==0) continue;
        lim[i]=lim[pre]+(i-pre)*(i-pre-1)/2;
        for (int j=0;j<=lim[i];++j) dp[i][j][0]=dp[i][j][1]=0;
        for(int j=pre;j<i;++j)
        {
            if(mas[j]==0) continue;
            ll add=(i-j)*(i-j-1)/2;
            for(int k=0;k<=lim[j];++k)
            {
                dp[i][k+add][0]=(dp[i][k+add][0]+dp[j][k][1])%mod;
                dp[i][k+add][1]=(dp[i][k+add][1]+dp[j][k][0])%mod;
            }
        }
        if(mas[i]==1) pre=i;
    }
    ll ans=0;
    for(int i=0;i<=lim[n+1];++i)
    {
        ll det=((dp[n+1][i][0]-dp[n+1][i][1])%mod+mod)%mod;
        ans=(ans+det*ksm(i,m)%mod)%mod;
    }

    ll cn=0;
    for(int i=1;i<=n;++i) if(mas[i]==1) cn++;
    if(cn%2) ans=(mod-ans)%mod;

    cout<<ans<<endl;
}
```



# 概率&期望

感觉自己概率/期望方面的水平实在堪忧，新开一个坑记录一下训练日常，以及一些有意思的trick

---

## 一些常见套路：

利用期望的线性性将全局的计算转化为对某一个点的计算

利用转移方程的递推得到n个方程，然后嗯解

在DAG上做dp



[F. Jellyfish and EVA]([Problem - F - Codeforces](https://codeforces.com/contest/1875/problem/F))

给定一张n个点的DAG，每一个有向边的起点的序号<终点的序号。在某一个点尝试走下一个点时，AB两个人同时选择一条出边，如果选择相同，则前进至对应点，否则两条边被删去。现在已知A每次随机选边，问B在最优选择的决策下成功从1走到n的概率

$n\leq 5000$

注意到A每次随机选边，所以B每次应该优先选择成功概率最大的点对应的边。所以考虑$dp_{i,j}$表示有i个后继节点的时候选到排名为j的点的概率。考虑转移，首先我们第一次一定先选排名第1的点，成功的概率为$\frac{1}{i}$,否则失败，然后选排名为j的点，此时枚举A选的点来进行转移，这个点可以是排名在j之前的，也可以是j之后的，但不能是j，也不能是1，因为转移失败了

边界条件$dp_{1,1}=0,dp_{2,1}=0.5,dp_{2,2}=0$

之后按照权值全部把贡献加起来即可

```c++
void init(ll n)
{
    dp[1][1]=1;dp[2][1]=0.5;
    for(int i=3;i<=n;++i)
    {
        dp[i][1]=1.0/i;
        for(int j=2;j<=i;++j)
        {
            double p1=(j-2)*1.0/i;
            double p2=(i-j)*1.0/i;
            dp[i][j]=p1*dp[i-2][j-2]+p2*dp[i-2][j-1];
            //先选第一个，然后按照概率择优
        }
    }
}
vector<ll> vt[N];
ll n,m;
double ans[N];
bool cmp(ll a,ll b)
{
    return ans[a]>ans[b];
}
void solve()
{
    cin>>n>>m;
    for(int i=1;i<=n;++i) vt[i].clear(),ans[i]=0;
    for(int i=1;i<=m;++i)
    {
        ll a,b;cin>>a>>b;
        vt[a].push_back(b);
    }
    ans[n]=1;
    for(int id=n-1;id>=1;--id)
    {
        ll deg=vt[id].size();
        sort(vt[id].begin(),vt[id].end(),cmp);
        double ma=0,p=0;
        for(int i=0;i<deg;++i)
        {
            ll idx=vt[id][i];
            ma+=ans[idx]*dp[deg][i+1];
        }
        ans[id]=ma;
    }
    cout<<fixed<<setprecision(10)<<ans[1]<<endl;
}

```



[G. Jellyfish and Miku]([Problem - G - Codeforces](https://codeforces.com/contest/1875/problem/G))

0-n共n+1个点，$a_i$表示从i-1连向i的有向边的权值。站在i点，设其所有连边的权值和为$sum$,则走向权值为x的边的概率是$\frac{x}{sum}$,现在要求自己构造所有$a_i$，并满足$\sum a_i\leq m$，使得从0到n的期望步数最小，并求该值

$n,m\leq 3000$

设$dp_i$表示从0第一次走到i的期望步数，不难得到下式

$\large dp_i=dp_{i-1}+1+\frac{a_{i-1}}{a_{i-1}+a_i}(dp_{i}-dp_{i})$,意义就是从i-1走到i的时候，要不就是一步到位，要不就是撤回到i-2，那么按照dp的定义将差值补上即可

转化一下得到：$dp_i=dp_{i-1}+1+\frac{a_{i-1}}{a_i}(dp_{i-1}-dp_{i-2}+1)$

按照套路令$g_i=dp_i-dp_{i-1}$,不难得到$g_i=1+2\sum_{j=1}^{i-1}\frac{a_j}{a_i}$,由此$f_n=\sum_{i=1}^{n}g_i=n+2\sum_{i=2}^{n}\sum _{j=1}^{i-1}\frac{a_j}{a_i}$

到这里我们用一个前缀和，再来一个简单dp即可完成转移。但是复杂度是$O(nm^2)$,考虑优化

仔细观察$f_n$的递推式，不难得到$\forall j<i,a_j\leq a_i$,因为如果存在$j<i,a_j>a_i$的话，交换$a_i,a_j$，自己推一下就不难发现，值是一定会变小的。

由此$(n-i+1)a_i\leq m$,故$a_i\leq \frac{m}{n-i+1}$

这样对于每一个$a_i$的枚举的和就只有$mlogm$了，总体复杂度来到了$m^2logm$

```c++
void solve()
{
    double ans=1e18;cin>>n>>m;
    for(int i=2;i<=n;++i) for(int j=1;j<=m;++j) dp[i][j]=1e9;
    for(int i=2;i<=n;++i)
    {
        for(int a=1;a<m;++a)
        {
            for(int b=1;b+a<=m&&b<=m/(n-i+1);++b)
            {
                dp[i][a+b]=min(dp[i][a+b],dp[i-1][a]+1.0*a/b);
            }
        }
    }
    for(int i=1;i<=m;++i) ans=min(ans,dp[n][i]);
    cout<<n+2*ans<<endl;
}
```



[D. Flexible String Revisit]([Problem - 1778D - Codeforces](https://codeforces.com/problemset/problem/1778/D))

给定01串a,b，每次操作随机翻转a的某一位，问期望操作多少次使得a=b

不难发现每一位其实都是独立的，所以我们可以考虑$dp_i$表示当前a与b有i位不同，转移方程也很好写

$dp_i=\frac{i}{n}(dp_{i-1}+1)+\frac{n-i}{n}(dp_{i+1}+1),i\in [1,n-1],dp_0=0$

但是注意到这个式子需要从前面转移，也需要从后面转移，实际上是无法实现的，所以我们需要转化一下。

首先$dp_1=\frac{1}{n}(dp_0+1)+\frac{n-1}{n}(dp_2+1)=\frac{n-1}{n}dp_2+1$,由此我们可以用$dp_1$来表示$dp_2$

同理，$\forall i \in[2,n]$，$dp_i$都能用上式来转化成$dp_1$,这样我们就有了$n-1$个方程，最后$dp_n=dp_{i-1}+1$（显然）,我们就有了$n$个方程，那么将每一个$dp_i$用$dp_1$表示，再用最后一个方程求解一下即可

```c++
void solve()
{
    cin>>n;cin>>s1>>s2;
    for(int i=0;i<n;++i)
    {
        if(s1[i]!=s2[i]) cnt++;
    }
    coef[1][0]=1;
    for(ll i=1;i<n;++i)
    {
        coef[i+1][0]=((n*iv[n-i]%mod*coef[i][0]%mod-i*iv[n-i]%mod*coef[i-1][0]%mod)%mod+mod)%mod;
        coef[i+1][1]=((n*iv[n-i]%mod*coef[i][1]%mod-i*iv[n-i]%mod*coef[i-1][1]%mod-n*iv[n-i]%mod)%mod+mod)%mod;
    }
    ll dp1=((coef[n-1][1]-coef[n][1]+1)%mod+mod)%mod;
    ll fm=((coef[n][0]-coef[n-1][0])%mod+mod)%mod;
    dp1=dp1*inv(fm)%mod;

    cout<<(dp1*coef[cnt][0]%mod+coef[cnt][1])%mod<<endl;
}
```



有一棵 $n≤10^5$ 节点的树，每次随机选一个未删除的节点，删除它以及它的子树中的所有节点。当树为空时，期望经过了多少次删除操作

不妨将每一次删除操作的贡献固定在子树的根节点上，也就是说，一次删除操作之后，该根节点产生了1的贡献，其他节点没有贡献，也不会再有贡献。考虑一个点i被删除的条件，是它到根节点，一共$dep_i$个点中，在任意一个点执行了一次删除操作。那么点i能产生贡献的条件就是这一次删除操作恰好发生在它本身，概率显然就是$\frac{1}{dep_i}$.根据期望的线性性，答案就是$\sum\frac{1}{dep_i}$



# 积性函数学习笔记

重学了一遍积性函数，涉及积性函数的递推，反演，杜教筛等

## 积性函数

### 定义：

对于一个$\mathbb{N}\Rightarrow \mathbb{R}$的函数f

$\forall i,j,(i,j)=1\Rightarrow f(i)*f(j)=f(i*j)$，则f称为积性函数

特别的，如果$\forall i,j,f(i)*f(j)=f(i*j)$,则f称为完全积性函数

一些典型的积性函数：

$\epsilon (i)=[i=1]$

$id_k(i)=i^k$,它也是一个完全积性函数，证明显然

$\phi(i)$ 欧拉函数

$\mu(i)$ 莫比乌斯函数

1(i)=1 

$\sigma(i)$ 约数个数

$\Delta(i)$ 约数和

下面挑两个简单证明一下其为什么是积性函数

---

首先，对于n，我们可以将其进行质因数分解，

$n=\prod_{i}p_i^{\alpha_i}$

考虑约数个数函数$\sigma(n)=\prod_{i}(\alpha_i+1)$

如果m,n互质，显然它们不含相同的质因子

我们记$n=\prod_{i}p_i^{\alpha_i}$,$m=\prod_{j}q_i^{\beta_i}$,则$m*n=\prod_{i}p_i^{\alpha_i}\prod_{j}q_j^{\beta_j}$

有$\sigma(n)=\prod_{i}(\alpha_i+1),\sigma(m)=\prod_{i}(\beta_i+1)$

$\sigma(m*n)=\prod_{i}(\alpha_i+1)*\prod_{j}(\beta_j+1)=\sigma(n)*\sigma(m)$

得证

再来看看约数和函数$\Delta(n)=\prod_{i}(p_i^0+p_i^1+...+p_i^{\alpha_i})$

与上一个分析一样，因为互质的两个数字没有相同的质因子，那么直接带入就有同样的答案。

后面碰到各种各样的函数的时候，采用类似的方法基本上都可以分析出来其是否是一个积性函数

### 性质



* 如果两个$\mathbb{N}\Rightarrow \mathbb{R}$的函数$f,g$都为积性函数，则其乘积也为积性函数：

  令$h(i)=f(i)*g(i)$,考虑$(m,n)=1$

  $h(mn)=f(mn)g(mn)=f(m)f(n)g(m)g(n)=(f(m)g(m))(f(n)g(n))$

  得证

* 如果两个$\mathbb{N}\Rightarrow \mathbb{R}$的函数$f,g$都为积性函数，则其范德蒙德卷积也为积性函数

  证明在后文给出，这是一个很重要的结论，后面各种题目里都会用到这个性质

* 如果$\mathbb{N}\Rightarrow \mathbb{R}$的函数$f$为积性函数,对于$n=\prod_{i}p_i^{\alpha_i}$,$f(n)=\prod_{i}f(p_i^{\alpha_i})$

  这是显然的，因为不同质数的幂次之间显然互质

  

### 线性推积性函数

我们都知道欧拉筛可以线性求质数，其原理就是用每一个合数的最小质因子来筛掉它。利用这一特点我们可以完成线性得到积性函数

因为积性函数的各个质因子独立，所以我们可以将其拆分为若干个函数之积

记$cal(x,num)$为求f值的函数，x为素数，$num$为其次数。利用此函数，加上整数的质因子拆分，我们在理论上就已经可以求得任意数的f值了。再用cn数组用于记录数字的最小质因子对应的次数。为什么是最小质因子？因为每一个合数只会被其最小质因子筛到，所以我们维护其它质因子也没有意义。

对于所有的积性函数$f，f[1]=1$恒成立，不然就不满足上述积性函数的性质了

下面给出模板，一些解释也都放在里面了

```c++
ll vis[N];
ll cn[N];//记录最小质因子的对应次数,显然质数x的cn值为1 
ll cal(ll x,ll num)
{
	//x为素数，num为其次数,返回对应的积性函数值
}
void jixing(ll n)
{
	f[1]=1;
	for(int i=2;i<=n;++i)
	{
		if(!vis[i])
		{
			p[++cnt]=i;
			f[i]=cal(i,1);
			cn[i]=1;
		}
		for(int j=1;j<=cnt&&i*p[j]<=n;++j)
		{
			vis[i*p[j]]=1;
			//i*p[j]的最小质因子一定为p[j] 
			if(i%p[j]==0)
			{
				//i*p[j]的最小质因子的次数=i对应的p[j]的次数+1 
				cn[i*p[j]]=cn[i]+1;
				//首先去掉f[i]中p[j]对应的值,然后更新当前次数下p[j]对应的值 
				f[i*p[j]]=f[i]/cal(p[j],cnt[i])*cal(p[j],cnt[i]+1); 
				break;
			}
			cn[i*p[j]]=1;
			f[i*p[j]]=f[i]*cal(p[j],1);
		}
	}
} 
```

在此基础上，我们可以看看如何线性求约数函数$\sigma(n)$

$f(n)=\sigma(\prod_{i}p_i^{\alpha_i})=\prod_{i}(\alpha_i+1)$,故枚举i的时候

若是质数，$f(i)=2,cn[i]=1$

枚举倍数的时候，$f(i*p[j])=\left\{\begin{matrix}
f(i)*2 & imodp[j]\neq0 \\ 
f[i]/(cn[i]+1)*(cn[i]+2) & imodp[j]=0
\end{matrix}\right.$

$cn[i]=\left\{\begin{matrix}
1 & imodp[j]\neq0 \\ 
cn[i]+1 & imodp[j]=0
\end{matrix}\right.$

模板

```c++
ll vis[N];
ll cn[N];//记录最小质因子的对应次数,显然质数x的cn值为1 
ll p[N];
ll f[N];
ll cnt=0;
void F(ll n)
{
	f[1]=1;
	cn[1]=1;
	for(int i=2;i<=n;++i)
	{
		if(!vis[i]) 
		{
			f[i]=2;
			p[++cnt]=i;
			cn[i]=1;
		}	
		for(int j=1;j<=cnt&&i*p[j]<=n;++j)
		{
			vis[i*p[j]]=1;
			if(i%p[j]==0)
			{
				cn[i*p[j]]=cn[i]+1;
				f[i*p[j]]=f[i]/(cn[i]+1)*(cn[i]+2);
				break;
			}
			cn[i*p[j]]=1;
			f[i*p[j]]=f[i]*2;
		}
	}	
} 
```

对于欧拉函数$\phi(n)=\phi(\prod_{i}p_i^{\alpha_i})=\prod_{i}(p_i-1)p_i^{\alpha_i-1}$(证明有一点繁琐，这里不予展示，可以自行百度)，它也是一个积性函数，所以线性求欧拉函数的时候：

若是质数，$\phi(i)=i-1$

枚举倍数的时候，$\phi(i*p[j])=\left\{\begin{matrix}
\phi(i)*(p[j]-1) & imodp[j]\neq0 \\ 
\phi(i)*p[j] & imodp[j]=0
\end{matrix}\right.$

模板

```c++
void F(ll n)
{
	phi[1]=1;
	for(int i=2;i<=n;++i)
	{
		if(!vis[i]) p[++cnt]=i,phi[i]=i-1;
		for(int j=1;(j<=cnt)&&(i*p[j]<=n);++j)
		{
			vis[i*p[j]]=1;
			if(i%p[j]==0)
			{
				phi[i*p[j]]=phi[i]*p[j];
				break;
			}
			phi[i*p[j]]=phi[i]*(p[j]-1);
		}
}
```

求莫比乌斯函数

```c++
void init(ll n)
{
    f[1]=1;
    for(int i=2;i<=n;++i)
    {
        if(!vis[i])
        {
            p[++tot]=i;
            f[i]=-1;
        }
        for(int j=1;j<=tot&&i*p[j]<=n;++j)
        {
            vis[i*p[j]]=1;
            if(i%p[j]==0) break;
            f[i*p[j]]=-f[i];
        }
    }
}
```



### $O(\sqrt(n))$求单个数字的欧拉函数

$首先将n进行质因数分解，n= \prod_{i=1}^{k} p_i^{\alpha_i}$

考虑欧拉函数的意义：1-n中与n互质的数字的个数

首先减去所有x满足$p1|(x,n)$,这样的x有$n/p_1$个，$对任意p_i$同理

注意到$满足p_1p_2|(x,n)的数字个数被减了两次，所以要加回去一次，也就是 \forall i,j,i\neq j,加上n/(p_ip_j)$

最后，$\phi(n)=n-\sum n/p_i+\sum n/(p_ip_j)-\sum n/(p_ip_jp_k)+...+(-1)^k \sum n/(p_ip_j...p_k)$

$=n(1-\frac{1}{p_1})(1-\frac{1}{p_2})...(1-\frac{1}{p_k})$

$=\prod_{i=1}^{k} p_i^{\alpha_i}*(1-\frac{1}{p_1})(1-\frac{1}{p_2})...(1-\frac{1}{p_k})$

$=\prod_{i=1}^{k}(p_i-1)p_i^{\alpha_i-1}$

即**$\phi(n)=\prod_{i=1}^{k}(p_i-1)p_i^{\alpha_i-1}$**

> 换一种理解：$\phi(n)=\prod\phi(p_i^{\alpha_i})$
>
> 对于单个的$\phi(p^{\alpha})$,实际含义是$x\in [1,p^{\alpha}]$中满足$(x,p^{\alpha})=1$的x的个数
>
> 若$x\equiv 1(\mod p^{\alpha})$,又$p|p^{\alpha}$,则$x\equiv 1(\mod p)$
>
> 所以实际含义也等于$x\in [1,p^{\alpha}]$中满足$(x,p^{\alpha})=1$的x的个数
>
> 那么就是$p^{\alpha}-p$
>
> 所以$\phi(n)=\prod\phi(p_i^{\alpha_i})=\prod (p_i-1)p_i^{\alpha_i-1}$



### 一些套路推导

* $\sum_{i=1}^{n}\frac{lcm(i,n)}{n}$

  $=\sum_{d|n}\sum_{i=1}^{n}\frac{i}{d}[(i,n)=d]$

  $=\sum_{d|n}\sum_{i=1}^{n}i[(\frac{i}{d},\frac{n}{d})=d]$

  $=\sum_{d|n}\sum_{i=1}^{\frac{n}{d}}i[(i,\frac{n}{d})=1]$

  $=\sum_{d|n}\sum_{i=1}^{d}i[(i,d)=1]$ 因为这里枚举d与枚举n/d等价

  $=\sum_{d|n}\frac{d\phi(d)}{2}$

  关于最后一步的证明：$\sum_{i=1}^{d}i[(i,d)=1]=\frac{1}{2}(\sum_{i=1}^{d}i[(i,d)=1]+\sum_{i=1}^{d}(d-i)[(d-i,d)=1])$ 正反枚举一遍

  又因为$(i,d)=(d-i,d)$，故$\sum_{i=1}^{d}i[(i,d)=1]=\frac{1}{2}\sum_{i=1}^{d}d[(i,d)=1]=\frac{d}{2}\phi(d)$

* 欧拉反演

  $id=\phi*1$其实也就是前面提到的，它确实很有用

* $\sum_{i=1}^{n}\sum_{j=1}^{n}f_{(a_i,a_j)}$ 其中$f_x$表示某个函数

  $=\sum_{d=1}^{n}f_d\sum_{i=1}^{n}\sum_{j=1}^{n}[(a_i,a_j)=d]$

  $=\sum_{d=1}^{n}f_d\sum_{i=1}^{n}\sum_{j=1}^{n}[(\frac{a_i}{d},\frac{a_j}{d})=1][d|a_i][d|a_j]$

  $=\sum_{d=1}^{n}f_d\sum_{i=1}^{n}\sum_{j=1}^{n}[d|a_i][d|a_j]\sum_{t|(\frac{a_i}{d},\frac{a_j}{d})}\mu(t)$

  $=\sum_{d=1}^{n}f_d\sum_{t=1}^{\frac{n}{d}}\mu(t)\sum_{i=1}^{n}\sum_{j=1}^{n}[dt|a_i][dt|a_j]$

  $=\sum_{d=1}^{n}f_d\sum_{t=1}^{\frac{n}{d}}\mu(t)(\sum_{i=1}^{n}[dt|a_i])^2$

  最后的一个$\sum$一般是可以$O(nlogn)$预处理的，如果f也可以预处理的话，复杂度是$O(nlogn)$

* **关于gcd**

  ![image-20231127150516761](C:\Users\26463\Desktop\Major\typo_r_a\Pic\DujiaoShai\P10.png)

### 例题

[$cf757 Bash Plays with Functions$](https://codeforces.com/contest/757/problem/E )

给定函数f，$f_r(n)=\left\{\begin{matrix}
\sum_{uv=n}[(u,v)=1] & r=0 \\ 
\sum_{uv=n}\frac{f_{r-1}(u)+f_{r-1}(v)}{2} & r\geq1 
\end{matrix}\right.$

对于给定的r,n,求$f_r(n)$

不妨先来分析一下$f_0(n):$

对于$n=p_1^{\alpha_1}p_2^{\alpha_2}...p_k^{\alpha_k}$

考虑$u=p_1^{\beta_1}p_2^{\beta_2}...p_k^{\beta_k},v=p_1^{\alpha_1-\beta_1}p_2^{\alpha_2-\beta_2}...p_k^{\alpha_k-\beta_k}$,此时有$uv=n$

为了保证$(u,v)=1$，我们不难发现$\beta_i$只能等于$\begin{Bmatrix}
0, & \alpha_i
\end{Bmatrix}$,

所以$f_0(n)=2^k$,不要忘记这是一个积性函数

$r\geq 1$时，不难发现$f_r(n)=\sum_{u|n}f_{r-1}(n)$,所以$f_r(n)=f_{r-1}(n)*1$,这里$*$代表迪利克雷卷积，所以$f_r(n)$也是一个积性函数。

那么问题就简单很多了，对于$f_r(n)$,我们只需要对求出n的每一个质因子的$f_r(p_i)$,然后乘起来即可。对于质数p,$f_0(p)=2,f_1(p)=\sum_{u|p}f_0(p)$,我们发现$f_0(p)$已经是一个与p无关的数字了，所以$\forall p_i,p_j,f_r(p_i)=f_r(p_j)$恒成立。故考虑$dp_{i,j}$表示$f_i(p^j),dp_{i,j}=\sum_{k=1}^{j}dp_{i-1,k}$，可以前缀和优化。同时j不超过20，所以时间复杂度也可以接受

code

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define endl '\n'
const int N=1e6+10;
const ll mod=1e9+7;
int n,r;
ll dp[N][30];
int LP[N];
void init()
{
    for(int i=1;i<=25;++i) dp[0][i]=2;
        dp[0][0]=1;
    for(int i=1;i<=1000000;++i)
    {
        dp[i][0]=1;
        ll sum=1;
        for(int j=1;j<=20;++j)
        {
            dp[i][j]=(dp[i-1][j]+sum)%mod;
            sum=(sum+dp[i-1][j])%mod;

        }
    }
}
void iinit()
{
    LP[1]=1;
    for(int i=2;i<=1000000;++i)
    {
        if(!LP[i])
        {
            for(int j=i;j<=1000000;j+=i) LP[j]=i;
        }
    }
}
void solve()
{
    cin>>r>>n;
    ll ans=1;

    // for(ll i=2;i*i<=n;++i)
    // {
    //     if(n%i) continue;
    //     ll cnt=0;
    //     while((n%i)==0)
    //     {
    //         cnt++;
    //         n/=i;
    //     }
    //     ans=ans*dp[r][cnt]%mod;
    // }

    while(n!=1)
    {
       int cnt=0,p=LP[n];
       while(n%p==0) n/=p,cnt++;
       ans=(ans*dp[r][cnt])%mod;
    }

    cout<<ans<<endl;
}
int main()
{
    ios::sync_with_stdio(0);cin.tie(0);cin.tie(0);
    init();iinit();
    ll t;cin>>t;while(t--)
    solve();
    return 0;
}

```

## 积性函数的反演

相比于上一块，这一块在竞赛中的应用会更加多一些。

重新讨论一下一开始有提到的迪利克雷卷积

所谓**狄利克雷卷积**，是定义在**数论函数**（ Z+→C 的函数）间的一种二元运算，可这样定义:$h(n)=\sum_{d|n}f(d)g(n/d)$,h称为f与g的迪利克雷卷积。后面我们统一用$*$来表示该卷积,即$(f*g)(n)=\sum_{d|n}f(d)g(n/d)$

**性质**：两个积性函数的迪利克雷卷积也是积性函数

**一个很重要的结论**：若$f=g*1$，则$g=f*\mu$.，相当于$\mu$是1的逆元

### 一些常见的$Dirichlet$卷积：

* $\epsilon=\mu *1$:

  展开：$[n=1]=\sum_{d|n}\mu(d)$

  $Proof: 1=\sum[n=1]\Rightarrow 1(n)=\sum_{d|n}\epsilon(d) \Rightarrow 1(n)=1(n)*\epsilon(n) \Rightarrow \epsilon=\mu*1$

  ​                这里带入了上面讲的结论

* $id=\phi*1$ 证明略

* $\phi=\mu*id$(其实就是上式的反演)

这些反演一般用在杜教筛里比较多，可以实现积性函数求前缀和。但是莫比乌斯函数与欧拉函数的反演在一般的计数问题中也比较常见

### 莫比乌斯反演

形式1：

$f(n)=\sum_{d|n}g(d) \Leftrightarrow g(n)=\sum_{d|n}\mu(\frac{n}{d})f(d)$

形式2：

$f(n)=\sum_{n|m,m<=N}g(m) \Leftrightarrow g(n)=\sum_{n|m,m<=N}\mu(\frac{m}{n})f(m)$

## 杜教筛

其解决的问题：对数论函数f求前n项的前缀和$S(n)$，n可以达到$10^{10}$。其核心思想是构造另一个数论函数g，**使得$h=f*g$(狄利克雷卷积)能够$O(1)$地求前缀和**，并且**g本身也可以$O(1$地求某一点的值**。

但是它并不要求f一定要是一个积性函数，只需要能满足上述两个条件即可

在此基础上，我们可以在$\large O(n^{\frac{2}{3}})$的时间复杂度内得到前缀和，当然还需要预处理前$\large n^{\frac{2}{3}}$项的前缀和

一般来说，并杜教筛的实现只需要预处理前缀和，再加上整除分块即可，难点在于g函数的构造

下面进行推导

因为 $f∗g$的前缀和很好求，考虑用它去推得 $S(n)$

$\large ∑_{i=1}^{n}(f∗g)(i)=∑_{i=1}^{n}∑_{d|i}f(d)g(\frac{i}{d})$

$\large =∑_{d=1}^{n}g(d) \sum_{i=1}^{⌊\frac{n}{d}⌋}f(i)=\sum_{d=1}^{n}g(d)S(⌊\frac{n}{d}⌋)$

$\large ∴∑_{i=1}^{n}(f∗g)(i)=\sum_{d=1}^{n}g(d)S(⌊\frac{n}{d}⌋)$

$\large g(1)S(n)= \sum_{i=1}^{n}(f∗g)(i)-\sum_{d=2}^{n}g(d)S(⌊\frac{n}{d}⌋)$

$\large S(n)=$$\Large \frac {\sum_{i=1}^{n}(f∗g)(i)-\sum_{d=2}^{n}g(d)S(⌊\frac{n}{d}⌋)}{g(1)}$

这样就得到了$S(n)$的递推式

### [常见数论函数的前缀和推导](https://www.cnblogs.com/Rubyonly233/p/14925239.html#常见数论函数的前缀和推导)

- $f(n)=μ(n),S(n)=∑_{i=1}^{n}μ(i)$

构造函数$g(n)=1(n)$，容易得到 $f∗g=ϵ$套用公式

$S(n)=\Large \frac{∑_{i=1}^{n}(f∗g)(i)−∑_{d=2}^{n}g(d)S(⌊\frac{n}{d}⌋)}{g(1)}$

$\Large =\frac{∑_{i=1}^{n}ϵ(i)−∑_{d=2}^{n}g(d)S(⌊\frac{n}{d}⌋)}{g(1)}$

$\Large =\frac{1−∑_{d=2}^{n}g(d)S(⌊\frac{n}{d}⌋)}{g(1)}$

$\Large =\frac{1−∑_{d=2}^{n}g(d)S(⌊\frac{n}{d}⌋)}{1}=$$\large 1−∑_{d=2}^{n}g(d)S(⌊\frac{n}{d}⌋)$

因为 $g(n)=1(n)$，所以 $g(n)$ 的前缀和可以直接 $O(1)$ 求，至此所有条件就够了

------

- $f(n)=φ(n),S(n)=∑_{i=1}^{n}φ(i)$

构造函数 g(n)=1(n)，容易得到 $f∗g=id_1$,套用公式

<img src="C:/Users/26463/Desktop/Major/typo_r_a/Pic/DujiaoShai/P1.png" alt="image-20230819162004418" style="zoom:70%;" />

同样可以套用模板去做

------

- $f(n)=μ(n)n,S(n)=∑_{i=1}^{n}μ(i)i$

构造函数 $g(n)=id_1(n)$

<img src="C:/Users/26463/Desktop/Major/typo_r_a/Pic/DujiaoShai/P2.png" alt="image-20230819162034122" style="zoom:70%;" />

接着套用公式

<img src="C:/Users/26463/Desktop/Major/typo_r_a/Pic/DujiaoShai/P3.png" alt="image-20230819162047244" style="zoom:70%;" />

因为 $g(n)=id_1(n)$，所以可以 $O(1)$ 计算前缀和，同样可以套用模板去做

------

- $f(n)=φ(n)n,S(n)=∑_{i=1}^{n}φ(i)i$

构造函数 $g(n)=id_1(n)$

<img src="C:/Users/26463/Desktop/Major/typo_r_a/Pic/DujiaoShai/p4.png" alt="image-20230819162102054" style="zoom:70%;" />

接着套用公式

<img src="C:/Users/26463/Desktop/Major/typo_r_a/Pic/DujiaoShai/p5.png" alt="image-20230819162114520" style="zoom:70%;" />

然后套用模板去做就行了

------

- $f(n)=μ(n)n^2,S(n)=∑_{i=1}^{n}μ(i)i^2$

构造函数 $g(n)=id_2(n)$

<img src="C:/Users/26463/Desktop/Major/typo_r_a/Pic/DujiaoShai/p6.png" alt="image-20230819162114520" style="zoom:70%;" />

接着套用公式

<img src="C:/Users/26463/Desktop/Major/typo_r_a/Pic/DujiaoShai/p7.png" alt="image-20230819162114520" style="zoom:70%;" />

因为 $g(n)=id_2(n)$，所以前缀和直接就是 $n(n+1)(2n+1)/6$，同样可以套用模板去做

------

- $f(n)=φ(n)n^2,S(n)=∑_{i=1}^{n}φ(i)i^2$

构造 $g(n)=id_2(n)$

<img src="C:/Users/26463/Desktop/Major/typo_r_a/Pic/DujiaoShai/p8.png" alt="image-20230819162114520" style="zoom:70%;" />

接着套用公式

<img src="C:/Users/26463/Desktop/Major/typo_r_a/Pic/DujiaoShai/p8.png" alt="image-20230819162114520" style="zoom:70%;" />

$∑g(d)$的求法跟上面一样，接着还是套用模板去做就行了



### [例子](http://acm.hdu.edu.cn/showproblem.php?pid=7325)

大意：给定$\large f(x)=(2^x-1)^k$,求$\large g(x)=\sum_{T=1}^{n}(\frac{n}{T})^2\sum_{d|T}f(d)\mu(\frac{T}{d})$,$n \leq 1e9$

考虑令$\large g(T)=\sum_{d|T}f(d)\mu(\frac{T}{d})$,$\large g$是$f$和$\mu$的狄利克雷卷积。如果可以快速求g的前缀和，我们就可以利用整除分块来解决这个式子。所以这是一道杜教筛套整除分块的经典例子

注意到f其实并不是一个积性函数，所以g也可能不是一个积性函数，但是并不影响我们构造对应的辅助函数

发现$g=f*\mu$,所以自然而然地考虑到$f=g*1$，f可以快速求前缀和，而$1(n)$函数也是无比友好，所以我们就可以利用它们进行构造了。

$\large S(n)=\sum_{i=1}^{n}g(i)=$$\Large \frac{\sum_{i=1}^{n}g*1-\sum_{i=2}^{n}1(i)S(n/i)}{1(1)}$

$\large =\sum_{i=1}^{n}f(i)-\sum_{i=2}^{n}S(n/i)$

最后考虑求f的前缀和

$\large \sum_{i=1}^{n}(2^i-1)^k=\sum_{i=1}^{n}\sum_{k=1}^{K}C(K,k)2^{ik}(-1)^{K-k}=\sum_{k=0}^{K}(-1)^{K-k}\sum_{i=1}^{n}2^{ik}$

后面一撮只要等比数列求和即可。特判一下k=0的情况，此时等比数列求和为n

最后我们的总体复杂度就是$O(n^{\frac{2}{3}}K)$

$ps:$感觉辅助函数的构造一般也不会太逆天，总归是有迹可循的，就像这里，把g与f反演就是一个及其自然的过程，包括上面的一些推导，也都不是空穴来风。最重要的还是积累，以及灵光一现。





















