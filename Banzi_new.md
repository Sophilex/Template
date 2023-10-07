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

```c++
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

## 树链剖分

### 注意事项

易写错：

* dfs2（y,y）//开新的链
  
* 确定rt=1，勿忘记给rt赋值
  
* 树上路径查找/修改时，第一句话：while（top[x]!=top[y]）,而不是while（dep[top[x]]!=dep[top[y]]）
* 含有区间覆盖操作时，线段树add需要初始化为-1，要一开始在定义时就把-1赋上去

### 基本板子

```c++
ll dep[N];//节点深度
ll fa[N];//节点父亲
ll son[N];//节点重儿子
ll siz[N];//节点子树的大小
ll mas[N],a[N];//初始序列   处理后的序列
ll cnt=0;//dfs序的计数器
ll idx[N];//节点的dfs序 id 
ll top[N];//节点所在链的顶端 要在init_dfs处理之后才能更新这个 

void init_dfs(ll id,ll p,ll depth)//节点 父亲 深度 
{//更新重儿子以及各节点的子树大小&深度 
	dep[id]=depth;
	fa[id]=p;
	siz[id]=1;
	ll son_val=0;//重儿子的子树大小
	for(int i=head[id];i!=-1;i=edge[i].next)
	{
		ll y=edge[i].t;
		if(y==p) continue;
		init_dfs(y,id,depth+1);
		siz[id]+=siz[y];//子树大小更新 
		if(siz[y]>son_val)
		{
			son_val=siz[y];
			son[id]=y;
		}
    } 
} 

void dfs2(ll id,ll p)
{
	idx[id]=++cnt;
	a[cnt]=mas[id];//序列转移
	top[id]=p;
	if(!son[id]) return;//无重儿子的情况(也就是无儿子，是叶子节点)直接返回
	//下面的dfs顺序：先遍历重儿子，再遍历轻儿子 
	dfs2(son[id],p);//顶端idx不变
	for(int i=head[id];i!=-1;i=edge[i].next)
	{
		ll y=edge[i].t;
		if(!idx[y]) dfs2(y,y);//新的链(轻链) 
	} 
}

void push_up(ll p)
{
	tr[p].val=(tr[p<<1].val+tr[p<<1|1].val);
}

void build(ll p,ll l,ll r)
{
	tr[p].l=l;tr[p].r=r;tr[p].siz=r-l+1;
	if(l==r)
	{
		tr[p].val=a[l];//用新的序列值 
		return;
	}
	ll mid=l+r>>1;
	build(p<<1,l,mid);
	build(p<<1|1,mid+1,r);
	push_up(p);
}

void push_down(ll p)
{
	if(tr[p].add==0) return;
	tr[p<<1].val=(tr[p<<1].val+tr[p<<1].siz*tr[p].add);
	tr[p<<1|1].val=(tr[p<<1|1].val+tr[p<<1|1].siz*tr[p].add);
	tr[p<<1].add=(tr[p<<1].add+tr[p].add);
	tr[p<<1|1].add=(tr[p<<1|1].add+tr[p].add);
	tr[p].add=0;//下传结束 
}

void add(ll p,ll l,ll r,ll val)
{
	if(tr[p].l>=l&&tr[p].r<=r)
	{
		tr[p].val=(tr[p].val+tr[p].siz*val);
		tr[p].add=(tr[p].add+val);
		return;
	}
	push_down(p);
	ll mid=tr[p].l+tr[p].r>>1;
	if(l<=mid) add(p<<1,l,r,val);
	if(r>=mid+1) add(p<<1|1,l,r,val);
	push_up(p);
}

ll sum(ll p,ll l,ll r)
{
	ll ans=0;
	if(tr[p].l>=l&&tr[p].r<=r) return tr[p].val;
	push_down(p);
	ll mid=tr[p].l+tr[p].r>>1;
	if(l<=mid) ans=(ans+sum(p<<1,l,r));
	if(r>=mid+1) ans=(ans+sum(p<<1|1,l,r));
	return ans;
}

ll tree_sum(ll x,ll y)//树上路径和 
{
	ll ans=0;
	while(top[x]!=top[y])
	{
		if(dep[top[x]]<dep[top[y]]) swap(x,y);
		ans=(ans+sum(1,idx[top[x]],idx[x]));
		x=fa[top[x]];
	}
	if(dep[x]>dep[y]) swap(x,y);
	//来到同一重链 
	ans=(ans+sum(1,idx[x],idx[y]));
	return ans;
}

void tree_add(ll x,ll y,ll val)
{
	
	while(top[x]!=top[y])
	{
		if(dep[top[x]]<dep[top[y]]) swap(x,y);
		add(1,idx[top[x]],idx[x],val);
		x=fa[top[x]];
	}
	if(dep[x]>dep[y]) swap(x,y);
	//来到同一重链
	add(1,idx[x],idx[y],val); 
}

ll get_lca(ll x,ll y)//获取两节点的lca 
{
	while(top[x]!=top[y])
	{
		if(dep[top[x]]<dep[top[y]]) swap(x,y);
		x=fa[top[x]];
	}
	return dep[x]>dep[y]?y:x;
}

ll find_firson(ll x,ll y)//找到x到y路径上的第一个儿子
{
    while(top[x]!=top[y])
	{
		if(dep[top[x]]<dep[top[y]]) swap(x,y);
		if(fa[top[x]]==y) return top[x];
		x=fa[top[x]];
	}	
	if(dep[x]<dep[y]) swap(x,y);
	return son[y];
} 

void trson_add(ll x,ll val)//以x为根的子树所有权值+=val 
{
	if(x==rt)
	{
		add(1,1,n,val);
		return;
	}
	//////
	ll LCA=get_lca(x,rt);
	if(LCA!=x) 
	{
		add(1,idx[x],idx[x]+siz[x]-1,val);
		return;
	}
	//////
	ll child=find_firson(x,rt);
	add(1,1,n,val);
	add(1,idx[child],idx[child]+siz[child]-1,-val);
	
}
```



### 边权下放点权

1.dfs时更新点权，也就是让边权赋给子节点

```c++
for(int i=head[id];i!=-1;i=edge[i].next)
	{
		ll y=edge[i].t;
		if(y==p) continue;
		a[y]=edge[i].l; //如果初始边权不为0，点权下放边权时要执行这一句操作 
		init_dfs(y,id,depth+1);
		siz[id]+=siz[y];
		if(siz[y]>son_val)
		{
			son_val=siz[y];
			son[id]=y;
		}
	}	
```

2.树上路径更新的时候，你稍微手玩一下就会发现两个节点的lca是不应该被更新的，所以在最后一步更新的时候，我们的代码要有所改变

```c++

ll tree_add(ll x,ll y,ll val)
{
	while(top[x]!=top[y])
	{
		if(dep[top[x]]<dep[top[y]]) swap(x,y);
		add(1,idx[top[x]],idx[x],val); 
		x=fa[top[x]];
	}
	if(dep[x]>dep[y]) swap(x,y);
	add(1,idx[x]+1,idx[y],val);//lca不查询，所以x要+1
}
```

当然你也可以这么写

```c++

ll tree_add(ll x,ll y,ll val)
{
	while(top[x]!=top[y])
	{
		if(dep[top[x]]<dep[top[y]]) swap(x,y);
		add(1,idx[top[x]],idx[x],val); 
		x=fa[top[x]];
	}
	if(dep[x]>dep[y]) swap(x,y);
	add(1,idx[x],idx[y],val);
    add(1,idx[x],idx[x],-val);
}
#其实就是把lca的更新的结果给改回去
```

3.查询树上路径权值和时（如果有这个操作），同理两点的lca的点权不应该被算进去，因为它对应的边不属于这两点的路径上。

```c++

ll tree_sum(ll x,ll y)
{
	ll ans=0;
	while(top[x]!=top[y])
	{
		if(dep[top[x]]<dep[top[y]]) swap(x,y);
		ans+=sum(1,idx[top[x]],idx[x]);
		x=fa[top[x]];
	}
	if(dep[x]>dep[y]) swap(x,y);
	ans+=sum(1,idx[x]+1,idx[y]);//点权下放边权操作核心代码，lca不查询，所以x要+1 
	return ans;
}
```

同理，你也可以这么写,意思我就不解释了

```c++
ll tree_sum(ll x,ll y)
{
	ll ans=0;
	while(top[x]!=top[y])
	{
		if(dep[top[x]]<dep[top[y]]) swap(x,y);
		ans+=sum(1,idx[top[x]],idx[x]);
		x=fa[top[x]];
	}
	if(dep[x]>dep[y]) swap(x,y);
	ans+=sum(1,idx[x],idx[y]);
    ans-=sum(1,idx[x],idx[x]);
	return ans;
}
```



## 线性基

---

1.数组中的任意一个数可以**唯一地**由线性基中的若干元素异或得到

2.线性基中任意多个元素的异或值不为0

3.线性基元素的异或集合等于原数组元素的异或集合

4.线性基中每一个元素的最高位的1必定不被更低位置的元素包含

5.线性基是能够满足条件1的**最小**集合

---

```c++
//偷的板子
struct Linear_basis{//默认不考虑空集 
    long long d[65];//数组d表示序列 A的线性基  
    int tot; 
    bool flag;
    void init(){
        tot=flag=0;
        for(int i=0;i<=60;i++)d[i]=0;
    } 
    void add(long long x){
        for(int i=60;i>=0;i--){
            if(x>>i&1){
                if(d[i])x^=d[i];
                else {
                    d[i]=x;
                    tot++;
                    break;
                }
            }
        }
        return;
    }
//  long long get_mx(){
//      long long ans=0;
//      for(int i=60;i>=0;i--)
//          if((ans^d[i])>ans)ans^=d[i];
//      return ans;
//  }
//  long long get_mi(){
//      if(tot<n)return 0;
//      for(int i=0;i<=60;i++)if(d[i])return d[i];
//      return -1;
//  }
//  void sort(){
//      if(flag)return;
//      for(int i=1;i<=60;i++){
//          for(int j=i-1;j>=0;j--){
//              if(d[i]>>j&1)d[i]^=d[j];
//          }
//      }
//      flag=1;
//      return;
//  }
//  long long get_K(long long K){
//      //讨论能否得到0 
//      if(K==1&&tot<n)return 0;
//      if(tot<n)K--;
//      sort();
//      long long ans=0;
//      for(int i=0;i<=60;i++){ 
//          if(d[i]>0){
//              if(K&1)ans^=d[i];
//              K>>=1;
//          }
//      } 
//      return ans;
//  }
}S;
```



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

```c++
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

```c++
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

假设数组的大小为n，其线性基的大小为k，**如果数字v能够被线性表出**（这是前提），则**异或出它的方案数就是$2^{n-k}$**，也就是线性基外的数字集合W的所有子集的数量。如果我们在W的子集中选择的是空集，线性基本身的元素自然可以构造出V，且方案数唯一。否则我们记选择的W的子集异或出来的结果为k，从高维往低位考虑，如果某一位需要翻转，只要异或上对应的元素即可。

### 求数组在$[1,R]$内异或出的最大值

仿照求数组异或第k小中的思路，重新构造线性基，使p[i]的第 j 位在p[j]存在的情况下不为 1，这样异或p[j]一定使数值变大，抽象化为二进制便是取 p [ j ]相当于 j 位取 1。记最终答案为ans，初始为0。那么我们只要从高位往低位遍历，如果某一位的线性基$\leq R$,直接贪心让ans异或上这个结果，并将R也异或上这个结果即可，证明显然。

### Tip

有些时候数字的位数可能会很多（几百位），那么我们在求一些异或运算或者比较数字大小的过程中就只能按位遍历了，然后就可能会T。一个优化技巧是压位，将每64位用一个unsigned long long 存储，那么能优化的幅度还是很可观的。

```c++
struct ZeroOneLinearEquation {
    vector<unsigned long long> f;
    ZeroOneLinearEquation() {
        //压位看这里
        f = vector<unsigned long long>((EQ_SIZE + 63) / 64, 0);
    }
    void set(int x) {
        f[x / 64] |= 1ULL << (x % 64);
    }
    void reset(int x) {
        f[x / 64] &= ~(1ULL << (x % 64));
    }
    bool get(int x) const {
        return f[x / 64] & (1ULL << (x % 64));
    }
    bool is_all_zeros() const {
        for (int i = 0; i < (int)f.size(); ++ i) {
            if (f[i]) {
                return false;
            }
        }
        return true;
    }
 
    int highest_bit() const {
        for (int i = (int)f.size() - 1; i >= 0; -- i) {
            if (f[i]) {
                return i * 64 + __builtin_clzll(f[i]) ^ 63;
            }
        }
        return -1;
    }
};
 
ZeroOneLinearEquation operator^(const ZeroOneLinearEquation &a, const ZeroOneLinearEquation &b) {
    ZeroOneLinearEquation c;
    for (int i = 0; i < (int)a.f.size(); ++ i) {
        c.f[i] = a.f[i] ^ b.f[i];
    }
    return c;
}
 
struct ZeroOneLinearEquationSystem {
    vector<ZeroOneLinearEquation> equations;
    vector<int> id_bit;
    ZeroOneLinearEquationSystem() {
        equations.clear();
        id_bit.clear();
    }
 
    void add(const ZeroOneLinearEquation &equation) {
        equations.push_back(equation);
    }
 
    void get_basis() {
        int l = 0;
        for (int i = EQ_SIZE - 1; i >= 0; -- i) {
            int j = l;
            while (j < (int)equations.size() && !equations[j].get(i)) {
                ++ j;
            }
            if (j < (int)equations.size()) {
                swap(equations[l], equations[j]);
                for (int k = l + 1; k < (int)equations.size(); ++ k) {
                    if (equations[k].get(i)) {
                        equations[k] = equations[k] ^ equations[l];
                    }
                }
                for (int k = 0; k < l; k ++) {
                    if (equations[k].get(i)) {
                        equations[k] = equations[k] ^ equations[l];
                    }
                }
                id_bit.push_back(i);
                ++ l;
            }
        }
        equations.resize(l);
    }
 
    bool can_describe(ZeroOneLinearEquation equation) {
        for (int i = 0; i < (int)equations.size(); ++ i) {
            if (equation.get(id_bit[i])) {
                equation = equation ^ equations[i];
            }
        }
        return equation.is_all_zeros();
    }
};
 
ZeroOneLinearEquation read_a_equation() {
    ZeroOneLinearEquation equation;
    static char s[1000];
    scanf(" %s", s);
    int n = strlen(s);
    reverse(s, s + n);
    for (int i = 0; i < n; ++ i) {
        if (s[i] == '1') {
            equation.set(i);
        }
    }
    return equation;
}
 
ZeroOneLinearEquationSystem es;
const int MOD = 1e9 + 7;
 
long long pow2[301];
 
bool operator>(const ZeroOneLinearEquation& a, const ZeroOneLinearEquation& b) {
    for (int i = (int)a.f.size() - 1; i >= 0 ; -- i) {
        if (a.f[i] > b.f[i]) {
            return true;
        } else if (a.f[i] < b.f[i]) {
            return false;
        }
    }
    return false;
}
```

## 点分树

动态点分治。把点分治过程中的的前后遍历到的重心在一颗虚树上连接起来，前者作为后者的父亲，这样整个虚树的树高只有log(n)级别。虚树上两个点x,y的lca一定在原树中位于它们的简单路径上，<font color='red'>因为在lca作为重心之后它们才会被分到两个连通块</font>。

支持快速修改操作，但是这个操作修改的内容应该是与树的形态无关的（否则点分树统计的内容就没有意义了）

点分树在递归进行点分治的过程中完成虚树的建立。

实现细节：

* 子连通块大小不要直接用 $size(to)$（这都是从淀粉质那里遗留下来的问题了，但依旧有人不重视）
* 一般不需要把虚树的实际形态建出来，但如果能用到的话，注意不要和原树搞混。
* 树状数组大小要设置得当。设 $now=|subtree(i)|$，由于 $i$ 在$ subtree(i)$中为重心，所以$ f1(i,j)$中 $j$的值域为 $0-now/2$，f2(i,j)中 j的值域为 $1-now$。由于下标可以为$ 0$，在树状数组内部还需要统一向后移一位。也就是说 $f1,f2 $的大小要分别设置为 $now/2+1 $和 $now+1 $。
* 如果预处理了每个点在虚树上的祖先，修改 / 查询 中跳父亲时需要倒序枚举（具体见代码）。
* 关于卡常：点分树做题经常会用到 vector, set,multiset, priority queue之类的东西，如果您嫌弃它们太慢且不愿开 O2,自备几个能代替 STL的模板吧...

模板题：维护一颗带点权树，需要支持两种操作：修改 x 的点权，查询与点 x距离不超过 K的点的权值之和。

<font color='orange'>对于每一个点i统计在虚树中其子树内到其距离$\leq d$的点权之和，记为$f_1(i,d)$</font>在虚树上递归往上查看其父亲u的时候，我们记u与i在原树中的距离为$dis$,多余的贡献是在u子树内但是不在i子树内的点的贡献，也就是在u子树内但是不在x子树内且与x距离$\leq d-dis$的点权之和,也就是u子树内距离u$\leq d-dis$的点权之和减去x子树内距离u$\leq d-dis$的点权之和。所以再记录<font color='orange'>$f_2(i,d)$,表示距离x在虚树上的父亲u$\leq d$的点权之和</font>，然后容斥计算即可。

```c++
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

[Hdu Landmine](http://acm.hdu.edu.cn/showproblem.php?pid=7358)

大意：
一棵树，每一个点有一个半径$r_i$，若i点被引爆，一秒后距离i点$\leq r_i$的点被引爆

$\forall i\in [2,n],$引爆i点之后，1点什么时候被引爆

思路：

考虑 **BFS**，每次要找所有未被扫过的点中能够炸到当前点 *i* 的所有点，相当于找所有未被标记的点 *j* 使

得 *dist*(*i, j*) *≤* $r_j$。建立点分树，那么就相当于对所有 *i* 在点分树上的祖先 *u*，找 *u* 点分树子树中所有未

被标记的点 *j*，使得 *dist*(*i, u*) *≤* *$r_j$* *−* *dist*(*j, u*)(因为在点分树下i,j之间的路径一定经过u)。直接对每个点 *u* 按照 $r_j$*−* *dist*(*j, u*) 将所有 *j* 排序即可。

每次扫指针找到所有需要求的点。

这题主要还是看一下另一种对距离的处理方法，具体见代码

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define IL inline
#define pii pair<ll,ll>
#define endl '\n'
const ll inf=2e9;
const ll N=1e5+10;
struct ty
{
    ll t,l,next;
}edge[N<<1];
ll cn=0;
ll head[N];
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
void add(ll a,ll b,ll c)
{
    edge[++cn].t=b;
    edge[cn].l=c;
    edge[cn].next=head[a];
    head[a]=cn;
}
ll n,m,a,b,c;
ll siz[N],mx[N],rt,nt,dep[N];
bool vis[N];
//节点是否已经分治过，在某次分治中距离为dis_vis[i]的节点是否存在
ll ans[110];
ll R[N];//radius
ll F[N];//虚树中的父亲节点
ll cur;//当前重心的dep
vector<pii> vt[N];//每一个点的子树中的信息
ll dis[20][N];
ll tim[N],rvis[N];
ll vt_siz[N];//每一个点的子树中的最后一个点，倒序枚举
queue<ll> q;
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
void cal(ll id,ll fa)
{
    // cout<<id<<' '<<fa<<" "<<cur<<" "<<dis[cur][id]<<endl;
    vt[rt].push_back(make_pair(R[id]-dis[cur][id],id));
    for(int i=head[id];i!=-1;i=edge[i].next)
    {
        ll y=edge[i].t;
        if(y==fa||vis[y]) continue;
        dis[cur][y]=dis[cur][id]+edge[i].l;
        cal(y,id);
    }
}
void dfz_(ll id,ll p)//点分治
{
    F[id]=p;vis[id]=1;
    cur=dep[id];
    dis[cur][id]=0;
    cal(id,0);
    sort(vt[id].begin(),vt[id].end());//处理出了子树下的所有未被标记的节点到其的价值
    for(int i=head[id];i!=-1;i=edge[i].next)
    {
        ll y=edge[i].t;
        if(vis[y]) continue;
        //递归
        rt=0;mx[rt]=nt=siz[y];
        find_rt(y,0);find_rt(rt,0);//更新siz
        dep[rt]=dep[id]+1;
        dfz_(rt,id);
    }
}
void gt(ll id)
{
    ll now=id;
    while(id)
    {
        // cout<<id<<' '<<now<<" "<<dis[dep[id]][now]<<endl;
        while(vt_siz[id]>=0 && vt[id][vt_siz[id]].first>=dis[dep[id]][now])//虚树中now到id的距离 
        {                          //因为now必然在id的子树下面，所以其它与id同高的点的dep不会将dis[dep][id]更新掉 
            ll y=vt[id][vt_siz[id]].second;
            vt_siz[id]--;
            if(rvis[y]) continue;
            rvis[y]=1;
            tim[y]=tim[now]+1;

            q.push(y);
        }
        id=F[id];
    }
}
void init(ll n)
{
    cn=0;
    for(int i=0;i<=n+5;++i) head[i]=-1,siz[i]=0,mx[i]=0,dep[i]=0,R[i]=0,F[i]=0,vt[i].clear();
    rt=0,nt=0,cur=0;
    for(int i=0;i<=19;++i) for(int j=0;j<=n;++j) dis[i][j]=0;
    for(int i=0;i<=n+5;++i) tim[i]=inf,rvis[i]=0,vis[i]=0;
    while(!q.empty()) q.pop();
}
void solve()
{
    read(n);init(n);
    for(int i=2;i<=n;++i) read(R[i]);
    for(int i=1;i<n;++i)
    {
        read(a);read(b);read(c);
        add(a,b,c);
        add(b,a,c);
    }
    mx[0]=nt=n;
    find_rt(1,0);
    find_rt(rt,0);//更新siz数组，因为现在是以一个新的点为根节点
    dep[rt]=0;
    dfz_(rt,0);
    tim[1]=0,rvis[1]=1;
    for(int i=1;i<=n;++i) vt_siz[i]=vt[i].size()-1;
    q.push(1);
    while(!q.empty())
    {
        ll fr=q.front();
        q.pop();
        gt(fr);
    }
    for(int i=2;i<=n;++i)
    {
        cout<<((tim[i]==inf)?-1:tim[i])<<" ";
    }
    cout<<endl;
}
int main()
{
    ll t;cin>>t;while(t--)
    solve();
}
```



## 李超线段树

$O(nlon)$统计某一点最大/最小值线段对应的值

该模板为统计最大值

```c++
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

```c++
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

```c++
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

## 费马-欧拉定理

n,a为整数，若满足n，a互质，则：$a^{\phi(n)}\equiv1(mod n)$

Proof ：1~n中与n互质的数字有$\phi(n)$个,记为$x_1,x_2...x_{\phi{n}}$，考虑$m_i=a*x_i$

​		不难得到以下结论：

   * 任意$m_i$之间两两互质

   * $gcd(mi,n)=1$

     所以$m_i$与$x_i$之间存在一一对应的同余关系

     所以：$a^{\phi(n)}*x_1*x_2*...*x_{\phi(n)}\equiv x_1*x_2*...x_{\phi(n)}(mod n)$

     化简得到上述定理 

     $Q.E.D.$

推广：

* $a*a^{\phi(n)-1}\equiv1(mod n)$

​				**所以若a与n互质，则a关于n的乘法逆元就是$a^{\phi(n)-1}$**

* 特别的，当n为质数时，我们就得到了费马小定理：a,n互质且n为质数时，$a^{n-1}\equiv 1(mod n)$

### 拓展欧拉定理

$b\leq \phi(m)$的时候,$\large a^{b}\equiv a^{(b mod \phi(m))+\phi(m)}\pmod m$,这里无需a，m互质

### 欧拉定理推论

$\large a^{b}\equiv a^{(b mod \phi(m))}\pmod m$,这里要求a,m互质，这也是与前者的区别

<font color='orange'>上述两个结论常用于降幂</font>

## 拓展欧几里得(解方程&求Gcd)

```c++
int Exgcd(int a,int b,int &x,int &y){
    if(!b){
        x=1,y=0;
        return a;
    }int d=Exgcd(b,a%b,x,y),t=x;
    x=y;y=t-(a/b)*y;
    return d;
}
```

```c++
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

方程$ax+by=c$的某个解$(x_0,y_0)$，则方程的通解为$(x_0+kb',y_0-ka')$,其中$b'=\frac{b}{(a,b)},a'=\frac{a}{(a,b)}$

```c++
ll gt(ll a,ll b,ll c)
{
    //求解满足方程组ax+by=c的最小非负数解x(若要正整数解在最后特判x=0即可)
    //无解返回-1
    ll x,y;
    ll Gcd=exgcd(a,b,x,y);
    if(c%Gcd) return -1;
    x=x*(c/Gcd);
    ll mo=b/Gcd;
    x=(x%mo+mo)%mo;
    return x;
    //x为特解，通解为(x+kb',y-ka'),b'=b/(a,b),a'=a/(a,b)
}
```

### Stein算法

欧几里得算法的缺陷在于，大整数对大整数的取模比较困难并且复杂度较高，而Stein算法避免了这个问题。

流程：如果a,b都是偶数，则先同时/2并且给gcd乘2，否则如果有一个偶数则给它/2，否则gcd(a,b)=gcd(b,a-b)

注意到每次减完之后都会/2，复杂度也是log级别的

## 离散对数问题

### BSGS

求解方程$a^x \equiv b\pmod p$,其中p为质数

设$x=im-j$,其中$m=ceil(sqrt(p*1.0)),j\in [0,m),i\in [1,m]$,于是原方程变成了$a^{im-j}\equiv b \pmod p$，也就是$a^{im}\equiv a^jb \pmod p$

于是我们可以枚举j，并将$a^jbmod p$的答案记录在map中。map中的key存的是答案，value存的是j。

然后我们再枚举i，在map中查找$a^{im} modp$,如果查询到了，那么查询到的即为$j$，那么$x=im-j$即为答案。

这样做限定了x在$[0,p]$的范围内。但是因为$\large a^{x}\equiv a^{xmod(p-1)} \pmod p$,通解就是$x+k(p-1)$，所以只要在$[0,p]$内求出解即可，求不出来就代表无解

```c++
ll bsgs(ll a, ll b, ll p)
{
    //返回值为[0,p]的特解x，通解为x+k(p-1),即a^{x} = a^{x%(p-1)} (mod p)
    map<ll,ll> mp;
    //a^x = b (mod p)
    a %= p, b %= p;
    if(!a && !b) return 1;
    if(!a || !b) return -1;
    mp.clear();
    ll m = ceil(sqrt(p*1.0)), tmp = 1;//上取整
    mp[b] = 0;//(a^0)*b=b
    for(int j = 1; j <= m; j++)
    {
        tmp = tmp*a % p;
        if(!mp[tmp*b%p]) mp[tmp*b%p] = j;
    }
    ll t = 1, ans;
    for(int i = 1; i <= m; i++)
    {
        t = t*tmp%p;
        if(mp[t])
        {
            ans = i*m-mp[t];
            return (ans%p+p)%p;
        }
    }
    return -1;
}
```

时间复杂度$O(sqrt(p))$

### pohlig-hellman



## 线性求逆元：

这里有一个线性时间复杂度的做法，可以求出1~n关于p的逆元。显然此时p与1~n的所有数都是互质关系

显然1的逆元为1.

考虑后面的数i，p可以表示为$k*i+j$ ，其中$k=\left \lfloor p/i \right \rfloor,j=pmod i$

我们不妨写成这样的形式：$k*i+j\equiv 0(modp)$

两边同时乘以$i^{-1}*j^{-1}$,得到：$k*j^{-1}+i^{-1}\equiv 0(mod p)$

化简得到：$i^{-1}\equiv -\left \lfloor p/i \right \rfloor*(pmod i)^{-1}(mod p)$ 

当然还有一个负号有点碍眼，我们可以将其处理掉：$-\left \lfloor p/i \right \rfloor\equiv (p-\left \lfloor p/i \right \rfloor)(mod p)$

显然$pmod i<i$,所以如果我们按从小到大的顺序求逆元的话，这是之前已经求好的值，所以该方法是合理的。

code：

```c++
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

```c++
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

```
ll work(ll n, ll m) {//Lucas 定理
	if (!n) return 1;
	return (work(n / p, m / p) * C(n % p, m % p)) % p;
}
```



## 组合数学小常识

### 范德蒙德卷积

$\sum_{i=0}^{k}\binom{n}{i}\binom{m}{k-i}=\binom{n+m}{k}$

####  推论

$\sum_{i=0}^{n}\binom{n}{i}\binom{n}{i-1}=\binom{2n}{n+1}$

$\sum_{i=0}^{n}\binom{n}{i}^2=\binom{2n}{n}$

$\sum_{i=0}^{m}\binom{n}{i}\binom{m}{i}=\binom{n+m}{m}$

### 小球盒子

小球数字为n，盒子数为m

* 不同小球，不同盒子

  有空盒：$m^n$

  无空盒：$m!S(n,m)$ 第二类斯特林数乘上对应排列

* 不同小球，相同盒子

  有空盒：$\sum_{k=1}^{min(n,m)}S(n,k)$

  无空盒：$S(n,m)$

* 相同小球，不同盒子

  有空盒:$\frac{n+m-1}{n}$

  无空盒:$\frac{n-1}{m-1}$

* 相同小球，相同盒子：

  有空盒：$\large \sum_{i=1}^{m}P_i(n)=\frac{1}{\prod_{i=1}^{m}(1-x^i)}[x^n]$

  无空盒：$\large p_m(n)=\frac{1}{\prod_{i=1}^{m}(1-x^i)}[x^{n-m}]$

###  二项式定理及拓展

$(a+b)^{n}=\sum_{i=0}^{n}C(n,i)a^{n-i}b{i}$

$(a+b)^{\alpha}=\sum_{i=0}^{\inf} C(\alpha,i)a^{\alpha-i}b^{i}$

$(a+b)^{-\alpha}=\sum_{i=0}^{inf}(-1)^iC(\alpha+i-1,i)a^{\alpha-i}b^{i}$

### 小公式

$\sum_{i=0}^{n}iC(n,i)=n2^{n-1}$

$\sum_{i=0}^{n}C(x+i,x)=C(n+x+1,n)$

### 斐波那契

$gcd(F_i,F_j)=F_{gcd(i,j)}$

$\sum_{i=0}^{n}F_i=F_{n+2}-1$

$\sum_{i=0}^{n}F_{i}^2=F_nF_{n+1}$

$\sum_{i=0}^{n}F_{2i-1}=F_{2n}$

$F_n=F_mF_{n-m+1}+F_{m-1}F_{n-m},n>=m$

$F_n^2+(-1)^n=F_{n-1}F_{n+1}$

$F_{2n}=C(2n,0)+C(2n-1,1)+...+C(n,n),F_{2n+1}=C(2n+1,0)+..+C(n+1,n)$

### 卡特兰数

凸多边形三角划分,n个结点的二叉树不同构的个数,一个栈的进栈序列为1，2，3，…，n，不同的出栈序列个数,长度为2n的合法括号序列个数,n层阶梯图切割成n个矩形的方案数,n*n的网格图，不经过对角线从左下角到右下角的方案数

1,1,2,5,14,42,132...

$C_n=\sum_{i=0}^{n-1}C_iC_{n-i-1},n>=2;C_0=C_1=1$

$C_n=C(2n,n)-C(2n,n-1)$

### 斯特林数

#### 第一类斯特林数

n个不同元素构成k个圆排列的方案数

$S_1(n,k)=S_1(n-1,k-1)+(n-1)S_1(n-1,k)$

边界条件 $S_1(n,k)=0(n<k||(n>0,k=0)),S_1(n,k)=1(n=k)$

#### 第二类斯特林数

组合意义：有n个互不相同的球，放到k个互不区分的盒子里面，每一个盒子里至少一个球，球方案数

递推式$S_2(n,k)=S_2(n-1,k-1)+kS_2(n-1,k)=\frac{\sum_{i=0}^{k}(-1)^iC(k,i)(k-i)^n}{k!}$

边界条件 $S_2(n,k)=0(n<k||(n>0,k=0)),S_2(n,k)=1(n=k)$

通项 $S_2(n,k)=\frac{1}{k!}\sum_{i=0}^{k}(-1)^i\binom{k}{i}(k-i)^n$

重要性质：$n^x=\sum_{k=0}^{n}S_2(n,k)x^{\underline{k}}$

$\sum_{i=0}^{n}i^k=\sum_{i=0}^{k}S(k,i)\frac{(n+1)^{\underline{i+1}}}{i+1}$

### 贝尔数

$B_n$表示元素个数为n的集合的分类数，也就是集合上的等价关系的数量

#### 递推公式：

$B_0=1$

$B_{n+1}=\sum_{i=0}^{n}\binom{n}{i}B_i$

考虑在原本的n个元素的集合$A$里再加入一个元素$A_{n+1}$,它有如下几种归宿：

自己单独分一类 $\binom{n}{0}B_n=\binom{n}{n}b_n$

跟某一个元素放一类 $\binom{n}{1}B_{n-1}=\binom{n}{n-1}B_{n-1}$

跟某两个元素放一类 $\binom{n}{2}B_{n-2}=\binom{n}{n-2}B_{n-2}$

...

跟所有n个元素放一类 $\binom{n}{n}B_{0}=\binom{n}{0}B_{0}$

#### 与第二类斯特林数的关系:

每一个贝尔数都是对应的斯特林数的和 $B_n=\sum_{k=0}^{n}\binom{n}{k}$

因为第二类斯特林数的意义就是把集合划分为k类的方案数

#### 贝尔三角形

$a_{0,0}=1$

$\forall n\geq 1$，第n行的的行首等于上一行的行尾

$\forall n,m\geq 1,a_{n,m}=a_{n,m-1}+a_{n-1,m-1}$

| 1    |      |      |      |      |      |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 1    | 2    |      |      |      |      |
| 2    | 3    | 5    |      |      |      |
| 5    | 7    | 10   | 15   |      |      |
| 15   | 20   | 27   | 37   | 52   |      |
| 52   | 67   | 87   | 114  | 151  | 203  |



#### $O(n^2)求贝尔数$

```c++
const int maxn = 2000 + 5;
int bell[maxn][maxn];

void f(int n) {
  bell[0][0] = 1;
  for (int i = 1; i <= n; i++) {
    bell[i][0] = bell[i - 1][i - 1];
    for (int j = 1; j <= i; j++)
      bell[i][j] = bell[i - 1][j - 1] + bell[i][j - 1];
  }
}

```

#### $O(nlog)求法$

记贝尔数的生成函数$\hat{B}(x)=\sum_{n=0}^{\inf}\frac{B_n}{n!}x^n$

则$\hat{B}(x)=exp(e^x-1)$

预处理出$e^x-1$的前n项之后做一次多项式$exp$即可得出贝尔数的前n项，时间复杂度瓶颈在于多项式exp，可以做到$O(nlog)$

## 树与图上的计数问题

### Prufer序列

起源于对$Cayley$定理的证明，但是其功能远不止于此

* $Tree->Prufer:$

  ①从树上选择编号最小的叶子节点，序列的下一位为其父节点的编号。

  ②删去该叶子节点。

  ③重复①和②，直到树只剩下两个节点，此时序列的长度刚好为 n−2 。

* $Prufer—>Tree:$

  ①选择编号最小的叶子节点（即未出现在序列中的节点），其父节点就是序列的第 i （ i 初始为1）个元素。

  ②由性质可得，其父节点的度数为其出现次数+1。将该叶子节点删去，其父节点度数-1。若度数变成1，则父节点也成为叶子节点。

  ③将 i 加一，然后重复①和②，直到序列的每一个元素都使用完毕。

```c++
ll p[N];
ll d[N];//度数
ll f[N];//连边
ll ans=0;
void Prufer_To_Tree(ll n)
{
    //f记录1-n-1的连边情况
    for(int i=1;i<=n-2;++i) cin>>p[i],d[p[i]]++;
    p[n-1]=n;
    for(int i=1,j=1;i<n;++i,++j)
    {
        while(d[j]) j++;
        f[j]=p[i];//把最小的叶往prufer序列第一个点上接 对应减掉度数

        while(i<n&&!--d[p[i]]&&p[i]<j) f[p[i]]=p[i+1],i++;
        //如果序列第一个点减掉度数后产生了新的更小的叶 就往序列下一个点上接
    }
    for(int i=1;i<=n;++i) ans^=1ll*i*f[i];
}
void Tree_To_Prufer(ll n)
{
    for(int i=1;i<n;++i) cin>>f[i],d[f[i]]++;
    for(int i=1,j=1;i<=n-2;++i,++j)
    {
        while(d[j]) j++;
        p[i]=f[j];
        while(i<=n-2&&!--d[p[i]]&&p[i]<j) p[i+1]=f[p[i]],i++;
    }
    for(int i=1;i<=n-2;++i) ans^=1ll*i*p[i];
}
```

由此我们发现两者是一一对应的，也就是双射，所以大小为n的有标号无根树的个数等于长度为$n-2$的prufer序列的个数，自然为$n^{n-2}$，$Cayley$定理得证

#### Prufer序列的性质

由Prufer序列构造的过程，我们可以发现其具有两个显而易见的性质。

* 构造完后剩下的两个节点里，一定有一个是编号最大的节点。

* **对于一个 n 度的节点，其必定在序列中出现 n−1 次**。因为每次删去其子节点它都会出现一次，最后一次则是删除其本身。一次都未出现的是原树的叶子节点。

#### 应用

**1、无向完全图的不同生成树数：**

 $n^{n−2}$ 。

**2、** n **个点的无根树计数：**

同上问题。

**3、** n **个点的有根树计数：**

对每棵无根树来说，每个点都可能是根，故总数为 $n^{n−2}×n=n^{n−1}$ 。

**4、** n **个点，每点度分别为** $d_i$ **的无根树计数：**

显然就是一个多重集，答案为$\frac{(n-2)!}{\prod_{i=1}^{n}d_i-1}$

**5、** **有标号的完全二分图$K_{n,m}$的生成树个数为$n^{m-1}m^{n-1}$:**

考虑将其生成树的prufer序列按照原本顺序分成$f_i\leq n,f_i>n$两部分。

对于$f_i\leq n$的部分，一定是删去某个标号$>n$的点之后留下$f_i$的，因为这是一张二分图。所以该部分的点一定恰好有$m-1$个（右部有m个点，整张图删完之后一定在左右部各留下一个点，所以右部一共要删去$m-1$个点）

$f_i>n$部分同理。

所以一个此时还是可以用一个prufer序列与合法生成树对应，故方案数为$n^{m-1}m^{n-1}$

#### Tips:

一般要特判n=1的情况



[Valuable Forests](https://ac.nowcoder.com/acm/contest/28665/E)

大意：

定义一个树的权值为其所有节点的度数的平方和，森林的权值为所有树的权值和。求大小为n的所有有标号森林的权值和

思路：

$f_i$表示大小为i的所有有标号森林的权值和，也就是答案

考虑对于最后一个点所在的树的大小为k的情况

则$f_n=\sum_{k=1}^{n}\binom{n-1}{k-1}f_{n-k}m_k+\binom{n-1}{k-1}g_kh_{n-k}$

其中$m_i$表示大小为$i$的有标号无根树的个数，$g_i$表示大小为$i$的所有有标号无根树的权值和，$h_{n-k}$表示大小为$n-k$的森林的数量。$\binom{n-1}{k-1}$是因为枚举的k的含义是n所在树的大小，我要从剩下$n-1$个点里面选$k-1$个点。如果不这样枚举的树的组合就会算重。

$m_n$很简单：$n=1$时$m_1=1$，否则$m_n=n^{n-2}$

对于$g_n$,我们枚举所有度数为i的贡献

转化到prufer序列中看这个问题，度数为i，表示出现了$i-1$次，所以强制选定对应的数字以及位置，剩下的$n-1$个数随便放

$g_n=\sum_{i=1}^{n-1}i^2n\binom{n-2}{i-1}{n-1}^{n-1-i}$

$h_n$的更新思路和$f$差不多，枚举最后一个点所在的树的大小即可

$h_n=\sum_{i=1}^{n}\binom{n-1}{i-1}h_{n-i}m_i$

[cf 156D](https://codeforces.com/problemset/problem/156/D)

大意：

给定n个点以及m条连边，记最少添加T条边使得整张图连通，问有多少种恰好添加T条边的方案使得图连通

思路:

记当前连通块个数为k，则T=k-1

对于每一个连通块，其大小为$a_i$，如果其度数为$d_i$的话，我们可以在以连通块为单位的情况下得到生成树个数为$\binom{k-2}{d1-1,d2-1...d_k-1}$

对于每一个连通块，固定连边的标号顺序（比如第1条边来自标号最小的连通块，依次类推），那么此时总方案数为$\binom{k-2}{d1-1,d2-1...d_k-1}*\prod_{i=1}^{k}a_i^{d_i}$,因为每一条边都可以有$a_i$种选择

所以答案为$\sum_{\sum d_i=k-2}\binom{k-2}{d1,d2...d_k}*\prod_{i=1}^{k}a_i^{d_i+1}=\sum_{\sum d_i=k-2}\frac{(k-2)!}{\prod_{i=1}^{k} d_i!}*\prod_{i=1}^{k}a_i^{d_i+1}=(k-2)!\prod_{i=1}^{k} a_i(\sum_{\sum_{i=1}^{k} d_i=k-2}\prod_{i=1}^{k}\frac{a_i^{d_i}}{d_i!})$

注意到$(k-2)!\prod_{i=1}^{k} a_i$是一个常数，我们只用看后面

记生成函数$f_x=\sum_{i=0}^{\inf}\frac{x_i}{i!}=e^x$

不难发现后面其实是k个函数$f_{a_i}$的卷积的某一项,故最终答案为$[x^{k-2}]\prod_{i=1}^{k}e^{a_ix}=[x^{k-2}]e^{nx}=\frac{n^{k-2}}{(k-2)!}$

所以最终答案为$n^{k-2}\prod_{i=1}^{k}a_i$



### LGV引理		

在一个有向无环图G中，出发点$A=\{a_1,a_2,...a_n\}$，目标点$B=\{b_1,b_2,..b_n\}$。有向边$e$的权值为$w_e$，$e(u,v)$为路径边权乘积之和，即$e(u,v)=\sum_{P:a->b}\prod_{e\in P}w_e$

则$\begin{vmatrix}
 e(a_1,b_1)& e(a_1,b_2) & ... & e(a_1,b_n)\\ 
 e(a_2,b_1)& e(a_2,b_1) & ... & e(a_2,b_n)\\ 
 ...& ... &  & ...\\ 
 e(a_n,b_1)& e(a_n,b_2) & ... & e(a_n,b_n)
\end{vmatrix}=\sum_{S:A->B}(-1)^{N(\sigma)}\prod_{i=1}^n\prod_{e\in S_i}w_e$

其中$\sigma$是一个n元置换，$N(\sigma)$表示逆序对个数,$S_i:a_i->b_i$是一组A到B的不相交路径。

**此处的不相交是指处处不存在相同的点在两条路径中同时存在，起终点也不行，所以A里的元素互不相同。如果初始条件并非如此，可能要转化一下**

在算法竞赛中，该引理在普通的DAg下没有过多的用处，因为其右式还是过于复杂。但是如果图满足所有边权为1（这样$e(u,v)\equiv1$,就可以表示路径数量了），并且只存在唯一的一个$\sigma $满足所有$a_i->b_i$不相交的话，此时右式只有一个因子，含义就是整张图的不相交路径组数，就可以用左式的行列式表示了

[Monotonic Matrix](https://ac.nowcoder.com/acm/contest/28665/F)

大意：

$A_1(1,0),A_2(0,1),B_1(n,m+1),B_2(n+1,m)$,求$A_1->B_1,A_2->B_2$的不相交路径数。其中每一步只能向上/向右

套板子即可

```c++
#include <bits/stdc++.h>
#define pii pair<int,int>
#define il inline
#define ll long long
using namespace std;
const int N=2010;
const ll inf=1e18;
const ll mod=1e9+7;
ll n,m;
ll c[N][N];
void init(ll n)
{
    for(int i=0;i<=n;++i)
    {
        for(int j=0;j<=i;++j)
        {
            if(j==0) c[i][j]=1;
            else c[i][j]=(c[i-1][j-1]+c[i-1][j])%mod;
        }
    }
}
ll f(ll x1,ll y1,ll x2,ll y2)
{
    //(x1,y1)->(x2,y2)
    ll len1=x2-x1;
    ll len2=y2-y1;
    return c[len1+len2][len2];
}
void solve()
{
    // cin>>n>>m;
    ll a=f(0,1,n,m+1)*f(1,0,n+1,m)%mod;
    ll b=f(0,1,n+1,m)*f(1,0,n,m+1)%mod;
    cout<<((a-b)%mod+mod)%mod<<endl;
}
int main()
{
//     freopen("1.out","w",stdout);
    ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);
    init(2005);
    while(cin>>n>>m)
    // ll t;cin>>t;while(t--)
    solve();
    return 0;
}

```



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



## 势函数和鞅的停时定理 

### 前置芝士

鞅：

鞅是一类特殊的随机过程，假设我们从一开始就在观察一场赌博游戏，现在已经得到了前t秒的观测值，那么当第t+1 秒观测值的期望等于第t秒的观测值时，我们称这是一个公平赌博游戏。

具体来说，对于一个随机过程${A_1,A_2,...}$,如果$E(A_{n+1}|A_0,A_2,..A_n)=A_n$,我们称该随机过程为鞅。

鞅的停时定理：

设时停时间（在**不知道随机过程的中间状态下**停止的时刻）为t，则$E(t)=E(0)$

这个E到底是什么，由具体的情境而定，但是只要一个随机过程是一个鞅，它就有该结论

### 势函数

接下来我们考虑一个很常见的问题：

对于一个随机过程${A_1,A_2,...}$,如果**其终止状态$A_t$是确定的**，求$E[t]$,即时停时刻的期望（**注意这里我们不要求该随机过程是一个鞅**）

为此，我们引入一个势函数$\phi(X)$

并且$\phi(x)$满足如下性质：

* $\forall n<t, E(\phi(A_{n+1})-\phi(A_n)|A_0,A_1,...A_n)=-1$,即势能不断降低
* $E(\phi(A_t))=C$,是一个常值

那么如果我们令$X_t=\phi(A_t)+t$,则$E(X_{n+1}-X_n|x_0,x_1...x_n)=E(\phi(A_{n+1})-\phi(A_n)+1|x_0,x_1...x_n)=E(\phi(A_{n+1})-\phi(A_n)|x_0,x_1...x_n)+1=0$

我们发现随机过程$X_t$就是一个鞅了

那么由鞅的停时原理，$E(X_t)=E(X_0)$,即$E(\phi(A_t)+t)=E(\phi(A_0)+0)$,也即$E(\phi(A_t))+E(t)=E(\phi(A_0))$

所以我们得到**$E(t)=E(\phi(A_0))-E(\phi(A_t))$**,根据我们之前定义的性质，$E(\phi)A_t$为一个常值，而$E(\phi(A_0))$显然也是一个常值，所以只要能找到这个满足条件的势函数，就能很方便的求出$E(t)$

这里我们只是在随机过程$X_t$中应用了停时定理，对原本的随机过程$A_t$并没有做什么限制

接下来结合具体的题目来讨论一下如何构造这样的一个势函数

[CF1349D](https://codeforces.com/contest/1349/problem/D)

大意：

有n个人在玩传球游戏，一开始第$i$个人有$a_i$个球。每一次传球，等概率随机**选中一个球**，设其当前拥有者为$i$，$i$将这个球等概率随机传给另一个人$j(j\neq i)$。当某一个人拥有所有球时，停止游戏。问游戏停止时的期望传球次数。

记球的总数为m

不妨记状态$A_t=(a_{t,1},a_{t,2}...a_{t,n})$,一个n维向量，分别表示 在时刻t，第i个人手中球的数量，显然它唯一地表示了某一个时刻的全局状态

也就是说，我们现在就把这个游戏过程抽象成了一个随机过程$A_0,A_1....$,并且其停时为t。那么按照之前所说，我们需要去定义一个势函数$\phi(A_t)$,为了计算方便，我们可以将$\phi$具体到A的每一维向量，不妨记为$\phi(A_t)=\sum_{i=1}^{n}f(a_{t,i})$,这里f是什么我们并不知道，但是如果我们知道了f，其实也就是相当于构造出了这个势能函数

这里再把我们定义的$\phi$的性质再放一下

---

$\phi(x)$满足如下性质：

* $\forall n<t, E(\phi(A_{n+1})-\phi(A_n)|A_0,A_1,...A_n)=-1$,即势能不断降低
* $E(\phi(A_t))=C$,是一个常值

---

那么我们首先来考虑第一个性质，为了方便，不妨先考虑$E(\phi(A_{n+1})|A_0,A_1,...A_n)$

发现传球过程就是一个$Markov$过程，并且该时刻的状态只与上一个时刻的状态有关,所以$E(\phi(A_{n+1})|A_0,A_1,...A_n)=E(\phi(A_{n+1})|A_n)$

考虑一次转移的所有可能

i传球给j的概率是$\Large \frac{a_{t,i}}{m}\frac{1}{n-1}$

$\large E(\phi(A_{n+1})|A_n)=\sum_{i=1}^{n}\sum_{j\neq i}\frac{a_{t,i}}{m}\frac{1}{n-1}[f(a_{t,i}-1)+f(a_{t,j}+1)+\sum_{k\notin(i,j)}f(a_{t,k})]$

$\large =\sum_{i=1}^{n}\frac{a_{t,i}}{m}f(a_{t,i}-1)+\frac{m-a_{t,i}}{m(n-1)}f(a_{t,i}+1)+\frac{(m-a_{t,i})(n-2)}{m(n-1)}f(a_{t,i})$

根据我们定义的性质$E(\phi(A_{n+1})-\phi(A_n)|A_0,A_1,...A_n)=-1$

$E(\phi(A_{n+1})-\phi(A_n)|A_0,A_1,...A_n)=E(\phi(A_{n+1})-\phi(A_n)|A_n)$

$\large =(\sum_{i=1}^{n}\frac{a_{t,i}}{m}f(a_{t,i}-1)+\frac{m-a_{t,i}}{m(n-1)}f(a_{t,i}+1)+\frac{(m-a_{t,i})(n-2)}{m(n-1)}f(a_{t,i}))-\sum f(a_{t,i})=-1$

所以$\large \sum f(a_{t,i})=(\sum_{i=1}^{n}\frac{a_{t,i}}{m}f(a_{t,i}-1)+\frac{m-a_{t,i}}{m(n-1)}f(a_{t,i}+1)+\frac{(m-a_{t,i})(n-2)}{m(n-1)}f(a_{t,i}))+1$

那么我们可以把末尾的1分配到每一个和式里面去，这样左右的形式就统一了

所以$\large \sum f(a_{t,i})=\sum_{i=1}^{n}[\frac{a_{t,i}}{m}f(a_{t,i}-1)+\frac{m-a_{t,i}}{m(n-1)}f(a_{t,i}+1)+\frac{(m-a_{t,i})(n-2)}{m(n-1)}f(a_{t,i})+\frac{a_{t,i}}{m}]$

那么不妨记$\large f(a)=\frac{a}{m}f(a-1)+\frac{m-a}{m(n-1)}f(a+1)+\frac{(m-a)(n-2)}{m(n-1)}f(a)+\frac{a}{m}$

这样和式还是成立的，我们也成功抽象出了f函数

再转化一下，$\large f(a+1)=\frac{m+an-2a}{m-a}f(a)-\frac{a(n-1)}{m-a}(f(a-1)+1)$

代入边界条件$a=0$时，有$f(1)=f(0)$,所以我们可以设$f(1)=f(0)=0$,毕竟势函数的初值并不重要

这样就得到了f，也就是相当于得到了势函数$\phi(x_t)=\sum_{i}f(x_{t,i})$

然后考虑势函数的第二个性质：$E(\phi(A_t))=C$是一个常值

显然$E(\phi(A_t))=\sum_{i}f(a_{t,i})=f(m)+(n-1)f(0)$是一个常值

所以根据我们的结论，$E(t)=E(\phi(A_0))-E(\phi(A_t))=\sum_{i}f(a_{0,i})-f(m)-(n-1)f(0)=\sum_{i}f(a_{0,i})-f(m)$

这样我们就非常方便的得到了停时的期望

不妨来看一个近一点的例子

[杭电多校09 Coins](http://acm.hdu.edu.cn/contest/problem?cid=1102&pid=1008)

大意：

n个人，每个人手中初始有$a_i$个硬币，每次随机选择两个人，第一个人给第二个人一个硬币，如果某个人手中没有硬币了，则立即退出游戏，不再回来。当某一个人拥有全部硬币时，游戏结束

问停时的期望

题意与上一题十分相像，但是该题存在人数不固定的情况，所以我们描述游戏局面的时候要稍微改变一下

还是令$m=\sum a_i$

令$A_t=(a_{t,1},a_{t,2}...a_{t,h_t})$来描述第t个时刻的局面，其中$h_t$表示当前的剩余人数，显然它不是一个固定的值。但是我们能保证$\forall i\leq h_t,a_{t,i}>0$

仿照上一题的思路，我们令$\phi(A_t)=\sum_{i=1}^{n}f(a_{t,i})$作为势函数，尝试确定f

$\large E(\phi(A_{n+1})|A_n)=\sum_{i=1}^{h_t}\sum_{j\neq i}\frac{1}{h_t(h_t-1)}[f(a_{t,i}-1)+f(a_{t,j}+1)+\sum_{k\notin(i,j)}f(a_{t,k})]$

$\large =\sum_{i=1}^{h_t}\frac{1}{h_t}f(a_{t,i}-1)+\frac{1}{h_t}f(a_{t,i}+1)+\frac{h_t}{h_t-2}f(a_{t,i})$

代入$\large E(\phi(A_{n+1})-\phi(A_n)|A_0,A_1,...A_n)=-1$,也就是$\large E(\phi(A_{n+1})-\phi(A_n)|A_n)=-1$(显然这里当前局面也只与上一个局面有关)，有

$\large \sum_{i=1}^{h_t} f(a_{t,i})=[\sum_{i=1}^{h_t}\frac{1}{h_t}f(a_{t,i}-1)+\frac{1}{h_t}f(a_{t,i}+1)+\frac{h_t}{h_t-2}f(a_{t,i})]+1$

$\large =\sum_{i=1}^{h_t}(\frac{1}{h_t}f(a_{t,i}-1)+\frac{1}{h_t}f(a_{t,i}+1)+\frac{h_t}{h_t-2}f(a_{t,i})+\frac{1}{h_t})$

抽象出$\large f(a)=\frac{1}{h}f(a-1)+\frac{1}{h}f(a+1)+\frac{h}{h-2}f(a)+\frac{1}{h}$

$f(a+1)-f(a)=f(a)-f(a-1)-1$

令$g(a)=f(a)-f(a-1),有g(a)=g(0)-a$，则$f(a)=f(0)+ag(0)-\frac{a(a+1)}{2}$

取$f(0)=g(0)=0$,则$f(a)=-\frac{a(a+1)}{2}$

所以$E(t)=E(\phi(A_0))-E(\phi(A_t))=\sum_{i=1}^{n}f(a_{0,i})-f(m)$

未完待续





















## 竞赛图

竞赛图(tournament)：有 $n^2$条边的有向图



### 性质：

- 竞赛图强连通缩点后的 DAG 是一条链
- 竞赛图的强连通块存在一条哈密顿回路
- 竞赛图存在一条哈密顿路径
- 竞赛图 $size>1$的强连通块中，大小为 $[3,size]$简单环均存在
- 兰道定理（竞赛图判定定理）：定义一个竞赛图的比分序列是把竞赛图每个点的出度从小到大排列得到的序列。一个长 度为 n的序列 s1≤s2≤s3≤…≤sn 是合法比分序列当且仅当 ∀i∈[1,n],$\sum_{j=1}^{i}s_j>=C(i,2)$，且在 i=n时取等号，[证明](https://blog.csdn.net/a_crazy_czy/article/details/73611366) 非常巧妙！

### 计数：

- n阶竞赛图个数显然就是$h_n=2^{C(n,2)}$啦

- 强连通 n 阶竞赛图个数为$f_n=h_n-\sum_{i=1}^{n-1}f_iC(n,i)h_{n-i}$ ，就枚举一个 SCC 连的都是出边。

- 生成函数加速：
  令 f0=0

  有：$h_n=\sum_{i=0}^{n}f_iC(n,i)h_{n-i}+[n==0]$

  H=FH+1⇒F=1−H−1

## 图论小结论

```c++
二分图：
一、二分图最大匹配
       定义：匹配是图中一些边的集合，且集合中任意两条边都没有公共点，所有的匹配中，边数最多的就是最大匹配。

算法：匈牙利算法


二、二分图最小点覆盖
       定义：点覆盖是图中一些点的集合，且对于图中所有的边，至少有一个端点属于点覆盖，点数最小的覆盖就是最小点覆盖。
       定理：最小点覆盖=最大匹配。最大匹配数=最小顶点覆盖数；（最小顶点覆盖=最大匹配（双向图）/2）

三、二分图最小边覆盖
       定义：边覆盖是图中一些边的集合，且对于图中所有的点，至少有一条集合中的边与其相关联，边数最小的覆盖就是最小边覆盖。
       定理：最小边覆盖=图中点的个数-最大匹配。
       简单证明：先贪心的选一组最大匹配的边加入集合，对于剩下的每个未匹配的点，随便选一条与之关联的边加入集合，得到的集合就是最小边覆盖，所以有：最小边覆盖=最大匹配+图中点的个数-2*最大匹配=图中点的个数-最大匹配。

四、二分图最大点独立集
       定义：独立集是图中一些点的集合，且图中任意两点之间都不存在边，点数最大的就是最大独立集。
       定理：最大独立集=图中点的个数-最大匹配。
       简单证明：可以这样来理解，先把所有的点都加入集合，删除最少的点和与其关联的边使得剩下的点相互之间不存在边，我们就得到了最大独立集。所以有：最大独立集=图中点的个数-最小点覆盖=图中点的个数-最大匹配。

五、有向无环图最小不相交路径覆盖
       定义：用最少的不相交路径覆盖所有顶点。
       定理：把原图中的每个点V拆成Vx和Vy，如果有一条有向边A->B，那么就加边Ax-By。这样就得到了一个二分图，最小路径覆盖=原图的节点数-新图最大匹配。
       简单证明：一开始每个点都独立的为一条路径，总共有n条不相交路径。我们每次在二分图里加一条边就相当于把两条路径合成了一条路径，因为路径之间不能有公共点，所以加的边之间也不能有公共点，这就是匹配的定义。所以有：最小路径覆盖=原图的节点数-新图最大匹配。

六、有向无环图最小可相交路径覆盖
       定义：用最小的可相交路径覆盖所有顶点。
       算法：先用floyd求出原图的传递闭包，即如果a到b有路，那么就加边a->b。然后就转化成了最小不相交路径覆盖问题。

七、偏序集的最大反链链：
       链：一条链上任意两个点x, y，满足要么 x 能到达 y ，要么 y 能到达 x 。
       反链：一条反链是一些点的集合，链上任意两个点x, y，满足 x 不能到达 y，且 y 也不能到达 x。
       定义：偏序集中的最大独立集。
       Dilworth定理：对于任意偏序集都有，最大独立集（最大反链）=最小链的划分（最小可相交路径覆盖）。
       通过Dilworth定理，我们就可以把偏序集的最大独立集问题转化为最小可相交路径覆盖问题了。

八、二分图带权最大匹配
       定义：每个边都有一组权值，边权之和最大的匹配就是带权最大匹配。
       算法：KM算法，复杂度为O（V^3）。具体就不说了，网上有不少资料。
       要注意的是，KM算法求的是最佳匹配，即在匹配是完备的基础上权值之和最大。这和带权最大匹配是不一样的，不过我们可以加入若干条边权为0的边使得KM求出来的最佳匹配等于最大权匹配。具体实现的时候最好用矩阵来存图，因为一般点的个数都是10^2级别，并且这样默认任意两点之间都存在边权为0的边，写起来很方便。如果要求最小权匹配，我们可以用一个很大数减去每条边的边权。
```



## 求生成树个数（矩阵树定理）

给n个点m条边的图，求该图的**生成树个数**

基尔霍夫矩阵：求生成树个数（这个不是求最小生成树个数的！）使用的数据范围较小，因为是用邻接矩阵存的图

设基尔霍夫矩阵为G

$G_{i,j}=degree(i) (i=j)$

$G_{i,j}=-cnt(i,j)(i\neq j)$

Matrix-Tree定理此定理利用图的Kirchhoff矩阵,可以在**O(N3)**时间内求出生成树的个数;

```c++
LL K[N][N];
LL gauss(int n){//求矩阵K的n-1阶顺序主子式
    LL res=1;
    for(int i=1;i<=n-1;i++){//枚举主对角线上第i个元素
        for(int j=i+1;j<=n-1;j++){//枚举剩下的行
            while(K[j][i]){//辗转相除
                int t=K[i][i]/K[j][i];
                for(int k=i;k<=n-1;k++)//转为倒三角
                    K[i][k]=(K[i][k]-t*K[j][k]+MOD)%MOD;
                swap(K[i],K[j]);//交换i、j两行
                res=-res;//取负
            }
        }
        res=(res*K[i][i])%MOD;
    }
    return (res+MOD)%MOD;
}
int main(){
    int n,m;
    scanf("%d%d",&n,&m);
    memset(K,0,sizeof(K));
    for(int i=1;i<=m;i++){
        int x,y;
        scanf("%d%d",&x,&y);
        K[x][x]++;
        K[y][y]++;
        K[x][y]--;
        K[y][x]--;
    }
    printf("%lld\n",gauss(n));
    return 0;
}
```

## 二分图匹配

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const ll N=101000;
#define endl '\n'
const ll inf=1<<30;
struct ty{
	ll t,next;
}edge[N<<1];
ll head[N];
ll cnt=0;
void add(ll a,ll b)
{
	edge[++cnt].t=b;
	edge[cnt].next=head[a];
	head[a]=cnt;
}
ll n,m,e;
ll a,b;
ll ti=0;//时间戳 
ll ans=0;
ll df[N];//被匹配的时间 
ll match[N];//匹配对象 
bool dfs(ll u,ll tim)
{
	for(int i=head[u];i!=-1;i=edge[i].next)
	{
		ll y=edge[i].t;
		if(df[y]==tim) continue;
		df[y]=tim;
		if(!match[y]/*还没配对，直接拉走*/||dfs(match[y],tim))
		{
			match[y]=u;
			return 1; 
		}
	}
	return 0;
}
int main()
{
	cin>>n>>m>>e;
	memset(head,-1,sizeof head);
	while(e--)
	{
	    cin>>a>>b;
		add(a,b);	
	} 
	for(int i=1;i<=n;++i)
	{
		if(dfs(i,++ti)) ans++;
	}
	cout<<ans<<endl;
	return 0;
}
```



## Tarjan缩点

```c++
vector<ll> vt[N];//存路径
ll dfn[N];
ll vis[N];
ll low[N];
ll num[N];
ll color[N];
ll st[N];
/*
	sig 颜色总数
	num[i] 颜色为i的点数
	color[i] 点i的颜色
*/
ll du[N];
void init()
{
	memset(vis,0,sizeof vis);
	memset(dfn,0,sizeof dfn);
	memset(low,0,sizeof low);
}
void Tarjan(int x)
{
	dfn[x]=++cnt;
	low[x]=cnt;
	st[++top]=x;
	vis[x]=1;
	for(int i=0;i<vt[x].size();++i)
	{
		int y=vt[x][i];
		if(!dfn[y])
		{
			Tarjan(y);
			low[x]=min(low[x],low[y]);
		}
		else if(vis[y])low[x]=min(low[x],dfn[y]);
	}
	if(dfn[x]==low[x])
	{
		int t=0;
		++sig;
		do
		{
			t=st[top--];
			vis[t]=0;
			color[t]=sig;
			num[sig]++;
		}while(t!=x);
	}
}
void Solve()
{
    cnt=0;sig=0;
    for(int i=1;i<=n;i++)
    {
        if(dfn[i]==0)
        {
            Tarjan(i);
        }
    }
    for(int i=1;i<=n;++i)
    {
    	for(ll j:vt[i])
    	{
    		if(color[i]!=color[j]) add(color[i],color[j],1);
		}
	}
}
```



## KM

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define endl '\n'
const int INF=0x3f3f3f3f;
const int N=305;

int n,m,match[N],pre[N];
bool vis[N];
int favor[N][N];
int val1[N],val2[N],slack[N];

void bfs(int p)
{
    memset(pre,0,sizeof pre);
    memset(slack,INF,sizeof slack);
    
    match[0]=p;
    
    int x=0,nex=0;
    do{
        vis[x]=true;
        
        int y=match[x],d=INF;
        
        for(int i=1;i<=m;i++)
        {
            if(!vis[i])
            {
                if(slack[i]>val1[y]+val2[i]-favor[y][i])
                {
                    slack[i]=val1[y]+val2[i]-favor[y][i];
                    pre[i]=x;
                }
                if(slack[i]<d)
                {
                    d=slack[i];
                    nex=i;
                }
            }
        }
        
        for(int i=0;i<=m;i++)
        {
            if(vis[i])
                val1[match[i]]-=d,val2[i]+=d;
            else
                slack[i]-=d;
        }
        
        x=nex;
        
    }while(match[x]);
    

    while(x)
    {
        match[x]=match[pre[x]];
        x=pre[x];
    }
}

int KM()
{
    memset(match,0,sizeof match);
    memset(val1,0,sizeof val1);
    memset(val2,0,sizeof val2);
    for(int i=1;i<=n;i++)
    {
        memset(vis,false,sizeof vis);
        bfs(i);
    }
    int res=0;
    for(int i=1;i<=m;i++)
        res+=favor[match[i]][i];
    return res;
}

int main()
{
	// Input favor[i][j]!!!
	while(scanf("%d",&n)!=EOF)
	{
//		m=n;
//		for(int i=1;i<=n;++i){
//			for(int j=1;j<=m;++j)
//			{
//				scanf("%d",&favor[i][j]);
//			}
//		}
		printf("%d\n",KM());
	}
	
	return 0;
}
```



## 差分约束

![image-20230818193926689](C:\Users\acm-673\AppData\Roaming\Typora\typora-user-images\image-20230818193926689.png)



## 数位dp

[数位dp](https://so.csdn.net/so/search?q=数位dp&spm=1001.2101.3001.7020)基本上是处理位数相关的问题，不过也不一定

大部分题目存在一定套路，但是有一些也不好想

其dp状态的设置大致是由枚举的位数，以及每一位的状态（是否前导0，是否顶到上界），以及一些依题目而定的性质

时间复杂度基本上就是dp枚举的状态数

例子：

大意：给出两个数a,b，求出[a,b]中各位数字之和能整除原数的数的个数，a,b<=1e18

思路：

考虑dp状态dp[i][j][k]表示枚举到前i位，前i位数字的和是j，前i位组成的数字是k，这是一个比较niave的想法，但是数据范围不支持我们这样处理第三维。考虑到最后只要求整除，所以可以考虑用k%j来代替k。但是这样还涉及到一个问题，就是如果j是不断变化的，就很难实现状态转移。所以我们可以在外层枚举j，然后dfs的时候就保持j不变就好了

由于这里是考虑每一位数字的和，所以我们不用考虑前导0的问题

```c++
ll a[20];
ll cnt=0;
ll dp[20][200][200];
ll dfs(ll x,ll sum,ll rel,ll op)
{
	if(x==0) return sum==0&&rel==0;
	if(!op&&dp[x][rel][sum]!=-1) return dp[x][rel][sum]; 
	ll lim=op?a[x]:9;
	ll tot=0;
	for(int i=0;i<=lim&&i<=sum;++i)
	{	
		tot+=dfs(x-1,sum-i,(rel*10%mod+i)%mod,op&&i==lim);	
	}
	if(!op) dp[x][rel][sum]=tot;
	return tot;
}
ll f(ll x)
{
	cnt=0;
	while(x)
	{
		a[++cnt]=x%10;
		x/=10;
	}
	ll det=0;
	for(int i=1;i<=9*cnt;++i)
	{
		mod=i;
		memset(dp,-1,sizeof dp);
		det+=dfs(cnt,i,0,1);
	}
	return det;
}
```

大意：

给定K,L,R，求L~R之间最多不包含超过K种数码的数的和。K<=10，L,R<=1e18

思路：
如果只是求满足条件的数字的个数的话就是上面提到的板子题了，这里要求和，我们同样可以考虑对相同状态进行合并求和。f[i][j]表示当前枚举到第i位，出现过的数码种类的状态为j，也就是我们要求的答案数组。g[i][j]表示当前枚举到第i位，出出现过的数码种类的状态为j的合法数字个数，也就是板子。考虑如何用g来推f。这里j可以10位二进制状压，我们每往下走一位，如果我们枚举第i位上填的数字是t，用j'表示下一位的数码种类数

$g_{i,j}=\sum g_{i-1,j'},f_{i,j}=\sum 10^itg_{i-1,j'}+f_{i-1,j'}$

f是数字的和，g是合法数字的个数

有了这个之后，我们就直接推就可以了。然后因为需要下一个状态的f和g，所以我们dfs要返回两个值

```c++
ll a[20];
ll cnt=0;
pii dp[40][1030];
ll p[40];
void init()
{
	p[0]=1;
	for(int i=1;i<=20;++i) p[i]=p[i-1]*10ll%mod;
}
bool check(ll x)
{
	ll cn=0;
	while(x)
	{
		cn+=(x%2);
		x/=2;
	}
	return cn<=k;
}
pii dfs(ll x,ll sum,ll head,ll op)
{
	if(x==0) return mk(0,1);
	if(!op&&!head&&dp[x][sum]!=mk(-1ll,-1ll)) return dp[x][sum];
	ll lim=op?a[x]:9;
	ll s1=0,s2=0;
	for(ll i=0;i<=lim;++i)
	{
		//f是数字的和，g是合法数字的个数
		pii gt=mk(0,0);
		if(head&&i==0) gt=dfs(x-1,0,1,op&&i==lim);
		else if(check(sum|(1<<i))) gt=dfs(x-1,sum|(1<<i),0,op&&i==lim);
		s1=(((s1+i*p[x-1]%mod*gt.second%mod)%mod)+gt.first)%mod;	
		s2=(s2+gt.second)%mod;
	} 
	if(!op&&!head) dp[x][sum]=mk(s1,s2);
	return mk(s1,s2);
}
ll f(ll x)
{
	cnt=0;
	while(x)
	{
		a[++cnt]=x%10;
		x/=10;
	}
	return dfs(cnt,0,1,1).first;
}
void solve()
{
	init();
	for(int i=0;i<=20;++i)
	{
		for(int j=0;j<=1025;++j)
		{
			dp[i][j]=mk(-1ll,-1ll);
		}
	}
	cin>>n>>m>>k;
	cout<<((f(m)-f(n-1))%mod+mod)%mod<<endl;
}
```

最后再提一嘴，有些时候会遇到题目说每一个数字是按位排序过的，也就是同一个数字内数位升序排列，然后要求处理相关问题。比如CF908G New Year and Original Order，[SDOI2010]代码拍卖会，这种题有一个不常见但是很关键的套路，就是将每一个数字拆分成若干个由1组成的数字的和，这个性质是由其数位升序带来的，做题的时候要留一个心眼。

---

大意：
一个数字是好的，当且仅当对于x的每一个非0位的数字y，y|x

问区间美丽数字的个数

思路：

 我们很难在枚举的过程中将dp状态设置为与x模2-9的值都有关，因为这样不好合并相同状态。

这里有两个小结论

* 1.两个整数a,b满足a|x,b|x的充要条件是lcm(a,b)|x

  Proof：

  ->:由a|x,b|x知，x=m*a=n*b，则我们有a|(n*b)，若(a,b)=1,则由上述结论我们有a|n,故x=n*b=(k*a)*b=k*lcm(a,b),即lcm(a,b)|x     ----------1*

  若(a,b)=d,则d*(a/d)=n*d*(b/d),即(a/d)=n*(b/d)，其中(a/d,b/d)=1，转为推导1故该方向得证

  <-:显然

  **推广一下就是：若干个数ai都整除x的充要条件是lcm{ai}|x**

* 2.考虑n个数a1,a2...an,记其LCM为L，则x%ai=(x%L)%ai

证明显然 

有了上述性质，我们不难发现，可以用x%2520(1-9的最小公倍数)来代替x的值，那么首先值域就已经大大简化了。然后由性质1，我们只需要维护出现过数字的lcm即可，最后的判断条件就是前缀%2520的值%lcm=0

那么我们记录dpi,j,k表示前i位，前缀模2520的值为j，前i位中非0位的lcm为k，就可以dp了。但是空间有点大，考虑优化。

不难发现，第三维其实并没有很多数字，2-9中任意若干个数字的lcm一定是2520的因数，而2520的因数总共只有48个，就大大简化了空间，并且我们可以预处理

```c++

ll a[22];
ll cnt=0;
int mp[2522];
ll dp[22][2522][52];
int Gt[2522][2522];
ll cm(ll x,ll y)
{
	if(x==0||y==0) return x|y;
	ll Gcd=__gcd(x,y);
	if(x>=y) swap(x,y);
	if(Gt[x][y]) return Gt[x][y]; 
	return Gt[x][y]=x*y/Gcd;
}
ll dfs(ll x,ll op,ll mo,ll Lcm)
{
	if(x==0) return mo%Lcm==0;
	if(!op&&dp[x][mo][mp[Lcm]]!=-1) return dp[x][mo][mp[Lcm]];
	ll tot=0;
	ll lim=op?a[x]:9;
	for(int i=0;i<=lim;++i)
	{
		if(i==0) tot+=dfs(x-1,op&&i==lim,mo*10%mod,Lcm);
		else tot+=dfs(x-1,op&&i==lim,(mo*10%mod+i)%mod,cm(Lcm,i));
	}
	if(!op) dp[x][mo][mp[Lcm]]=tot;
	return tot;
	
}
void init()
{
	ll cn=0;
	for(int i=1;i<=2520;++i)
	{
		if(2520%i) continue;
		mp[i]=++cn;
	}
//	cout<<cn<<endl;
}
ll f(ll x)
{
	cnt=0;
	while(x)
	{
		a[++cnt]=x%10;
		x/=10;
	}
	return dfs(cnt,1,0,1);
}
```

[CF908G](https://blog.csdn.net/sophilex/article/details/131385426?spm=1001.2014.3001.5502)

求区间[L,R]内每一个数字在各数位排序后得到的数的和，答案对1e9+7取模

n<=1e700

思路：

这道题唯一的性质就是每一个数字的各个数位是升序排列的，然后我们可以发现这么一个结论：

每一个数字都可以被**最多9个由1组成的数**累加得到

举个例子,23349,可以按照如下方式拆分

<img src="C:\Users\26463\AppData\Roaming\Typora\typora-user-images\image-20230917100803071.png" alt="image-20230917100803071" style="zoom:50%;" />

最多只有9个是因为每一位最大只有9。然后我们看一下每一行的1的个数，其实也不难发现，第i行的1的个数=大于等于i的数字的个数，手模一下就可以验证了。

所以在第d行，计数字x中>=d的数字的个数为k，则x在第d行的贡献就是(10^k-1)/9(其实就是在构造k个1)

在此基础上，我们分9次来计算每一行的贡献。对于第d行，设dpi,j表示前i位，有j位>=d,直接做数位dp即可

所以此题的关键就是用1来组成数字，剩余的工作就都是很基础的了。不好想，只能说留个印象。

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define endl '\n'
const ll N=1e5+10;
const ll mod=1e9+7;
string n;
ll a[710];
ll cnt=0;
ll dp[710][710]; 
ll p[710];
ll inv9;
ll ksm(ll x,ll y)
{
	ll ans=1;
	while(y)
	{
		if(y&1) ans=ans*x%mod;
		x=x*x%mod;
		y>>=1;
	}
	return ans;
}
ll inv(ll x)
{
	return ksm(x,mod-2);
}
void init()
{
	inv9=inv(9); 
	ll now=1;
	for(int i=1;i<=710;++i)
	{
		p[i]=((now*10%mod)-1+mod)%mod*inv9%mod;
		now=now*10%mod;
	}
//	for(int i=1;i<=10;++i) cout<<p[i]<<endl;
}
ll dfs(ll x,ll op,ll pre,ll d)
{
	if(!x) return p[pre];
	if(!op&&dp[x][pre]!=-1) return dp[x][pre];
	ll tot=0;
	ll lim=op?a[x]:9;
	for(int i=0;i<=lim;++i)
	{
		if(i<d) tot=(tot+dfs(x-1,op&&i==lim,pre,d))%mod;
		else tot=(tot+dfs(x-1,op&&i==lim,pre+1,d))%mod;
	}
	if(!op) dp[x][pre]=tot;
	return tot;
}
ll f(string s)
{
	cnt=0;
	int len=s.size();
	for(int i=len-1;i>=0;--i)
	{
		a[++cnt]=s[i]-'0';
	}
	ll ans=0;
	for(int i=1;i<=9;++i)
	{
		memset(dp,-1,sizeof dp);
		ans=(ans+dfs(cnt,1,0,i))%mod;
	}
	return ans;
}
void solve()
{
	init();
	cin>>n;
	cout<<f(n)<<endl;
}
int main()
{
	ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);
	solve();
	return 0;
}
```



## 序列自动机

```c++

ll ne[N][30];
void init()
{
	ll len=strlen(s+1);
	for(int i=len;i;--i)
	{
		for(int j=0;j<26;++j) ne[i-1][j]=ne[i][j];
	    ne[i-1][s[i]-'a']=i;
	}
}
void solve()
{//判断是否为子序列
	cin>>(ss+1);
	ll p=0;
	for(int i=1;i<=strlen(ss+1);++i)
	{
		p=ne[p][ss[i]-'a'];
		if(!p)
		{
			cout<<"No"<<endl;
			return;
		}
	}
	cout<<"Yes"<<endl;
}
```

## 坐标旋转

输入坐标，及一个角度制度数(90,180之类)，求坐标逆时针旋转该角度后的新坐标

```c++
const long double pi=acos(-1)
int main()
{
	long double a,b,c,d,x,y,theta;
	cin>>a>>b>>d;//坐标x，y 逆时针旋转角度
	theta=(long double)d/(long double)360*1.0000000*2*pi;//弧度制
	x=(long double)a*(long double)cos(theta)-b*sin(theta);
	y=(long double)a*(long double)sin(theta)+b*cos(theta);
	cout<<fixed<<setprecision(8)<<x<<' '<<y<<endl;
	return 0;
}
```

## 自适应辛普森

时间复杂度:

$O(log\frac{r-l}{eps}T(cal))$

其中$r,l$为积分区间，$eps$为精度,$T(cal)$为单次计算的时间复杂度

如果积分函数的最高次$\leq $2次的话，就可以用这个

```c++
double f(double x){  
    return //积分函数
}
double simpson(double a,double b){
    double c=(a+b)/2.0;
    return (f(a)+f(b)+4.0*f(c))*(b-a)/6.0;
}
double ars(double a,double b,double eps){  //积分区域a~b,eps为题目要求精度*10-2
    double c=(a+b)/2.0;
    double mid=simpson(a,b),l=simpson(a,c),r=simpson(c,b);
    if(fabs(l+r-mid)<=15*eps)  return l+r+(l+r-mid)/15.0;
    return ars(a,c,eps/2.0)+ars(c,b,eps/2.0);
}

```

注意事项：

* 存在误差，在时间复杂度允许时，建议开大精度范围
