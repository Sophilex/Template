[TOC]



## Dsu on tree

又叫dsu on tree，一般用来解决下面这类问题

1.只有对子树的查询

2.没有修改操作

其实就有点像[并查集](https://so.csdn.net/so/search?q=并查集&spm=1001.2101.3001.7020)里面的启发式合并，只不过是在树上做信息合并罢了。

例题

给出一个树，求出每个节点的子树中出现次数最多的颜色的编号和

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

大意：

给定一棵以 1 为根，n 个节点的树。设 d(u,x) 为 u 子树中到 u 距离为 x 的节点数。

对于每个点，求一个最小的 k，使得d(u,k) 最大。

思路：

跟前面差不多，维护一下深度再记录一下最大值就可以了

考虑到最大值标记ma在add操作中不能重置，我们应该在撤销影响后初始化ma

```
#include<bits/stdc++.h>
using namespace std;
#define ll int
#define endl '\n'
const ll N=1e6+10;
struct ty
{
	ll t,l,next;
}edge[N<<1];
ll cn=0;
ll head[N];
void add_edge(ll a,ll b,ll c)
{
	edge[++cn].t=b;
	edge[cn].l=c;
	edge[cn].next=head[a];
	head[a]=cn;
}
ll n,m;
ll siz[N],son[N],dep[N],col[N];
ll cnt[N],ans[N];
ll son_flag=0;
ll ma=0,p=0;
void init_dfs(ll id,ll fa)
{
	siz[id]=1;
	ll sn=0;
	for(int i=head[id];i!=-1;i=edge[i].next)
	{
		ll y=edge[i].t;
		if(y==fa) continue;
		dep[y]=dep[id]+1;
		init_dfs(y,id);
		siz[id]+=siz[y];
		if(siz[y]>siz[sn])
		{
			sn=y;
		}
	}
	son[id]=sn;
}
void add(ll id,ll fa,ll val)
{
	cnt[dep[id]]+=val;
	if(cnt[dep[id]]>ma||(cnt[dep[id]]==ma&&dep[id]<p))
	{
		ma=cnt[dep[id]];
		p=dep[id];
	}
	for(int i=head[id];i!=-1;i=edge[i].next)
	{
		ll y=edge[i].t;
		if(y==son_flag||y==fa) continue;
		add(y,id,val);
	}
}
void dfs2(ll id,ll fa,ll kp)
{
	for(int i=head[id];i!=-1;i=edge[i].next)
	{
		ll y=edge[i].t;
		if(y==fa||y==son[id]) continue;
		dfs2(y,id,0);
	}
	if(son[id])
	{
		dfs2(son[id],id,1);
		son_flag=son[id];
	}
	
	add(id,fa,1);
	son_flag=0;
	//统计
	//cout<<id<<' '<<ma<<" "<<p<<' '<<dep[id]<<endl;
	ans[id]=p-dep[id];
	
	if(kp==0)
	{
		add(id,fa,-1);
		ma=0,p=0;
	}
	//
}
void solve()
{
	memset(head,-1,sizeof head);
	cin>>n;
	for(int i=2;i<=n;++i)
	{
		ll a,b;
		cin>>a>>b;
		add_edge(a,b,1);
		add_edge(b,a,1);	
	}
	init_dfs(1,0);
	dfs2(1,0,1);
	for(int i=1;i<=n;++i) cout<<ans[i]<<endl;
} 
int main()
{
	ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);
	solve();
	return 0;
}
```



## 点分治

有时候我们会碰到一些树上的路径问题，如果需要处理的规模很大的话，这时候点[分治](https://so.csdn.net/so/search?q=分治&spm=1001.2101.3001.7020)是一个很好的工具，往往可以在O(nlogn)的复杂度内完成操作，一般用于离线处理问题

模板题

大意：大小为n的树，m次询问，查询树上是否存在长度为k的路径，

1≤n≤10^4,1≤m≤100,1≤k≤10^7

思路：点分治的话复杂度是nmlogn，吃得住

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

树上路径长%3=0的有序点对数量

```
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define endl '\n'
const ll N=2e4+10;
struct ty
{
	ll t,l,next;
}edge[N<<1];
ll cn=0;
ll head[N];
void add(ll a,ll b,ll c)
{
	edge[++cn].t=b;
	edge[cn].l=c;
	edge[cn].next=head[a];
	head[a]=cn;
}
ll n,m,a,b,c;
ll nc;//当前子树的大小
ll siz[N],mx[N];
ll rt;
ll vis[N],dis_vis[5];
ll cnt,dis[N],d[5];
//d:路径长为d[i]的点数 
ll ans;
void get_rt(ll id,ll fa)
{
	siz[id]=1;
	mx[id]=0;//最大子树初始化！ 
	for(int i=head[id];i!=-1;i=edge[i].next)
	{
		ll y=edge[i].t;
		if(y==fa||vis[y]) continue;
		get_rt(y,id);
		siz[id]+=siz[y];
		mx[id]=max(mx[id],siz[y]);
	}
	mx[id]=max(mx[id],nc-mx[id]);
	if(mx[id]<mx[rt])
	{
		rt=id;
	}
}
void get_dis(ll id,ll fa)
{
	d[dis[id]]++;
	for(int i=head[id];i!=-1;i=edge[i].next)
	{
		ll y=edge[i].t;
		if(y==fa||vis[y]) continue;
		dis[y]=(edge[i].l+dis[id])%3;
		get_dis(y,id);	
	}	
} 
void calc(ll id)
{
	dis_vis[0]=1;//到自己的路径长
	for(int i=head[id];i!=-1;i=edge[i].next)
	{
		ll y=edge[i].t;
		if(vis[y]) continue;
		for(int i=0;i<=3;++i) d[i]=0;
		dis[y]=edge[i].l;
		get_dis(y,id);
		for(int i=0;i<3;++i)
		{
			ans+=2*dis_vis[i]*d[(3-i)%3];	
			dis_vis[i]+=d[i];
		} 
	} 
	//ans+=d[0]*d[0]+d[1]*d[2]*2;
	for(int i=0;i<=3;++i) dis_vis[i]=0;
}
void dfz_(ll id)
{
	calc(id);//统计过根的路径长&数量 
	vis[id]=1;
	for(int i=head[id];i!=-1;i=edge[i].next)
	{
		ll y=edge[i].t;
		if(vis[y]) continue;
		rt=0;
		nc=mx[rt]=siz[y];
		get_rt(y,0);get_rt(rt,0);
		dfz_(rt);
	} 
}
void solve()
{
	memset(head,-1,sizeof head);
	cin>>n;
	for(int i=1;i<n;++i)
	{
		cin>>a>>b>>c;
		add(a,b,c%3);
		add(b,a,c%3);
	}
//	for(int i=1;i<=m;++i) cin>>Q[i];
	nc=mx[0]=n;
	get_rt(1,0);
	get_rt(rt,0);
	dfz_(rt);
	ans+=n;
	ll fm=n*n;
	ll g=__gcd(ans,fm);
	cout<<ans/g<<'/'<<fm/g<<endl;
}
int main()
{
	ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);
	solve();
	return 0; 
}
```

### 关于统计点上信息的处理

可见大部分问题都是关于路径信息的，如果变成点上信息的话，每一个分治查询的根节点就也会对子树的情况产生影响。我们可以在一开始先不更新根节点信息，然后在子树内节点更新信息的时候，把根节点的信息加上去，然后去除影响之前，先撤掉根节点的信息。具体实现可以看代码

大意：树上每一个点有一个颜色a/b/c，统计树上有多少条路径满足其上三种颜色的数量一样多

```
#include<bits/stdc++.h>
using namespace std;
#define ll int
#define endl '\n'
#define pii pair<ll,ll>
const ll N=1e5+10;
struct ty
{
    ll t,l,next;
}edge[N<<1];
ll cn=0;
ll head[N];
char col[N];
void add(ll a,ll b,ll c)
{
    edge[++cn].t=b;
    edge[cn].l=c;
    edge[cn].next=head[a];
    head[a]=cn;
}
ll n,m;
pii ini,ii;
ll a,b,c;
ll siz[N],mx[N],rt,nt;
// siz子树大小 dis到根的距离 mx最大子树对应节点
ll cnt;//当前存在的路径长度
map<pii,ll> dis_vis;
pii dis[N],d[N];
//节点是否已经分治过，在某次分治中距离为dis_vis[i]的节点是否存在
ll ans;
ll vis[N];
pii upd(pii p,ll id)
{
    pii pp=p;
    if(col[id]=='a') pp.first++;
    if(col[id]=='b') pp.first--,pp.second++;
    if(col[id]=='c') pp.second--;
    return pp;
}
pii operator +(pii a,pii b)
{
    pii c=a;
    c.first+=b.first;
    c.second+=b.second;
    return c;
}
pii operator -(pii a,pii b)
{
    pii c=a;
    c.first-=b.first;
    c.second-=b.second;
    return c;
}
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
    pii pre=dis[id]+ii;
    d[++cnt]=pre;
    // cout<<id<<' '<<fa<<' '<<pre.first<<' '<<pre.second<<endl;

    for(int i=head[id];i!=-1;i=edge[i].next)
    {
        ll y=edge[i].t;
        if(y==fa||vis[y]) continue;
        dis[y]=upd(dis[id],y);
        // cout<<y<<" "<<id<<' '<<dis[y].first<<" "<<dis[y].second<<endl;
        get_dis(y,id);
    }
}

void calc(ll id)//统计过根的路径
{
    ini.first=0;ini.second=0;
    ii=ini;ii=upd(ii,id);
    dis_vis[ini]=1;//根节点的情况
    vector<pii> vt;vt.clear();
    for(int i=head[id];i!=-1;i=edge[i].next)
    {
        ll y=edge[i].t;
        if(vis[y]) continue;
        cnt=0;//清空d数组
        dis[y]=upd(ini,y);
        // cout<<id<<" "<<y<<' '<<dis[y].first<<" "<<dis[y].second<<endl;

        get_dis(y,id);
        for(int j=1;j<=cnt;++j)
        {
            pii pre=d[j];pre.first*=-1;pre.second*=-1;
            ans+=dis_vis[pre];
        }
        for(int j=1;j<=cnt;++j)
        {
            pii pre=d[j]-ii;
            dis_vis[pre]++,vt.push_back(pre);
        }
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
    cin>>n;
    for(int i=1;i<=n;++i) dis[i]=make_pair(0,0);
    for(int i=1;i<=n;++i) cin>>col[i];

    for(int i=1;i<n;++i)
    {
        cin>>a>>b;
        add(a,b,1);
        add(b,a,1);
    }
    mx[0]=nt=n;
    find_rt(1,0);
    find_rt(rt,0);//更新siz数组，因为现在是以一个新的点为根节点
    // cout<<rt<<endl;
    dfz_(rt);
    cout<<ans<<endl;
}
int main()
{
    ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);
    solve();
}

```



## 线性基

---

1.数组中的任意一个数可以**唯一地**由线性基中的若干元素异或得到

2.线性基中任意多个元素的异或值不为0

3.线性基元素的异或集合等于原数组元素的异或集合

4.线性基中每一个元素的最高位的1必定不被更低位置的元素包含

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

### 求数组异或最小值

如果有元素不能插入线性基，那么最小值显然就是0，否则就是线性基里最小的元素，因为最小的元素无论异或谁都会变大

### 求数组异或的第k小

重新构造线性基，使p[i]的第 j 位在p[j]存在的情况下不为 1，这样异或p[j]一定使数值变大，抽象化为二进制便是取 p [ j ]相当于 j 位取 1，则可以将 k 进行按位查看是否异或得结果。

tips：当线性基个数cnt小于 n 时，说明非满秩，所有结果中可以异或出 0，这样看来第 1 小得为 0 而非p[0],所以对k 减一。总的异或结果是不超过$2^{cnt}-1$的。

```
//#pragma GCC optimize("O3")
//#pragma GCC optimize("unroll-loops")
#include<iostream>
#include<algorithm>
#include<vector>
#include<cstring>

using namespace std;
typedef long long ll;
const int maxn=55;

ll p[maxn],f[maxn],cnt,n,m,ans,u,v,w;

inline void insert(ll x)
{
	for(int i=50;i>=0;i--)
	{
		if(x>>i&1)
		{
			if(!p[i]){p[i]=x; break;}
			x^=p[i];
		}
	}
	return;
}

inline void rebuild()
{
	for(int i=0;i<=50;i++)
	{
		for(int j=i-1;j>=0;j--)//尝试将自身低位去掉
		{
			if(p[i]>>j&1)p[i]^=p[j];
		}
		if(p[i])f[cnt++]=p[i];
	}
	return;
}

inline ll query(ll k)
{
	if(cnt!=n)k--;
	if(k>=(1ll<<cnt))return -1;
	ll ans=0;
	for(int i=0;i<cnt;i++)ans^=f[i]*(k>>i&1);
	return ans;
}

int main()
{
	ios::sync_with_stdio(false);
	cin.tie(0); cout.tie(0);
	cin>>n;
	for(int i=1;i<=n;i++)
	{
		cin>>u,insert(u);
	}
	rebuild(),cin>>m;
	for(int i=1;i<=m;i++)
	{
		ll k; cin>>k,cout<<query(k)<<"\n";
	} 
	return 0;
}

```



### 利用线性基考虑异或出某一个数的方案数

假设数组的大小为n，其线性基的大小为k，如过数字v能够被线性表出，则**异或出它的方案数就是$2^{n-k}$**，也就是线性基外的数字集合W的所有子集的数量。如果我们在W的子集中选择的是空集，线性基本身的元素自然可以构造出V，且方案数唯一。否则我们记选择的W的子集异或出来的结果为k，从高维往低位考虑，如果某一位需要翻转，只要异或上对应的元素即可。

## 点分树

动态点分治。把点分治过程中的的前后遍历到的重心在一颗虚树上连接起来，前者作为后者的父亲，这样整个虚树的树高只有log(n)级别。虚树上两个点x,y的lca一定在原树中位于它们的简单路径上，因为在lca作为重心之后它们才会被分到两个连通块。

支持快速修改操作，但是这个操作修改的内容应该是与树的形态无关的（否则点分树统计的内容就没有意义了）

点分树在递归进行点分治的过程中完成虚树的建立。

实现细节：

* 子连通块大小不要直接用 $size(to)$（这都是从淀粉质那里遗留下来的问题了，但依旧有人不重视）
* 一般不需要把虚树的实际形态建出来，但如果能用到的话，注意不要和原树搞混。
* 树状数组大小要设置得当。设 $now=|subtree(i)|$，由于 $i$ 在$ subtree(i)$中为重心，所以$ f1(i,j)$中 $j$的值域为 $0-now/2$，f2(i,j)中 j的值域为 $1-now$。由于下标可以为$ 0$，在树状数组内部还需要统一向后移一位。也就是说 $f1,f2 $的大小要分别设置为 $now/2+1 $和 $now+1 $。
* 如果预处理了每个点在虚树上的祖先，修改 / 查询 中跳父亲时需要倒序枚举（具体见代码）。
* 关于卡常：点分树做题经常会用到 vector,vector, set,set, multiset,multiset, priority queuepriority queue 之类的东西，如果您嫌弃它们太慢且不愿开 O2O2，自备几个能代替 STLSTL 的模板吧...

模板题：维护一颗带点权树，需要支持两种操作：修改 x 的点权，查询与点 x距离不超过 K的点的权值之和。

对于每一个点i统计在虚树中其子树内到其距离$\leq d$的点权之和，记为$f_1(i,d)$,在虚树上递归往上查看其父亲u的时候，我们记u与i在原树中的距离为$dis$,多余的贡献是在u子树内但是不在i子树内的点的贡献，也就是在u子树内但是不在x子树内且与x距离$\leq d-dis$的点权之和,也就是u子树内距离u$\leq d-dis$的点权之和减去x子树内距离u$\leq d-dis$的点权之和。所以再记录$f_2(i,d)$,表示距离x在虚树上的父亲u$\leq d$的点权之和，然后容斥计算即可。

```
#include<bits/stdc++.h>
using namespace std;
#define ll int
#define endl '\n'
#define low(x) x&(-x)
#define Re ll
const ll N=1e5+10;
const ll inf=2e9;
struct ty
{
    ll t,l,next;
}edge[N<<1];
ll cn=0;
ll head[N],dep[N];
void add(ll a,ll b,ll c)
{
    edge[++cn].t=b;
    edge[cn].l=c;
    edge[cn].next=head[a];
    head[a]=cn;
}
struct LCA
{
    ll F[N],son[N],siz[N],top[N];

    void init_dfs(ll id,ll fa)
    {
        dep[id]=dep[fa]+1;
        F[id]=fa;
        siz[id]=1;
        son[id]=0;
        top[id]=0;
        for(int i=head[id];i!=-1;i=edge[i].next)
        {
            ll y=edge[i].t;
            if(y==fa) continue;
            init_dfs(y,id);
            siz[id]+=siz[y];
            if(siz[y]>siz[son[id]])
            {
                son[id]=y;
            }
        }
    }
    void dfs2(ll id,ll p)
    {
        top[id]=p;
        if(!son[id])return;
        dfs2(son[id],p);
        for(int i=head[id];i!=-1;i=edge[i].next)
        {
            ll y=edge[i].t;
            if(y!=F[id]&&y!=son[id]) dfs2(y,y);
        }
    }
    ll gt_lca(ll x,ll y)
    {
        while(top[x]!=top[y])
        {
            if(dep[top[x]]<dep[top[y]]) swap(x,y);
            x=F[top[x]];
        }
        return dep[x]>dep[y]?y:x;
    }
    void init()
    {
        init_dfs(1,0);
        dfs2(1,1);
    }
}T;
struct Bit
{
    ll n;vector<ll> tr;
    void init(ll M)
    {
        n=M;
        tr.resize(n+2);
    }
    void add(ll x,ll y)
    {
        x++;
        while(x<=n)
        {
            tr[x]+=y;
            x+=low(x);
        }
    }
    ll sum(ll x)
    {
        x++;x=min(x,n);
        ll ans=0;
        while(x)
        {
            ans+=tr[x];
            x-=low(x);
        }
        return ans;
    }
}tr1[N],tr2[N];
ll vis[N],mx[N],siz[N],fa[N];//fa表示虚树中的父亲
ll rt,nt;//当前的根，当前处理的连通块的大小
ll get_dis(ll x,ll y)
{
    //返回真实树中两点的距离
    return dep[x]+dep[y]-dep[T.gt_lca(x,y)]*2;
}
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
void dfz_(ll id,ll p)//点分治
{
    ll now=nt;vis[id]=1,fa[id]=p;
    tr1[id].init(now/2+1),tr2[id].init(now+1);//由重心性质可知,TR1会使用[0,now/2],TR2会使用[1,now]，向后移一位变为[1,now/2+1]和[2,now+1]
    for(int i=head[id];i!=-1;i=edge[i].next)
    {
        ll y=edge[i].t;
        if(vis[y]) continue;
        //递归
        nt=siz[y]>siz[id]?now-siz[id]:siz[y];
        rt=0;mx[rt]=inf;
        find_rt(y,0);find_rt(rt,0);//更新siz
        dfz_(rt,id);
    }
}
ll n,val[N],k,q,op,nans;
void change(ll x,ll y)//val[x]+=y
{
    tr1[x].add(0,y);
    for(int i=x;fa[i];i=fa[i])
    {
        ll Dis=get_dis(x,fa[i]);
        tr1[fa[i]].add(Dis,y);
        tr2[i].add(Dis,y);
    }
}
ll query(ll x,ll d)//到x的距离为d
{
    ll ans=tr1[x].sum(d);
    for(int i=x;fa[i];i=fa[i])
    {
        ll Dis=get_dis(x,fa[i]);
        if(Dis>d) continue;
        ans+=tr1[fa[i]].sum(d-Dis);
        ans-=tr2[i].sum(d-Dis);
    }
    return ans;
}
void solve()
{
    memset(head,-1,sizeof head);
    cin>>n>>q;
    for(int i=1;i<=n;++i) cin>>val[i];
    for(int i=1;i<n;++i)
    {
        ll a,b;cin>>a>>b;
        add(a,b,1);add(b,a,1);
    }
    T.init();
    nt=n,rt=0,mx[rt]=inf;
    find_rt(1,0);find_rt(rt,0);
    dfz_(rt,0);
    for(int i=1;i<=n;++i) change(i,val[i]);
    ll ans=0;
    while(q--)
    {
        ll x,y;
        cin>>op>>x>>y;
        x^=ans;y^=ans;
        if(op==0)
        {
            nans=query(x,y);
            ans=nans;
            cout<<nans<<endl;
            continue;
        }
        change(x,y-val[x]);
        val[x]=y;
    }
}
int main()
{
    // freopen("P6329_1.in","r",stdin);
    ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);
    solve();
    return 0;
}

```



## 李超线段树

$O(nlon)$统计某一点最大/最小值线段对应的值

该模板为统计最大值

```
namespace LcTree
{
    struct ty
    {
        ll l,r;
        double k,b;//记录线段的斜率，截距
        ll fl;//是否插入过线段
        /*
            如果线段可能存在小于0的情况，可能会需要维护该区间是否有插入线段
            该模板没有维护该情况，若需要请自行修改
        */
        // #define ls p<<1
        // #define rs p<<1|1
        // #define k(p) tr[p].k
        // #define b(p) tr[p].b
        // #define l(p) tr[p].l
        // #define r(p) tr[p].r
    }tr[N<<2];
    void build(ll p,ll l,ll r)
    {
        tr[p].l=l;tr[p].r=r;tr[p].fl=0;//还没有插入直线
        if(l==r)
        {
            tr[p].k=tr[p].b=0.0;
            return;
        }
        ll mid=l+r>>1;
        build(p<<1,l,mid);build(p<<1|1,mid+1,r);
    }
    double cal(ll p,ll pos)
    {
        return tr[p].k*(double)(pos*1.0)+tr[p].b;
    }
    void upd(ll p,double k,double b)
    {
        tr[p].k=k;tr[p].b=b;
    }
    void change(ll p,ll l,ll r,double val_k,double val_b)
    {
        ll mid=tr[p].l+tr[p].r>>1;
        if(tr[p].l>=l&&tr[p].r<=r)
        {
            if(tr[p].fl==0)
            {
                tr[p].fl=1;
                upd(p,val_k,val_b);
                return;
            }
            double x0=tr[p].l*1.0,x1=tr[p].r*1.0;
            double y0p=cal(p,x0);double y1p=cal(p,x1);
            double y0=val_k*x0+val_b;double y1=val_k*x1+val_b;
            if(y0>=y0p&&y1>=y1p) upd(p,val_k,val_b);
            else if(y0<=y0p&&y1<=y1p) return;
            else
            {
                double mid=(tr[p].l*1.0+tr[p].r*1.0)/2.0;
                double jiao=(val_b-tr[p].b)*1.0/(tr[p].k-val_k);
                if(y0>y0p)
                {
                    if(jiao<=mid) change(p<<1,l,r,val_k,val_b);
                    else change(p<<1|1,l,r,tr[p].k,tr[p].b),upd(p,val_k,val_b);
                }
                else
                {
                    if(jiao<=mid) change(p<<1,l,r,tr[p].k,tr[p].b),upd(p,val_k,val_b);
                    else change(p<<1|1,l,r,val_k,val_b);
                }
            }

            return;
        }
        if(l<=mid) change(p<<1,l,r,val_k,val_b);
        if(r>mid) change(p<<1|1,l,r,val_k,val_b);
    }
    double query(ll p,ll pos)
    {
        if(tr[p].l==tr[p].r) return cal(p,pos);
        double res=cal(p,pos);
        ll mid=tr[p].l+tr[p].r>>1;
        if(pos<=mid) res=max(res,query(p<<1,pos));
        else res=max(res,query(p<<1|1,pos));
        return res;
    }
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



## 竞赛图

竞赛图(tournament)：有 $n^2$条边的有向图



性质：

- 竞赛图强连通缩点后的 DAG 是一条链
- 竞赛图的强连通块存在一条哈密顿回路
- 竞赛图存在一条哈密顿路径
- 竞赛图 $size>1$的强连通块中，大小为 $[3,size]$简单环均存在
- 兰道定理（竞赛图判定定理）：定义一个竞赛图的比分序列是把竞赛图每个点的出度从小到大排列得到的序列。一个长度为 n的序列 s1≤s2≤s3≤…≤sn 是合法比分序列当且仅当 ∀i∈[1,n],$\sum_{j=1}^{i}s_j>=C(i,2)$，且在 i=n时取等号，[证明](https://blog.csdn.net/a_crazy_czy/article/details/73611366) 非常巧妙！

计数：

- n阶竞赛图个数显然就是$h_n=2^{C(n,2)}$啦

- 强连通 n 阶竞赛图个数为$f_n=h_n-\sum_{i=1}^{n-1}f_iC(n,i)h_{n-i}$ ，就枚举一个 SCC 连的都是出边。

- 生成函数加速：
  令 f0=0

  有：$h_n=\sum_{i=0}^{n}f_iC(n,i)h_{n-i}+[n==0]$

  H=FH+1⇒F=1−H−1

