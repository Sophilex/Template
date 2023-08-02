[TOC]



## Dsu on tree

又叫dsu on tree，一般用来解决下面这类问题

1.只有对子树的查询

2.没有修改操作

其实就有点像[并查集](https://so.csdn.net/so/search?q=并查集&spm=1001.2101.3001.7020)里面的启发式合并，只不过是在树上做信息合并罢了。

```
struct ty
{
    ll t,next;
}edge[N<<1];
ll head[N];
ll cn=0;
void add_edge(ll a,ll b)
{
    edge[++cn].t=b;
    edge[cn].next=head[a];
    head[a]=cn;
}
ll n,m;
ll Flag;
ll siz[N],son[N],col[N];
pair<ll,ll> q[N];
ll num[N],dk[N];
map<ll,ll> ans[N];
vector<ll> vt[N];
void init_dfs(ll id,ll fa)
{
    siz[id]=1;
    ll sn=0;
    for(int i=head[id];i!=-1;i=edge[i].next)
    {
        ll y=edge[i].t;
        if(y==fa) continue;
        init_dfs(y,id);
        siz[id]+=siz[y];
        if(siz[y]>sn)
        {
            sn=siz[y];
            son[id]=y;
        }
    }
}
void add(ll id,ll fa,ll val)
{
    if(val==-1) dk[num[col[id]]]--;
    num[col[id]]+=val;
    if(val==1) dk[num[col[id]]]++;
    for(int i=head[id];i!=-1;i=edge[i].next)
    {
        ll y=edge[i].t;
        if(y==fa||y==Flag) continue;
        add(y,id,val);
    }
}
void dfs2(ll id,ll fa,ll op)
{
    for(int i=head[id];i!=-1;i=edge[i].next)
    {
        ll y=edge[i].t;
        if(y==fa||y==son[id]) continue;
        dfs2(y,id,0);
    }
    if(son[id]) dfs2(son[id],id,1),Flag=son[id];
    add(id,fa,1);
    Flag=0;
    for(auto s:vt[id])
    {
        ans[id][s]=dk[s];
    }
    if(op==0)
    {
        add(id,fa,-1);
    }
}
void solve()
{
    memset(head,-1,sizeof(head));
    cin>>n>>m;
    for(int i=1;i<=n;++i) cin>>col[i];
    for(int i=1;i<n;++i)
    {
        ll a,b;
        cin>>a>>b;
        add_edge(a,b);
        add_edge(b,a);
    }
    for(int i=1;i<=m;++i)
    {
        ll a,b;
        cin>>a>>b;
        vt[a].push_back(b);
        q[i]=make_pair(a,b);
    }
    init_dfs(1,0);
    dfs2(1,0,0);
    for(int i=1;i<=m;++i)
    {
        ll a=q[i].first;
        ll b=q[i].second;
        cout<<ans[a][b]<<endl;
    }
}
```

## 点分治

```
ll n,m;
ll a,b,c;
ll Q[110],siz[N],dis[N],mx[N],rt,nt;
//Q 询问 siz子树大小 dis到根的距离 mx最大子树对应节点 
ll cnt,d[N];//当前存在的路径长度 
bool vis[N],dis_vis[10000005];
//节点是否已经分治过，在某次分治中距离为dis_vis[i]的节点是否存在 
ll ans[110];
void find_rt(ll id,ll fa)
{
	siz[id]=1;mx[id]=0;
	for(int i=head[id];i!=-1;i=edge[i].next)
	{
		ll y=edge[i].t;
		if(y==fa||vis[y]) continue;
		find_rt(y,id);
		mx[id]=max(mx[id],siz[y]);
		siz[id]+=siz[y];
	} 
	mx[id]=max(mx[id],nt-siz[id]);//这里是nt-siz[id],因为重心要在不同子树里面求 
	if(mx[id]<mx[rt])
	{
		rt=id;
	}
}
void get_dis(ll id,ll fa)
{
	d[++cnt]=dis[id];
	for(int i=head[id];i!=-1;i=edge[i].next)
	{
		ll y=edge[i].t;
		if(y==fa||vis[y]) continue;
		dis[y]=edge[i].l+dis[id];
		get_dis(y,id);
	}
}
 
void calc(ll id)//统计过根的路径 
{
	dis_vis[0]=1;//rt到自己的距离为0
	vector<ll> vt;
	for(int i=head[id];i!=-1;i=edge[i].next)
	{
		ll y=edge[i].t;
		if(vis[y]) continue;
		cnt=0;//清空d数组 
		dis[y]=edge[i].l;
		get_dis(y,id);
		for(int j=1;j<=cnt;++j)
		{
			for(int k=1;k<=m;++k)
			{
				if(Q[k]>=d[j])
				{
					ans[k]|=dis_vis[Q[k]-d[j]];
				}
			}
		}
		for(int j=1;j<=cnt;++j) if(d[j]<=1e7) dis_vis[d[j]]=1,vt.push_back(d[j]);
	}
	for(auto j:vt) dis_vis[j]=0;//路径初始化 
}
void dfz_(ll id)//点分治 
{
	calc(id);
	vis[id]=1;
	for(int i=head[id];i!=-1;i=edge[i].next)
	{
		ll y=edge[i].t;
		if(vis[y]) continue;
		//递归 
		rt=0;mx[rt]=nt=siz[y];
		find_rt(y,0);find_rt(rt,0);//更新siz
		dfz_(rt); 
	}
}
void solve()
{
	memset(head,-1,sizeof head);
	read(n);read(m);
	for(int i=1;i<n;++i)
	{
		read(a);read(b);read(c);
		add(a,b,c);
		add(b,a,c);
	}
	for(int i=1;i<=m;++i) read(Q[i]);
	mx[0]=nt=n;
	find_rt(1,0);
	find_rt(rt,0);//更新siz数组，因为现在是以一个新的点为根节点
	dfz_(rt); 
	for(int i=1;i<=m;++i)
	{
		cout<<(ans[i]?"AYE":"NAY")<<endl;
	}
}
```

## 线性基

---

1.数组中的任意一个数可以**唯一地**由线性基中的若干元素异或得到

2.线性基中任意多个元素的异或值不为0

3.线性基元素的异或集合等于原数组元素的异或集合

---

### 求数组任意异或的最大值

```
ll n,k;
ll mas[N];
ll d[70];
void add(ll x)
{
	for(int i=60;i>=0;--i)
	{
		if(x&(1ll<<i))
		{
			if(d[i]) x^=d[i]; 
			else
			{
				d[i]=x;
				break;
			}
		}
		
	}
}
void solve()
{
	cin>>n;
	for(int i=1;i<=n;++i) cin>>mas[i];
	for(int i=1;i<=n;++i) add(mas[i]);
	ll ans=0;
	for(int i=60;i>=0;--i)
	{
		ans=max(ans,ans^d[i]);
	}
	cout<<ans<<endl;
}
```

### 求数组的异或结果的种类数

显然线性基中不同元素的异或结果一定不同，所以答案就是2^(线性基大小)

```
ll n,m;
string s;
ll d[70];
vector<ll> vt;
void add(ll x)
{
	for(int i=60;i>=0;--i)
	{
		if(x&(1ll<<i))
		{
			if(d[i]) x^=d[i];
			else
			{
				d[i]=x;
				break;
			}
		}
	}
}
void solve()
{
	cin>>n>>m;
	for(int i=1;i<=m;++i)
	{
		cin>>s;
		ll cnt=0;
		for(int j=0;j<n;++j)
		{
			cnt*=2ll;
			if(s[j]=='O') cnt++;
		}
		//cout<<cnt<<endl;
		vt.push_back(cnt);
	}
	for(auto i:vt) add(i);
	ll num=0;
	for(int i=0;i<=60;++i) if(d[i]>0) num++;
	ll ans=1;
	for(int i=1;i<=num;++i)
	{
		ans=ans*2%2008;
	}
	cout<<ans<<endl;
}
```

## 高维前缀和

### 二进制子集前缀和

$\forall i,0\leq i\leq 2^n-1，,求\large \sum_{j\subset i} a_{j}$

```
for(int j=0;j<n;++j)
{
    for(int i=0;i<(1<<n);++i)
    {
        if(i&(1<<j))
        {
            dp[i]+=dp[i^(1<<j)]
        }
    }
}
//其中 j属于i 定义为j的二进制表示是i的二进制表示的子集
```

因为是正序枚举的，所以i^(1<<j)是是当前这一维度，而此时i还在上一维，所以这样就实现了高维的前缀和。这样的时间复杂度是,而如果去枚举子集来做前缀和的话，时间复杂度是n^3的。

我们来看一些具体的应用

#### ARC 100 E - Or Plus Max

大意：
给定一个长度为2^n的数组，对于每一个k，1<=k<=2^n-1，求出最大的ai+aj，其中iorj<=k

思路：
关键就是如何处理i or j<=k,不难发现这蕴含的意思其实就是iorj的结果是k的二进制表示下的子集

所以我们直接跑高维前缀和，维护一下每一个k对应的能够用的最大值以及次大值即可

code

```
ll n;
ll mas[N],sec[N],dp[N];
void upt(ll id,ll val)
{
	if(val>=mas[id])
	{
		sec[id]=mas[id];
		mas[id]=val;
	}
	else if(val>=sec[id])
	{
		sec[id]=val;
	}
}
void solve()
{
	cin>>n;
	for(int i=0;i<(1<<n);++i) cin>>mas[i];
	for(int j=0;j<n;++j)
	{
		for(int i=0;i<(1<<n);++i)
		{
			if(i&(1<<j))
			{
				upt(i,mas[i^(1<<j)]);
				upt(i,sec[i^(1<<j)]);
			}
		}
	}
	for(int i=1;i<(1<<n);++i)
	{
		dp[i]=max(dp[i-1],mas[i]+sec[i]);
		cout<<dp[i]<<endl;
	}
}
```

#### 再看一道

给定一个数组，问里面有多少个数字满足ai&aj=0

思路：

显然对于一个数ai，我们找到它的二进制表示的补集Q，那么Q的所有子集出现过的次数就是ai的贡献，所以我们还是可以通过高维前缀和来处理

代码不写了，题源也找不到，自己意会一下

那么上面都是二进制枚举子集的前缀和，如果是枚举超集的话，那其实叫后缀和会更加合理一些吧

### 二进制超集后缀和
代码也很好写，无非就是把原本为0的地方改成1

```
for(int j=0;j<n;++j)
{
    for(int i=0;i<(1<<n);++i)
    {
        if((i&(1<<j))==0)
        {
            dp[i]+=dp[i^(1<<j)]
        }
    }
}
```

---

然后我们看看一开始提出的第二种定义

$\forall i,0\leq i\leq 2^n-1$,求$\large \sum_{j\subset i} a_{j}$,其中 j属于i 定义为j | i

显然j|i蕴含的意思是，对于每一个质因子p，j蕴含的p的幂次不大于i蕴含的p的幂次，所以这里还是一个子集的关系，只不过集合的定义由二进制表示变成了素数分解

我们换一种写法

$\forall i,1\leq i\leq n$,求$\sum_{j|i}aj$

我们只要按照埃氏筛的样子做一遍更新就可以了

```
for(int i=1;i<=n;++i)
{
    if(!vis[i])
    {
        for(int j=1;j*i<=n;++j) sum[i*j]+=sum[j],vis[i*j]=1;
    }
}
```

如果要预处理的话也可以，唯一需要注意的，跟上文一样，这里我们的维度是由不同的素因子来确定的，所以我们要先枚举素因子来保证每一维的前缀和都更新全了

```
for(int i=1;i<=totprime;++i)
{
    for(int j=1;p[i]*j<=n;++j)
    {
        sum[p[i]*j]+=sum[j];
    }
}
```

那么这种东西其实有一种更加正式的名字：

### 狄利克雷前缀和

code

```
#include<bits/stdc++.h>
using namespace std;
#define ll unsigned int
#define endl '\n'
const ll N=2e7+10;
ll n,a;
ll seed;
inline ll getnext(){
	seed^=seed<<13;
	seed^=seed>>17;
	seed^=seed<<5;
	return seed;
}

ll b[N];
bool vis[N];
ll ans=0;
void solve()
{
	cin>>n>>seed;
	for(int i=1;i<=n;++i) b[i]=getnext();
	vis[1]=1;
	for(int i=1;i<=n;++i)
	{
		if(!vis[i])
		{
			for(int j=1;j*i<=n;++j) b[i*j]+=b[j],vis[i*j]=1;
		}
	}
	for(int i=1;i<=n;++i) ans^=b[i];
	cout<<ans<<endl;
}
int main()
{
	ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);
	solve();
	return 0;
}
```

事实上还有一种东西叫做

### 狄利克雷后缀和
$\forall i,1\leq i \leq n$，求$\sum_{i|j}a_j$

其实也不难理解，就是素因子分解下的枚举超集的后缀和，跟上面讲的二进制超集后缀和一个意思

```
for(int i=1;i<=totprime;++i)
{
    for(int j=n/p[i];j;--j)
    {
        sum[j]+=sum[j*p[j]];
    }
}
```

但是因为这里我们需要用到比自己大的值，所以内层需要倒序更新

#### cf 757 D2. Divan and Kostomuksha (hard version)

大意：
给定一个数组a，要求重排数组，使得数组的前缀gcd之和最大

思路：
一个显然的小贪心：如果一开始的gcd是x的话，我们一定要尽可能多的保留gcd为x，因为后面的gcd不会大于x。要做到这一点，我们需要统计有多少个数字是x的倍数

换句话说，我们需要统计cnt【i】，表示有多少个数字是i的倍数，这其实就是一个狄利克雷后缀和

这里还有一个性质：cnt【j】一定不大于cnt【i】（j|i），这一点显然

那么我们如果以i作为一开始的gcd的话，会保留cnt【i】个前缀gcd为i的长度，如果以j作为一开始的gcd的话，会保留cnt【j】个前缀gcd为j的长度，这个长度肯定是不大于上一个的，所以我们可以从i转移到j，

设dp【i】表示一开始的gcd为i的情况下数组的最大价值

dp[j]=max(dp[j],1ll*cnt[j]*(j-i)+dp[i]);

一开始的初始条件是dp[1]=1

另外这题数据范围有点大，需要预处理一下素数，然后按素数转移即可

code

```
ll n,a;
ll cn=0;
ll cnt[N+10];
long long dp[N+10];
ll p[N+10];
bool vis[N+10];
void init()
{
    for(int i=2;i<=N;++i)
    {
        if(!vis[i])
        {
            p[++cn]=i;
        }
        for(int j=1;j<=cn&&i*p[j]<=N;++j)
        {
            vis[i*p[j]]=1;
            if(i%p[j]==0) break;
        }
    }
}
void solve()
{
    init();
    cin>>n;
    //cout<<cn<<endl; 
    for(int i=1;i<=n;++i)
    {
        cin>>a;
        cnt[a]++;
    }
    for(int i=1;i<=cn;++i)
    {
        for(int j=N/p[i];j;--j)
        {
            cnt[j]+=cnt[j*p[i]];    
        } 
    }
    dp[1]=n;
    long long ans=0;
    for(int i=1;i<=N;++i)
    {
        for(int j=1;i*p[j]<=N;++j)
        {
            ll sd=i*p[j];
            dp[sd]=max(dp[sd],1ll*cnt[sd]*(sd-i)+dp[i]);
        }
        ans=max(ans,dp[i]);
    }
    cout<<ans<<endl;
}
```

## 可持久化Trie

```
const ll N=6e5+10;
ll idx=0;
ll tr[N*25][3];
ll root[N];
ll last[N*25];
ll sum[N];
ll n,m;
char op;
ll l,r,val;
ll a,b,c;
void insert(ll pre,ll now,ll i,ll k)
{
	if(k<0)
	{
		last[now]=i;
		return;
	}
	bool id=(sum[i]>>k)&1;
	if(pre) tr[now][id^1]=tr[pre][id^1];
	tr[now][id]=++idx;
	insert(tr[pre][id],tr[now][id],i,k-1);
	last[now]=max(last[tr[now][0]],last[tr[now][1]]);
}
ll find(ll l,ll now,ll val)
{
    ll p=now;
    for(int i=23;i>=0;--i)
    {
    	bool id=(val>>i)&1;
    	if(last[tr[p][id^1]]>=l) p=tr[p][id^1];
    	else p=tr[p][id];
	}
	return sum[last[p]]^val;
} 
void solve()
{
	cin>>n>>m;
	root[0]=++idx;
	last[0]=-1;
	insert(0,root[0],0,23);
	for(int i=1;i<=n;++i)
	{
		cin>>a;
		sum[i]=sum[i-1]^a;
		root[i]=++idx;
		insert(root[i-1],root[i],i,23);
	}
	while(m--)
	{
		cin>>op;
		if(op=='A')
		{
			cin>>a;
			n++;//n++操作要在语句外执行，不能写成sum[++n]=sum[n-1]^a，会wa，我也不懂。。。 
			sum[n]=sum[n-1]^a;
			root[n]=++idx;
			insert(root[n-1],root[n],n,23);
			continue;
		}
		cin>>a>>b>>c;
		cout<<find(a-1,root[b-1],sum[n]^c)<<endl;
	}
}
```

## 模拟退火

```
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define endl '\n'
const ll N=1010;
const double down=0.996;
struct ty
{
	double x,y;
	double w;
}mas[N];
ll n;
double ansx,ansy,answ;
double sumx,sumy;
double pf(double x)
{
	return x*x;
}
double energy(double x,double y)
{
	double sum=0;
	for(int i=1;i<=n;++i)
	{
		double dx=x-mas[i].x;
		double dy=y-mas[i].y;
		sum+=mas[i].w*sqrt(pf(dx)+pf(dy));
	}
	return sum;
}
void sa()
{
	double t=3000;
	while(t>1e-15)
	{
		double ex=ansx+(rand()*2-RAND_MAX)*t;
		double ey=ansy+(rand()*2-RAND_MAX)*t;
		double ew=energy(ex,ey);
		double det=ew-answ;
		if(det<0)
		{
			ansx=ex;
			ansy=ey;
			answ=ew;
		}
		else if(exp(-det/t)*RAND_MAX>rand())
		{
			ansx=ex;
			ansy=ey;
		}
		t*=down;
	}
}
void gt()
{
	sa();sa();sa();sa();
}
void solve()
{
	//srand(time(0));
	cin>>n;
	for(int i=1;i<=n;++i)
	{
		cin>>mas[i].x>>mas[i].y>>mas[i].w;
		sumx+=mas[i].x;sumy+=mas[i].y;
	}
	ansx=sumx/n;ansy=sumy/n;
	answ=energy(ansx,ansy);
	gt();
	cout<<fixed<<setprecision(3)<<ansx<<" "<<ansy<<endl;
}
```

分治FFT

```
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define IL inline
#define ull unsigned long long
#define clr(f,n) memset(f,0,sizeof(int)*(n))
#define cpy(f,g,n) memcpy(f,g,sizeof(int)*(n))
const int _G=3,mod=998244353,Maxn=1e5+10;
namespace FastIOT{
	const int bsz=1<<18;
	char bf[bsz],*hed,*tail;
	inline char gc(){if(hed==tail)tail=(hed=bf)+fread(bf,1,bsz,stdin);if(hed==tail)return 0;return *hed++;}
	template<typename T>IL void read(T &x){T f=1;x=0;char c=gc();for(;c>'9'||c<'0';c=gc())if(c=='-')f=-1;
	for(;c<='9'&&c>='0';c=gc())x=(x<<3)+(x<<1)+(c^48);x*=f;}
	template<typename T>IL void print(T x){if(x<0)putchar(45),x=-x;if(x>9)print(x/10);putchar(x%10+48);}
	template<typename T>IL void println(T x){print(x);putchar('\n');}
}
using namespace FastIOT;
int F[Maxn<<1],G[Maxn<<1],n,m;
ll powM(ll a,ll t=mod-2)
{
    ll ans=1;
    while(t)
    {
    	if(t&1)ans=ans*a%mod;
    	a=a*a%mod;t>>=1;
    }
	return ans;
}
const int invG=powM(_G);
int tr[Maxn<<1],tf;
void tpre(int n)
{
  	if (tf==n)return ;tf=n;
  	for(int i=0;i<n;i++)
    tr[i]=(tr[i>>1]>>1)|((i&1)?n>>1:0);
}
void NTT(int *g,bool op,int n)
{
    tpre(n);
    static ull f[Maxn<<1],w[Maxn<<1]={1};
    for (int i=0;i<n;i++)f[i]=(((ll)mod<<5)+g[tr[i]])%mod;
    for(int l=1;l<n;l<<=1)
    {
    	ull tG=powM(op?_G:invG,(mod-1)/(l+l));
    	for (int i=1;i<l;i++)w[i]=w[i-1]*tG%mod;
    	for(int k=0;k<n;k+=l+l)
        for(int p=0;p<l;p++)
	    {
	        int tt=w[p]*f[k|l|p]%mod;
	        f[k|l|p]=f[k|p]+mod-tt;
	        f[k|p]+=tt;
        }      
    	if (l==(1<<10)) for (int i=0;i<n;i++)f[i]%=mod;
    }
    if (!op)
    {
    	ull invn=powM(n);
    	for(int i=0;i<n;++i)
        g[i]=f[i]%mod*invn%mod;
    }
    else for(int i=0;i<n;++i)g[i]=f[i]%mod;
}
void px(int *f,int *g,int n)
{for(int i=0;i<n;++i)f[i]=1ll*f[i]*g[i]%mod;}
void times(int *f,int *g,int len,int lim)
{
    static int sav[Maxn<<1];
    int n=1;for(n;n<len+len;n<<=1);
    clr(sav,n);cpy(sav,g,n);
    NTT(f,1,n);NTT(sav,1,n);
    px(f,sav,n);NTT(f,0,n);
    clr(f+lim,n-lim);clr(sav,n);
}
void invp(int *f,int m)
{
  int n;for (n=1;n<m;n<<=1);
  static int w[Maxn<<1],r[Maxn<<1],sav[Maxn<<1];
  w[0]=powM(f[0]);
  for (int len=2;len<=n;len<<=1){
    for (int i=0;i<(len>>1);i++)r[i]=w[i];
    cpy(sav,f,len);NTT(sav,1,len);
    NTT(r,1,len);px(r,sav,len);
    NTT(r,0,len);clr(r,len>>1);
    cpy(sav,w,len);NTT(sav,1,len);
    NTT(r,1,len);px(r,sav,len);
    NTT(r,0,len);
    for (int i=len>>1;i<len;i++)
      w[i]=(w[i]*2ll-r[i]+mod)%mod;
  }cpy(f,w,m);clr(sav,n);clr(w,n);clr(r,n);
}
void sqrtp(int *f,int m)
{
  int n;for (n=1;n<m;n<<=1);
  static int b1[Maxn<<1],b2[Maxn<<1];
  b1[0]=1;
  for (int len=2;len<=n;len<<=1){
    for (int i=0;i<(len>>1);i++)b2[i]=(b1[i]<<1)%mod;
    invp(b2,len);
    NTT(b1,1,len);px(b1,b1,len);NTT(b1,0,len);
    for (int i=0;i<len;i++)b1[i]=(f[i]+b1[i])%mod;
    times(b1,b2,len,len);
  }cpy(f,b1,m);clr(b1,n+n);clr(b2,n+n);
}
void dao(int *f,int m){//求导 
  for (int i=1;i<m;i++)
    f[i-1]=1ll*f[i]*i%mod;
  f[m-1]=0;
}
int inv[Maxn];
void Init(int lim){
  inv[1]=1;
  for (int i=2;i<=lim;i++)
    inv[i]=1ll*inv[mod%i]*(mod-mod/i)%mod;
}
void jifen(int *f,int m){//积分 
  for (int i=m;i;i--)
    f[i]=1ll*f[i-1]*inv[i]%mod;
  f[0]=0;
}
void lnp(int *f,int m)//多项式ln 
{
  static int g[Maxn<<1];
  cpy(g,f,m);
  invp(g,m);dao(f,m);
  times(f,g,m,m);
  jifen(f,m-1);
  clr(g,m);
}
void solve()
{
	read(n); 
	for(int i=1;i<n;++i) read(G[i]);
	G[0]=1;
	for(int i=1;i<n;++i) G[i]*=-1;
	invp(G,n);
	//times(F,G,max(n,m),n+m+1);
	for(int i=0;i<n;++i) printf("%d ",G[i]);
	
}
int main()
{
	//ios::sync_with_stdio(false);cin.tie(0);cout.tie(0);
	solve();
	return 0;
}
```

## FFT

```
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define IL inline
#define ull unsigned long long
#define clr(f,n) memset(f,0,sizeof(int)*(n))
#define cpy(f,g,n) memcpy(f,g,sizeof(int)*(n))
const int _G=3,mod=998244353,Maxn=1050000;
namespace FastIOT{
	const int bsz=1<<18;
	char bf[bsz],*hed,*tail;
	inline char gc(){if(hed==tail)tail=(hed=bf)+fread(bf,1,bsz,stdin);if(hed==tail)return 0;return *hed++;}
	template<typename T>IL void read(T &x){T f=1;x=0;char c=gc();for(;c>'9'||c<'0';c=gc())if(c=='-')f=-1;
	for(;c<='9'&&c>='0';c=gc())x=(x<<3)+(x<<1)+(c^48);x*=f;}
	template<typename T>IL void print(T x){if(x<0)putchar(45),x=-x;if(x>9)print(x/10);putchar(x%10+48);}
	template<typename T>IL void println(T x){print(x);putchar('\n');}
}
using namespace FastIOT;
int F[Maxn<<1],G[Maxn<<1],n,m;
ll powM(ll a,ll t=mod-2)
{
    ll ans=1;
    while(t)
    {
    	if(t&1)ans=ans*a%mod;
    	a=a*a%mod;t>>=1;
    }
	return ans;
}
const int invG=powM(_G);
int tr[Maxn<<1],tf;
void tpre(int n)
{
  	if (tf==n)return ;tf=n;
  	for(int i=0;i<n;i++)
    tr[i]=(tr[i>>1]>>1)|((i&1)?n>>1:0);
}
void NTT(int *g,bool op,int n)
{
    tpre(n);
    static ull f[Maxn<<1],w[Maxn<<1]={1};
    for (int i=0;i<n;i++)f[i]=(((ll)mod<<5)+g[tr[i]])%mod;
    for(int l=1;l<n;l<<=1)
    {
    	ull tG=powM(op?_G:invG,(mod-1)/(l+l));
    	for (int i=1;i<l;i++)w[i]=w[i-1]*tG%mod;
    	for(int k=0;k<n;k+=l+l)
        for(int p=0;p<l;p++)
	    {
	        int tt=w[p]*f[k|l|p]%mod;
	        f[k|l|p]=f[k|p]+mod-tt;
	        f[k|p]+=tt;
        }      
    	if (l==(1<<10)) for (int i=0;i<n;i++)f[i]%=mod;
    }
    if (!op)
    {
    	ull invn=powM(n);
    	for(int i=0;i<n;++i)
        g[i]=f[i]%mod*invn%mod;
    }
    else for(int i=0;i<n;++i)g[i]=f[i]%mod;
}
void px(int *f,int *g,int n)
{for(int i=0;i<n;++i)f[i]=1ll*f[i]*g[i]%mod;}
void times(int *f,int *g,int len,int lim)
{
    static int sav[Maxn<<1];
    int n=1;for(n;n<len+len;n<<=1);
    clr(sav,n);cpy(sav,g,n);
    NTT(f,1,n);NTT(sav,1,n);
    px(f,sav,n);NTT(f,0,n);
    clr(f+lim,n-lim);clr(sav,n);
}
void solve()
{
	read(n);read(m); 
	n++;m++;
	for(int i=0;i<n;++i) read(F[i]);
	for(int i=0;i<m;++i) read(G[i]);
	times(F,G,max(n,m),n+m+1);
	for(int i=0;i<n+m-1;++i) printf("%d ",F[i]);
	
}
```

## 拓展欧几里得

```
int Exgcd(int a,int b,int &x,int &y){
    if(!b){
        x=1,y=0;
        return a;
    }int d=Exgcd(b,a%b,x,y),t=x;
    x=y;y=t-(a/b)*y;
    return d;
}
```

```
ll exgcd(ll a,ll b,ll &x,ll &y){
	if(!b){
		x=1;
		y=0;
		return a;
    }
    ll d=exgcd(b,a%b,y,x);
    y-=a/b*x;
    return d;
}
```

## 线性求逆元：

前面的求逆元的复杂度还是太高了，这里有一个线性时间复杂度的做法，可以求出1~n关于p的逆元。显然此时p与1~n的所有数都是互质关系

显然1的逆元为1.

考虑后面的数i，p可以表示为$k*i+j$ ，其中$k=\left \lfloor p/i \right \rfloor,j=pmod i$

我们不妨写成这样的形式：$k*i+j\equiv 0(modp)$

两边同时乘以$i^{-1}*j^{-1}$,得到：$k*j^{-1}+i^{-1}\equiv 0(mod p)$

化简得到：$i^{-1}\equiv -\left \lfloor p/i \right \rfloor*(pmod i)^{-1}(mod p)$ 

当然还有一个负号有点碍眼，我们可以将其处理掉：$-\left \lfloor p/i \right \rfloor\equiv (p-\left \lfloor p/i \right \rfloor)(mod p)$

显然$pmod i<i$,所以如果我们按从小到大的顺序求逆元的话，这是之前已经求好的值，所以该方法是合理的。

code：

```
inv[1]=1;
for(int i=2;i<p;++i)
{
	inv[i]=(p-p/i)*inv[p%i]%p;
}
```

---



## 推广：线性求任意n个数的逆元

上一种方法仍然有其局限性，如果求任意n个数的话，可能数字数不多，但是上限很大，这时复杂度就会很爆炸。

我们考虑n个数$a_i$ ，满足$1<=a_i<p$ 

构造$s_i=\prod_{k=1}^{i}a_i$,

我们可以在log(p)的复杂度内得到$s_n$的逆元（拓展欧几里得/快速幂都行），我们记为$inv_{s_n}$,不难发现$ (a\times b)^{-1}=a^{-1}\times b^{-1}$，所以我们将 $s_n$乘以 $a_n$的时候，$a_n$就会和其逆元抵消掉,我们就得到了$inv_{s_n-1}$ 

这样我们可以得到所有$s_n$的对应逆元，

显然有$s_{i-1}*inv_{s_i}=a_i^{-1}$

这样总体复杂度为$*O(n+log(p))$,接近线性复杂度，其能做的事情也是非常好了

---

## 中国剩余定理（CRT）

设正整数$m_1,m_2,...m_k$两两**互素**，有同余方程组

$x\equiv a_1(mod m_1)$

$x\equiv a_2(mod m_2)$

$x\equiv a_3(mod m_3)$

...

$x\equiv a_k(mod m_k)$

则对于任意的整数$a_1,a_2...a_k$，方程组有解

设$M=\prod_{i=1}^{n}m_i,并设M_i=M/m_i,设t_i为M_i模m_i在模M意义下的乘法逆元，即t_i=(M_imodm_i)^{-1}$

则方程组的通解为$x=a_1t_1M_1+a_2t_2M_2+...+a_kt_kM_k$

在模M的意义下，方程组有唯一解：$a=(\sum_{i-1}^{k}a_it_iM_i)mod M$

```
#include<bits/stdc++.h>
using namespace std;
#define ll long long
ll n;
ll a[15];
ll b[15];
void exgcd(ll a,ll b,ll &x,ll &y){
	if(!b){
		x=1;
		y=0;
		return;
	}
	exgcd(b,a%b,y,x);
	y-=a/b*x;
}
int main()
{
	cin>>n;
	ll t=1;
	for(int i=1;i<=n;++i){
		cin>>a[i];
		t*=a[i];
		cin>>b[i];
	}
	ll ans=0;
	//Mi*ti+mi*y=1; 
	for(int i=1;i<=n;++i){
		ll g=t/a[i];//g=Mi;
		ll x,y; 
		exgcd(g,a[i],x,y);
		//得到x即为所求逆元
	//	x=x%a[i];
		if(x<0) x+=a[i];
	//	cout<<ans<<" "<<a[i]<<" "<<x<<endl;
		ans+=b[i]*g*x; 
		//cout<<ans<<endl;
	}
	ans%=t;
	cout<<ans<<endl;
	return 0;
}
```

## 拓展中国剩余定理（EXCRT）

k个数$m_i$不满足两两互质时，方程组可能无解。

我们还是先考虑简单情况

$x\equiv a_1(mod m_1)$

$x\equiv a_2(mod m_2)$

它等价于：

$x=a_1+k_1*m_1$

$x=a_2+k_2*m_2$

化简得到：$k_1m_1-k_2m_2=a_2-a_1$

这是一个二元不定方程

此时如果$gcd(m_1,m_2)与(a_2-a_1)不是互质关系的话$，方程无解

否则可以求出一组解$k_1,k_2$,

然后将两个方程重新表示成x，这样就把两个方程合并了

最后这样做n-1次，就得到了一个方程，再求解即可

code

```
//excrt模板题 
#include<bits/stdc++.h>
using namespace std;
#define ll long long
ll k,a,r; 
ll exgcd(ll a,ll b,ll &x,ll &y){
	if(!b){
		x=1;
		y=0;
		return a;
    }
    ll d=exgcd(b,a%b,y,x);
    y-=a/b*x;
    return d;
}
ll ksc(ll x,ll y,ll z){
	ll ans=0;
	while(y){
		if(y&1) ans=(ans+x)%z;
		x=(x+x)%z;
		y>>=1;
	}
	return ans;
}
ll mod[100010];
ll yu[100010];
int main()
{
	cin>>k;
	
	for(int i=1;i<=k;++i){
		cin>>mod[i]>>yu[i];
	}
	ll x,y;
	ll t=mod[1],ans=yu[1];
	//t是前i个方程余数的lcm 
	for(int i=2;i<=k;++i){
		ll res=((yu[i]-ans)%mod[i]+mod[i])%mod[i];
		ll gcd=exgcd(t,mod[i],x,y);
		if(res%gcd){
			cout<<-1<<endl;
			return 0;
		}
		x=ksc(x,res/gcd,mod[i]);
		ans+=x*t;
		t=mod[i]/gcd*t;//t的更新 
		ans=((ans%t)+t)%t;//0~t之间满5足只有一个合法解，它就是 
	}
	cout<<ans<<endl;
	return 0;
}
```

---

## Lucas定理

![image-20230409200204176](C:\Users\26463\AppData\Roaming\Typora\typora-user-images\image-20230409200204176.png)

```
ll work(ll n, ll m) {//Lucas 定理
	if (!n) return 1;
	return (work(n / p, m / p) * C(n % p, m % p)) % p;
}
```

---

## 范德蒙德卷积

$\sum_{i=0}^{k}\binom{n}{i}\binom{m}{k-i}=\binom{n+m}{k}$

#### 推论

$\sum_{i=0}^{n}\binom{n}{i}\binom{n}{i-1}=\binom{2n}{n+1}$

$\sum_{i=0}^{n}\binom{n}{i}^2=\binom{2n}{n}$

$\sum_{i=0}^{m}\binom{n}{i}\binom{m}{i}=\binom{n+m}{m}$

---



## 斯特林数

### 第一类斯特林数

### 第二类斯特林数

组合意义：有n个互不相同的球，放到k个互不区分的盒子里面，每一个盒子里至少一个球，球方案数

递推式$S_2(n,k)=S_2(n-1,k-1)+kS_2(n-1,k)$

通项 $S_2(n,k)=\frac{1}{k!}\sum_{i=0}^{k}(-1)^i\binom{k}{i}(k-i)^n$

重要性质：$n^x=\sum_{k=0}^{n}S_2(n,k)x^{\underline{k}}$



![image-20230414130153028](C:\Users\26463\AppData\Roaming\Typora\typora-user-images\image-20230414130153028.png)



## 数论相关

### 二项式反演

#### 恰好与至多的转化

$f_n$表示恰好有n个的方案数，$g_n$表示至多有n个的方案数

$g_n=\sum_{i=0}^{n}C(n,i)f_i$

$f_n=\sum_{i=0}^{n}(-1)^{n-i}C(n,i)g_i$

* 球染色问题：n个球，k个颜色，求满足相邻颜色不同，每种颜色至少出现一次的方案数。

  不考虑后一个限制，方案数为$k*(k-1)^{n-1}$,实际表示的是至多用k种颜色的方案数，那么直接用二项式反演即可

#### 恰好与至少的转化

$f_n$表示恰好有n个的方案数，$g_n$表示至少有n个的方案数

$g_i=\sum_{x=i}^{n}C(x,i)f_x$

$f_i=\sum_{x=i}^{n}(-1)^{x-i}C(x,i)g_x$





