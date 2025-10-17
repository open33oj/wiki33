# 最近公共祖先

## 例题

给定一棵 $n$ 个点的树，$s$ 是根节点，$m$ 个询问。

每次询问点两个点的 `lca`。

## 预处理 $\sqrt{n}$ 祖先

$O((n+Q)\sqrt{n})$

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 500000;
int n, m, s, nn;
vector<int> e[MAXN + 5];
int fa[MAXN + 5];   // fa[u] 记录 u 的父节点
int faNN[MAXN + 5]; // faNN[u] 记录 u 的 nn 层祖宗节点
int dep[MAXN + 5];  // dep[u] 记录 1 为根节点时的深度
void dfs(int u, int father)
{
    fa[u] = father;
    for (int v : e[u])
    {
        if (v == father)
            continue;
        dep[v] = dep[u] + 1;
        dfs(v, u);
    }
}
int lca(int u, int v)
{
    if (dep[v] < dep[u])
        swap(u, v);
    while (dep[v] - dep[u] >= nn)
        v = faNN[v];
    while (dep[v] != dep[u])
        v = fa[v];
    while (faNN[v] != faNN[u])
        u = faNN[u], v = faNN[v];
    while (u != v)
        u = fa[u], v = fa[v];
    return u;
}
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> m >> s;
    nn = sqrt(n);
    for (int i = 1; i <= n - 1; i++)
    {
        int u, v;
        cin >> u >> v;
        e[u].push_back(v);
        e[v].push_back(u);
    }
    dep[s] = 0;
    dfs(s, 0);
    for (int u = 1; u <= n; u++)
    {
        faNN[u] = u;
        for (int i = 1; i <= nn; i++)
            faNN[u] = fa[faNN[u]];
    }
    while (m--)
    {
        int u, v;
        cin >> u >> v;
        cout << lca(u, v) << "\n";
    }
    return 0;
}

```

## 倍增

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 500'000;
int n, m, s;
vector<int> e[MAXN + 5];
// f[i][j] 记录 i 的 2^j 级别祖先
int f[MAXN + 5][25];
// dis[i] 求节点 i 的深度
int dis[MAXN + 5];
void dfs(int u, int fa)
{
    f[u][0] = fa;
    for (int i = 0; i < e[u].size(); i++)
    {
        int v = e[u][i];
        if (v == fa)
            continue;
        dis[v] = dis[u] + 1;
        dfs(v, u);
    }
}
// lca(u,v) 返回 u,v 的最近公共祖先
int lca(int u, int v)
{
    // 保证 u 在上面，v 在下面
    if (dis[v] < dis[u])
        swap(u, v);
    // 拉到同样的深度
    for (int j = 20; j >= 0; j--)
        if (dis[v] - dis[u] >= (1 << j))
            v = f[v][j];
    // 初始两点之间是祖孙关系时，拉到同样深度就会变成同一个点
    if (u == v)
        return u;
    // 同步往上走，跳到了 lca 下面一跳位
    for (int j = 20; j >= 0; j--)
        if (f[u][j] != f[v][j])
            u = f[u][j], v = f[v][j];
    return f[u][0];
}
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> m >> s;
    for (int i = 1; i <= n - 1; i++)
    {
        int u, v;
        cin >> u >> v;
        e[u].push_back(v);
        e[v].push_back(u);
    }
    dis[s] = 0;
    dfs(s, 0); // 预处理深度 dis[u]，以及一级祖先 f[u][0]

    // i 的 2^j 级别祖先 = i 的 2^{j-1} 级别祖先的 2^{j-1} 级别祖先
    for (int j = 1; (1LL << j) <= n; j++)
        for (int i = 1; i <= n; i++)
            f[i][j] = f[f[i][j - 1]][j - 1];

    while (m--)
    {
        int u, v;
        cin >> u >> v;
        cout << lca(u, v) << "\n";
    }
    return 0;
}

```

## 欧拉序+st表

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 500'000;
int n, m, s;
vector<int> e[MAXN + 5];
// dis[i] 存 i 的深度
int dis[MAXN + 5];
// <深度, 点的编号>
vector<pair<int, int>> a;
// pos[i] 存 i 在 a 中第一次出现的下标
int pos[MAXN + 5];
void dfs(int u, int fa)
{
    a.push_back({dis[u], u});
    pos[u] = (int)a.size() - 1;
    for (int i = 0; i < e[u].size(); i++)
    {
        int v = e[u][i];
        if (v == fa)
            continue;
        dis[v] = dis[u] + 1;
        dfs(v, u);
        a.push_back({dis[u], u});
    }
}
// 处理 a 数组的 st 表
// st[i][j] 存 a[i] 开始的 2^j 个元素中的最小值下标
int st[MAXN * 2 + 5][20];
void initST()
{
    int len = a.size();
    for (int i = 0; i < len; i++)
        st[i][0] = i;
    for (int j = 1; (1LL << j) <= len; j++)
    {
        for (int i = 0; i + (1LL << j) - 1 < len; i++)
        {
            // i 为起点，2^j 长度的最小值下标
            int L = st[i][j - 1];
            int R = st[i + (1LL << (j - 1))][j - 1];
            if (a[L].first < a[R].first)
                st[i][j] = L;
            else
                st[i][j] = R;
        }
    }
}
int lca(int u, int v)
{
    if (pos[u] > pos[v])
        swap(u, v);
    // 找到 pos[u]~pos[v] 之间 first 最小的下标
    int len = pos[v] - pos[u] + 1;
    int j = log2(len);
    // pos[u] 开头的 2^j 和 pos[v] 结尾的 2^j 的最小值的最小值
    int L = st[pos[u]][j];
    int R = st[pos[v] - (1LL << j) + 1][j];
    if (a[L].first < a[R].first)
        return a[L].second;
    else
        return a[R].second;
}
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> m >> s;
    for (int i = 1; i <= n - 1; i++)
    {
        int u, v;
        cin >> u >> v;
        e[u].push_back(v);
        e[v].push_back(u);
    }
    dis[s] = 0;
    dfs(s, 0);
    initST();
    while (m--)
    {
        int u, v;
        cin >> u >> v;
        cout << lca(u, v) << "\n";
    }
    return 0;
}

```

## tarjan(离线+并查集)

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 1000000;
const int MAXM = 1000000;
int n, m, s;
vector<int> e[MAXN + 5];
//(v, 问题 id)
vector<pair<int, int> > ask[MAXN + 5];
int ans[MAXM + 5]; // 问题 id 为 i 的问题答案

// 每个点是否搜过了
bool vis[MAXN + 5];
// 并查集
int fa[MAXN + 5];
int findFa(int x)
{
    if (fa[x] == x)
        return x;
    else
        return fa[x] = findFa(fa[x]);
}
void dfs(int u, int from)
{
    for (int i = 0; i < ask[u].size(); i++)
        if (vis[ask[u][i].first])
            ans[ask[u][i].second] = findFa(ask[u][i].first);
    vis[u] = true;
    for (int i = 0; i < e[u].size(); i++)
    {
        int v = e[u][i];
        if (v == from)
            continue;
        dfs(v, u);
        fa[v] = u;
    }
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> m >> s;
    for (int i = 1; i <= n - 1; i++)
    {
        int u, v;
        cin >> u >> v;
        e[u].push_back(v);
        e[v].push_back(u);
    }
    memset(ans, 0, sizeof(ans));
    // 读询问
    for (int i = 1; i <= m; i++)
    {
        int u, v, id;
        cin >> u >> v;
        id = i;
        if (u == v)
            ans[i] = u;
        else
        {
            ask[u].push_back(make_pair(v, id));
            ask[v].push_back(make_pair(u, id));
        }
    }
    // tarjan
    for (int i = 1; i <= n; i++)
    {
        fa[i] = i;
        vis[i] = false;
    }
    dfs(s, 0);
    for (int i = 1; i <= m; i++)
        cout << ans[i] << "\n";
    return 0;
}
```

## 树链剖分

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 500000 + 5;
int n, m, s;
vector<int> e[MAXN];
//每个点的：父节点、深度、大小、重子节点
int fa[MAXN], dep[MAXN], siz[MAXN], hson[MAXN];
void dfs_build(int u, int fat)
{
    hson[u] = 0;
    siz[hson[u]] = 0;
    siz[u] = 1;
    for (int i = 0; i < e[u].size(); i++)
    {
        int v = e[u][i];
        if (v == fat)
            continue;
        dep[v] = dep[u] + 1;
        fa[v] = u;
        dfs_build(v, u);
        siz[u] += siz[v];
        if (siz[v] > siz[hson[u]])
            hson[u] = v;
    }
}
//每个点的：所在链的链顶、重边优先的 dfs 序、dfs序对应的节点编号
int tot, top[MAXN], dfn[MAXN], rnk[MAXN];
void dfs_div(int u, int fa)
{
    dfn[u] = ++tot;
    rnk[tot] = u;
    if (hson[u])
    {
        top[hson[u]] = top[u];
        dfs_div(hson[u], u);
        for (int i = 0; i < e[u].size(); i++)
        {
            int v = e[u][i];
            if (v == fa || v == hson[u])
                continue;
            top[v] = v;
            dfs_div(v, u);
        }
    }
}
int lca(int u, int v)
{
    while (top[u] != top[v])
    {
        if (dep[top[u]] > dep[top[v]])
            u = fa[top[u]];
        else
            v = fa[top[v]];
    }
    return dep[u] > dep[v] ? v : u;
}
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> m >> s;
    for (int i = 1; i <= n - 1; i++)
    {
        int u, v;
        cin >> u >> v;
        e[u].push_back(v);
        e[v].push_back(u);
    }
    dep[s] = 1;
    fa[s] = 0;
    dfs_build(s, 0);
    tot = 0;
    top[s] = s;
    dfs_div(s, 0);
    while (m--)
    {
        int u, v;
        cin >> u >> v;
        cout << lca(u, v) << "\n";
    }
    return 0;
}
```