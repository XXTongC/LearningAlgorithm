# A

## 解法1（只使用Kruskal）

考虑方法1 先二重循环枚举合法点取出有效边 $kruskal$ $O(n) = n^2log(n)$

考虑方法2 枚举合法点的同时取合法边 $O(n) = mlog(n)$

根据点的数量分类求解。

```cpp
//建议打开o2
#pragma GCC optimize(2)
#include<iostream>
#include <utility>
#include <algorithm>
#include <cstring>
#include <vector>
#include <map>

#define pb push_back
#define ff first
#define ss second
const int N =1e5+10;
using ll = long long;
const int M = 5005;
const int INF=0x3f3f3f3f;
using PLL = std::pair<ll,ll>;
ll f[N];
ll find(ll x)     //并查集+路径优化，用于储存各棵树
{
	if (f[x] != x) f[x] = find(f[x]);
	return f[x];
}
struct edge
{
    ll l,r,v;
    bool operator<(const edge &t) const
    {
        return v< t.v;
    }
};
std::vector<edge>st;        // 总边集
std::map<PLL, ll> mp; // 用于查找边权
ll s[N];   // 查询点集
bool vis[N]; // 需要处理的点(当询问规模过大时使用)
void solve()
{
    ll n,m,q;
    std::cin>>n>>m>>q;
    for (int i=1;i<=m;i++)
    {
        ll u,v,w;
        std::cin>>u>>v>>w;
        st.push_back({u, v, w});
        mp[{u,v}]=w;
    }
    std::sort(st.begin(),st.end()); // 边权从小到大排序
    while (q--)
    {
        for(int i=1;i<=n;i++) vis[i]=0;
        ll k;
        std::cin>>k;
        for (int i=1;i<=k; i++)
        {
            std::cin>>s[i];
            f[s[i]]=s[i];
            vis[s[i]]=1;
        }
        std::vector<edge>vec; // 本轮建立MST所用到的边集
        if(k<1000)///点少取点，点多取边
        {
            for (int l=1;l<=k;l++)
            {
                for (int r=1;r<=k;r++)
                {
                    if(mp.find({s[l], s[r]})!=mp.end())
                    // if (mp[{s[l], s[r]}] != 0)// 直接比较会超时，需要用find
                    {
                        vec.push_back({s[l],s[r],mp[{s[l],s[r]}]});
                    }
                }
            }
            std::sort(vec.begin(), vec.end());
        }
        else
        {
            vec=st;
        }
        // Kruskal模版
        ll ans=0,cnt=0;
        ll len=vec.size();
        for(int i=0;i<len;i++)
        {    
            ll a=vec[i].l,b=vec[i].r,w=vec[i].v;
            if(vis[a]&&vis[b])
            {
                a=find(a),b=find(b);
                if(a!=b) // 如果两个连通块不连通，则将这两个连通块合并
                {
                    f[a]=b;
                    ans+=w;
                    cnt++;
                }
            }
        }
        if(cnt<k-1)
            ans=-1;
        std::cout<<ans<<'\n';
    }
}
int main()
{
    std::ios::sync_with_stdio(false);
    std::cin.tie(0);
    std::cout.tie(0);
    solve();
}
```

## 解法2

一样使用根号，但点少时使用Prim，点多时使用Kruskal，那么保证 $O(n) = n\sqrt{n}$

```cpp
#pragma GCC optimize(2)
#include <iostream>
#include <algorithm>
#include <cstring>
#include <unordered_set>
#include <cmath>
#include <unordered_map>
using ll = long long;
const ll INF = 1e16;
const int N = 2e5 + 7;
std::unordered_set<int> s;	//prime算法存有效节点
std::unordered_map <int, ll> mp[N];	//prime算法存图
ll dist[N];
int n, m, q,k;

int p[N];
int st[N];

struct node
{
	int a;
	int b;
	int w;
	bool operator<(const node& d) const
	{
		return w < d.w;
	}
}Nodes[2 * N];
int find(int x)     //并查集+路径优化，用于储存各棵树
{
	if (p[x] != x) p[x] = find(p[x]);
	return p[x];
}

void kruscal()
{
	int count = 1;
	ll sum = 0;
	
	memset(p, 0, sizeof p);
	for (int o = 0; o < k; o++)
	{
		int op;
		std::cin >> op;
		p[op] = op;	//同时初始化树，不在子图里的自动初始化为0
	}

	for (int i = 0; count != k && i < m; i++) //如果森林里只剩一棵树了可以提前结束，减少时间消耗
	{
		int a = Nodes[i].a, b = Nodes[i].b, w = Nodes[i].w;
		a = find(a), b = find(b);
		if (a != 0 && b != 0 && a != b)
		{
			sum += w;
			++count;
			p[a] = b;
		}
	}
	if (count < k) std::cout << -1 << std::endl;
	else std::cout << sum << std::endl;
}

void prime()
{
	s.clear();
	int u;
	for (int i = 1; i <= k; i++) {
		std::cin >> u;
		s.insert(u);
	}
	ll res, cnt;

	for (auto& v : s) {
		dist[v] = INF;
	}

	dist[*s.begin()] = 0;
	res = cnt = 0;

	while (s.size()) {

		ll mn = INF;
		int pos = 0;

		for (auto& v : s) {
			if (mn > dist[v]) {
				mn = dist[v];
				pos = v;
			}
		}

		if (pos == 0) break;

		s.erase(pos);
		res += mn;
		cnt++;

		for (auto& v : s) {
			auto it = mp[pos].find(v);
			if (it == mp[pos].end()) continue;
			dist[v] = std::min(dist[v], it->second);
		}

	}

	if (s.size() != 0) std::cout << -1 << std::endl;
	else std::cout << res << std::endl;
}

int main()
{
	std::ios::sync_with_stdio(false);
	std::cin.tie(0);
	std::cout.tie(0);
	std::cin >> n >> m >> q;
	
	for (int i = 0; i < m; i++)
	{
		int a, b, c;
		std::cin >> a >> b >> c;
		mp[a][b] = c;
		mp[b][a] = c;
		if (a != b)
		{
			Nodes[i] = { a,b,c };
		}
	}
	std::sort(Nodes, Nodes + m);     //以边的长度进行排序
	for (int j = 0; j < q; j++)
	{
		
		std::cin >> k;
		if(k>std::sqrt(n))	//根据图的稠密与否来选择算法
		{
			kruscal();
		}else
		{
			prime();
		}
		
	}
}
```