---
title: "AtCoder Educational DP Contest 题解"
---

## A. Frog 1

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 100000 + 5;
const int INF = 1000000000;
int n;
int h[MAXN + 5];
int f[MAXN + 5]; // f[i] 走到 i 的时候的最小代价
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> h[i];
    f[1] = 0;
    for (int i = 2; i <= n; i++)
    {
        f[i] = INF;
        if (i >= 2)
            f[i] = min(f[i], f[i - 1] + abs(h[i] - h[i - 1]));
        if (i >= 3)
            f[i] = min(f[i], f[i - 2] + abs(h[i] - h[i - 2]));
    }
    cout << f[n];
    return 0;
}
```

## B. Frog 2

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 100000;
const int INF = 1000000000;
int n, k;
int h[MAXN + 5];
int f[MAXN + 5]; // f[i] 走到 i 的时候的最小代价
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> k;
    for (int i = 1; i <= n; i++)
        cin >> h[i];
    f[1] = 0;
    for (int i = 2; i <= n; i++)
    {
        f[i] = INF;
        for (int j = i - 1; j >= max(1, i - k); j--)
            f[i] = min(f[i], f[j] + abs(h[i] - h[j]));
    }
    cout << f[n];
    return 0;
}
```

## C. Vacation

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 100000;
int n;
int a[MAXN + 5], b[MAXN + 5], c[MAXN + 5];
int fa[MAXN + 5], fb[MAXN + 5], fc[MAXN + 5];
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> a[i] >> b[i] >> c[i];
    for (int i = 1; i <= n; i++)
    {
        fa[i] = max(fb[i - 1], fc[i - 1]) + a[i];
        fb[i] = max(fa[i - 1], fc[i - 1]) + b[i];
        fc[i] = max(fa[i - 1], fb[i - 1]) + c[i];
    }
    cout << max(fa[n], max(fb[n], fc[n]));
    return 0;
}
```

## D. Knapsack 1

### 递推

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int MAXN = 100;
const int MAXW = 100000;
int n, W;                     // 物品数量、背包容量
int w[MAXN + 5], v[MAXN + 5]; // 重量、价值
int f[MAXW + 5];              // 每个容量下的最大价值
signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> W;
    for (int i = 1; i <= n; i++)
        cin >> w[i] >> v[i];
    for (int i = 1; i <= n; i++)
        for (int j = W; j >= w[i]; j--)
            f[j] = max(f[j], f[j - w[i]] + v[i]);
    cout << f[W];
    return 0;
}
```

### 记忆化搜索

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
int n, W;
int w[105], v[105];
// 前 nn 件物品在 ww 重量限制下的最大价值
int book[105][100005];
int f(int nn, int ww)
{
    if (book[nn][ww] != -1)
        return book[nn][ww];
    if (nn == 0)
        return 0;
    int res = 0;
    // 不用第 nn 件物品
    res = max(res, f(nn - 1, ww));
    // 用第 nn 件物品
    if (w[nn] <= ww)
        res = max(res, f(nn - 1, ww - w[nn]) + v[nn]);
    return book[nn][ww] = res;
}
signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> W;
    for (int i = 1; i <= n; i++)
        cin >> w[i] >> v[i];
    for (int i = 0; i <= n; i++)
        for (int j = 0; j <= W; j++)
            book[i][j] = -1;
    cout << f(n, W);
    return 0;
}
```

## E. Knapsack 2

### 二维形式

```cpp
#include <bits/stdc++.h>

using namespace std;
int n, W;
int w[105], v[105];
// f[i][j]: 前 i 件物品，得到 j 的价值：需要的最少重量
int f[105][100005];
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> W;
    for (int i = 1; i <= n; i++)
        cin >> w[i] >> v[i];

    for (int j = 0; j <= 100000; j++)
        f[0][j] = W + 1;
    f[0][0] = 0;

    for (int i = 1; i <= n; i++)
        for (int j = 0; j <= 100000; j++)
        {
            if (j < v[i])
                f[i][j] = f[i - 1][j];
            else
                f[i][j] = min(f[i - 1][j], f[i - 1][j - v[i]] + w[i]);
        }

    int ans = 0;
    for (int i = 0; i <= 100000; i++)
        if (f[n][i] <= W)
            ans = i;
    cout << ans;
    return 0;
}
```

### 化掉第一维

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int MAXN = 100;
const int MAXV = 100 * 1000;
int n, W;                     // 物品数量、背包容量
int w[MAXN + 5], v[MAXN + 5]; // 重量、价值
int f[MAXV + 5];              // 每个价值的最小重量
signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> W;
    for (int i = 1; i <= n; i++)
        cin >> w[i] >> v[i];

    // 初始化，只有 0 的价值不需要重量能达到
    f[0] = 0;
    for (int i = 1; i <= MAXV; i++)
        f[i] = W + 1;

    // dp
    for (int i = 1; i <= n; i++)
        for (int j = MAXV; j >= v[i]; j--)
            f[j] = min(f[j], f[j - v[i]] + w[i]);

    // 检查可以在 W 容量下达到的最大价值
    for (int i = MAXV; i >= 0; i--)
        if (f[i] <= W)
        {
            cout << i;
            break;
        }
    return 0;
}
```

## F. LCS

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXLEN = 3000;
string s, t;
int f[MAXLEN + 5][MAXLEN + 5];
string ans;
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> s >> t;
    for (int i = 1; i <= (int)s.size(); i++)
        for (int j = 1; j <= (int)t.size(); j++)
        {
            if (s[i - 1] == t[j - 1])
                f[i][j] = f[i - 1][j - 1] + 1;
            else
                f[i][j] = max(f[i - 1][j], f[i][j - 1]);
        }
    int i = (int)s.size();
    int j = (int)t.size();
    ans = "";
    while (i != 0 && j != 0)
    {
        if (s[i - 1] == t[j - 1])
        {
            ans = s[i - 1] + ans;
            i--;
            j--;
        }
        else if (f[i - 1][j] > f[i][j - 1])
            i--;
        else
            j--;
    }
    cout << ans;
    return 0;
}
```

## G. Longest Path

### 利用记忆化搜索

不用手动维护求解顺序了

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 100000;
const int MAXM = 100000;
int n, m;
vector<int> e[MAXN + 5];
// u 为起点能走的最长路径
int book[MAXN + 5];
int f(int u)
{
    if (book[u] != -1)
        return book[u];
    int res = 0;
    for (int v : e[u])
        res = max(res, f(v) + 1);
    return book[u] = res;
}
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= m; i++)
    {
        int u, v;
        cin >> u >> v;
        e[u].push_back(v);
    }

    for (int i = 1; i <= n; i++)
        book[i] = -1;

    int ans = 0;
    for (int i = 1; i <= n; i++)
        ans = max(ans, f(i));
    cout << ans;
    return 0;
}
```

### 利用拓扑排序

解决先求谁在求谁的问题。

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 100000;
const int MAXM = 100000;
int n, m;
vector<int> e[MAXN + 5];
vector<int> ee[MAXN + 5]; // 存反向边（用来求解 f）
// 以 i 为终点的路径数量
// f[i] = max(f[前一个点]+1);
int f[MAXN + 5];
int d[MAXN + 5]; // 当前入度，用来求拓扑序，按照拓扑顺序求解 f
queue<int> zero; // 当前入度为 0 的点
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= m; i++)
    {
        int u, v;
        cin >> u >> v;
        e[u].push_back(v);
        ee[v].push_back(u);
        d[v]++;
    }

    for (int i = 1; i <= n; i++)
        if (d[i] == 0)
            zero.push(i);
    while (!zero.empty())
    {
        int u = zero.front();
        zero.pop();
        // 求 f
        f[u] = 0;
        for (int v : ee[u])
            f[u] = max(f[u], f[v] + 1);
        // 维护拓扑序
        for (int v : e[u])
        {
            d[v]--;
            if (d[v] == 0)
                zero.push(v);
        }
    }

    int ans = 0;
    for (int i = 1; i <= n; i++)
        ans = max(ans, f[i]);
    cout << ans;
    return 0;
}
```

## H. Grid 1

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MOD = 1'000'000'000 + 7;
int n, m;
char g[1005][1005];
int f[1005][1005];
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            cin >> g[i][j];
    f[1][1] = 1;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
        {
            if (i == 1 && j == 1)
                continue;
            if (g[i][j] != '#')
                f[i][j] = (f[i - 1][j] + f[i][j - 1]) % MOD;
            else
                f[i][j] = 0;
        }
    cout << f[n][m];
    return 0;
}
```

## I. Coins

```cpp
#include <bits/stdc++.h>
using namespace std;
int n;
double p[3000];
// f[i][j+3000]
// 前 i 个硬币，正面比反面多 j 个的概率
double f[3000][6000];
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> p[i];
    f[0][0 + 3000] = 1;
    for (int i = 1; i <= n; i++)
        for (int j = -i; j <= i; j++)
            f[i][j + 3000] = f[i - 1][j - 1 + 3000] * p[i] +
                             f[i - 1][j + 1 + 3000] * (1 - p[i]);
    double ans = 0;
    for (int i = 1; i <= n; i++)
        ans += f[n][i + 3000];
    cout << fixed << setprecision(10) << ans;
    return 0;
}
```

## J. Sushi

```cpp
#include <bits/stdc++.h>
using namespace std;
int n;
int cnt[5];
double f[305][305][305];
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        int x;
        cin >> x;
        cnt[x]++;
    }
    for (int i = 0; i <= n; i++)
        for (int j = 0; j <= n; j++)
            for (int k = 0; k <= n; k++)
            {
                if (i == 0 && j == 0 && k == 0)
                {
                    f[i][j][k] = 0;
                    continue;
                }
                int o = n - i - j - k;
                // f[i][j][k] = 1+
                //              (o/n)*f[i][j][k]+
                //              (i/n)*f[i-1][j+1][k]+
                //              (j/n)*f[i][j-1][k+1]+
                //              (k/n)*f[i][j][k-1]
                f[i][j][k] = 1;
                if (i != 0)
                    f[i][j][k] += ((double)i / n) * f[i - 1][j + 1][k];
                if (j != 0)
                    f[i][j][k] += ((double)j / n) * f[i][j - 1][k + 1];
                if (k != 0)
                    f[i][j][k] += ((double)k / n) * f[i][j][k - 1];
                f[i][j][k] *= ((double)n / (n - o));
            }
    cout << fixed << setprecision(10) << f[cnt[3]][cnt[2]][cnt[1]];
    return 0;
}
```

## K. Stones

```cpp
#include <bits/stdc++.h>
using namespace std;
int n, k;
int a[105];
bool f[100000 + 5];
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> k;
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    for (int i = 1; i <= k; i++)
        for (int j = 1; j <= n; j++)
            if (i - a[j] >= 0 && f[i - a[j]] == false)
            {
                f[i] = true;
                break;
            }
    if (f[k])
        cout << "First";
    else
        cout << "Second";
    return 0;
}
```

## L. Deque

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
int n;
int a[3005];
int maxAns[3005][3005], minAns[3005][3005];
signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    for (int len = 1; len <= n; len++)
    {
        for (int l = 1; l + len - 1 <= n; l++)
        {
            int r = l + len - 1;
            if (len == 1)
            {
                minAns[l][r] = -a[l];
                maxAns[l][r] = a[l];
                continue;
            }
            minAns[l][r] = min(maxAns[l][r - 1] - a[r], maxAns[l + 1][r] - a[l]);
            maxAns[l][r] = max(minAns[l][r - 1] + a[r], minAns[l + 1][r] + a[l]);
        }
    }
    cout << maxAns[1][n];
    return 0;
}
```

## M. Candies

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MOD = 1'000'000'000 + 7;
int n, k;
int a[105];
int f[105][100000 + 5];
int sum[105][100000 + 5];
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> k;
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    f[0][0] = 1;
    sum[0][0] = f[0][0];
    for (int i = 1; i <= k; i++)
        sum[0][i] = sum[0][i - 1] + f[0][i];
    for (int i = 1; i <= n; i++)
    {
        for (int j = 0; j <= k; j++)
        {
            // f[i][j] = sum(f[i-1][j-0 ~ j-a[i]])
            int l = max(j - a[i], 0);
            int r = j - 0;
            if (l == 0)
                f[i][j] = sum[i - 1][r];
            else
            {
                f[i][j] = sum[i - 1][r] + MOD - sum[i - 1][l - 1];
                f[i][j] %= MOD;
            }
        }
        sum[i][0] = f[i][0];
        for (int j = 1; j <= k; j++)
            sum[i][j] = (sum[i][j - 1] + f[i][j]) % MOD;
    }
    cout << f[n][k];
    return 0;
}
```

## N. Slimes

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
int n;
int a[405];
int sum[405];
int f[405][405];
signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> a[i];

    sum[1] = a[1];
    for (int i = 2; i <= n; i++)
        sum[i] = sum[i - 1] + a[i];

    for (int len = 1; len <= n; len++)
        for (int l = 1; l + len - 1 <= n; l++)
        {
            int r = l + len - 1;
            if (len == 1)
            {
                f[l][r] = 0;
                continue;
            }
            f[l][r] = f[l][l] + f[l + 1][r] + sum[r] - sum[l - 1];
            for (int mid = l + 1; mid <= r - 1; mid++)
                f[l][r] = min(f[l][r],
                              f[l][mid] + f[mid + 1][r] + sum[r] - sum[l - 1]);
        }
    cout << f[1][n];
    return 0;
}
```

## O. Matching

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MOD = 1000000000 + 7;
int n;
int a[25][25];
int book[25][(1 << 21) + 5];
// 男生前 x 个人和 sta 这些人匹配的方案数
int dfs(int x, int sta)
{
    if (book[x][sta] != -1)
        return book[x][sta];
    if (x == 0)
        return 1;
    int res = 0;
    // 第 x 个男生和谁组合
    for (int i = 1; i <= n; i++)
    {
        // 是否存在第 i 个女生
        if ((sta & (1 << (i - 1))) == 0)
            continue;
        if (a[x][i] == 0)
            continue;
        res = (res + dfs(x - 1, sta - (1 << (i - 1)))) % MOD;
    }
    return book[x][sta] = res;
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++)
            cin >> a[i][j];
    for (int i = 0; i <= n; i++)
        for (int j = 0; j <= (1 << n) - 1; j++)
            book[i][j] = -1;
    cout << dfs(n, (1 << n) - 1);
    return 0;
}
```

## P. Independent Set

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int MAXN = 100000;
const int MOD = 1000000000 + 7;
int n;
vector<int> e[MAXN + 5];
int f[MAXN + 5][2];
void dfs(int u, int fa)
{
    f[u][0] = f[u][1] = 1;
    for (int v : e[u])
    {
        if (v == fa)
            continue;
        dfs(v, u);
        f[u][1] = f[u][1] * f[v][0] % MOD;
        f[u][0] = f[u][0] * (f[v][0] + f[v][1]) % MOD;
    }
}
signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n - 1; i++)
    {
        int u, v;
        cin >> u >> v;
        e[u].push_back(v);
        e[v].push_back(u);
    }
    dfs(1, 0);
    cout << (f[1][0] + f[1][1]) % MOD;
    return 0;
}
```

## Q. Flowers

```cpp
#include <bits/stdc++.h>
using namespace std;
int n;
int a[200000 + 5];
int h[200000 + 5];
long long f[200000 + 5];
int lowbit(int x)
{
    return x & -x;
}
long long t[200000 + 5];
void update(int x, long long y)
{
    for (int i = x; i <= n; i += lowbit(i))
        t[i] = max(t[i], y);
}
long long query(int x)
{
    long long res = t[x];
    for (int i = x; i >= 1; i -= lowbit(i))
        res = max(res, t[i]);
    return res;
}
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> h[i];
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    for (int i = 1; i <= n; i++)
    {
        f[i] = query(h[i] - 1) + a[i];
        update(h[i], f[i]);
    }
    long long ans = 0;
    for (int i = 1; i <= n; i++)
        ans = max(ans, f[i]);
    cout << ans;
    return 0;
}
```

## R. Walk

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int MOD = 1000000000 + 7;
const int MAXR = 50; // 矩阵最大行数
const int MAXC = 50; // 矩阵最大列数
struct Mat
{
    int r, c; // 行数列数
    int m[MAXR + 1][MAXC + 1];
    Mat() { memset(m, 0, sizeof(m)); }
};
Mat operator*(const Mat &a, const Mat &b)
{
    assert(a.c == b.r);
    Mat res;
    res.r = a.r, res.c = b.c;
    for (int i = 1; i <= a.r; i++)
        for (int k = 1; k <= a.c; k++)
        {
            int temp = a.m[i][k];
            for (int j = 1; j <= b.c; j++)
                res.m[i][j] = (res.m[i][j] + temp * b.m[k][j]) % MOD;
        }
    return res;
}
Mat quick_pow(Mat a, int b)
{
    assert(a.r == a.c);
    Mat res;
    res.r = res.c = a.r;
    for (int i = 1; i <= res.r; i++)
        res.m[i][i] = 1;
    while (b > 0)
    {
        if (b % 2)
            res = res * a;
        a = a * a;
        b = b / 2;
    }
    return res;
}
signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    int n, k;
    cin >> n >> k;
    Mat g;
    g.r = g.c = n;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++)
            cin >> g.m[i][j];
    // 计算答案
    g = quick_pow(g, k);
    int ans = 0;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++)
            ans = (ans + g.m[i][j]) % MOD;
    cout << ans << "\n";
    return 0;
}
```

## S. Digit Sum

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXLEN = 10000;
const int MAXD = 100;
const int MOD = 1'000'000'000 + 7;
string sk;
int D;
int K[MAXLEN + 5], len;
int book[MAXLEN + 5][MAXD];
int dfs(int pos, int modd, bool zero, bool limit)
{
    if (pos < 0)
        return !zero && modd == 0;
    if (!zero && !limit && book[pos][modd] != -1)
        return book[pos][modd];
    int up = limit ? K[pos] : 9;
    int res = 0;
    for (int now = 0; now <= up; now++)
    {
        // 第 pos 位选 i
        res += dfs(pos - 1, ((modd - now) % D + D) % D, zero && now == 0, limit && now == K[pos]);
        res %= MOD;
    }
    if (!zero && !limit)
        book[pos][modd] = res;
    return res;
}
int main()
{
    memset(book, -1, sizeof(book));
    cin >> sk >> D;
    len = sk.size();
    for (int i = 0; i < len; i++)
        K[i] = sk[len - 1 - i] - '0';
    cout << dfs(len - 1, 0, true, true);
    return 0;
}
``` 

## T. Permutation

### $O(n^3)$

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MOD = 1'000'000'000 + 7;
int n;
string s;
int f[3005][3005];
int main()
{
    cin >> n;
    cin >> s;
    f[1][1] = 1;
    for (int i = 2; i <= n; i++)
        for (int j = 1; j <= i; j++)
        {
            f[i][j] = 0;
            if (s[i - 2] == '>')
                for (int k = j; k <= i - 1; k++)
                {
                    f[i][j] += f[i - 1][k];
                    f[i][j] %= MOD;
                }
            else
                for (int k = 1; k <= j - 1; k++)
                {
                    f[i][j] += f[i - 1][k];
                    f[i][j] %= MOD;
                }
        }
    int ans = 0;
    for (int i = 1; i <= n; i++)
        ans = (ans + f[n][i]) % MOD;
    cout << ans;
    return 0;
}
```

### $O(n^2)$ 满分

前缀和优化

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MOD = 1'000'000'000 + 7;
int n;
string s;
int f[3005][3005];
int sum[3005][3005];
int main()
{
    cin >> n;
    cin >> s;
    f[1][1] = 1;
    sum[1][1] = 1;
    for (int i = 2; i <= n; i++)
    {
        for (int j = 1; j <= i; j++)
        {
            f[i][j] = 0;
            if (s[i - 2] == '>')
                f[i][j] = (sum[i - 1][i - 1] - sum[i - 1][j - 1] + MOD) % MOD;
            else
                f[i][j] = sum[i - 1][j - 1];
        }
        for (int j = 1; j <= i; j++)
            sum[i][j] = (sum[i][j - 1] + f[i][j]) % MOD;
    }
    int ans = 0;
    for (int i = 1; i <= n; i++)
        ans = (ans + f[n][i]) % MOD;
    cout << ans;
    return 0;
}
```

## U. Grouping

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int INF = 1'000'000'000'000'000'000;
int n;
int a[20][20];
int book1[(1 << 16) + 5];
int book2[(1 << 16) + 5];
int cal(int sta)
{
    if (book1[sta] != INF)
        return book1[sta];
    int res = 0;
    for (int i = 1; i <= n; i++)
        for (int j = i + 1; j <= n; j++)
        {
            if ((sta & (1 << (i - 1))) &&
                (sta & (1 << (j - 1))))
                res += a[i][j];
        }
    return book1[sta] = res;
}
int dfs(int sta)
{
    if (book2[sta] != INF)
        return book2[sta];
    if (sta == 0)
        return 0;
    int res = 0;
    for (int choose = sta; choose > 0; choose = (choose - 1) & sta)
        res = max(res, dfs(sta - choose) + cal(choose));
    return book2[sta] = res;
}
signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n;
    for (int i = 0; i <= (1 << n) - 1; i++)
        book1[i] = book2[i] = INF;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++)
            cin >> a[i][j];
    cout << dfs((1 << n) - 1);
    return 0;
}
```

## V. Subtree

### 不考虑取模的代码提供思路

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int MAXN = 100000;
int n, m;
vector<int> e[MAXN + 5];
int f[MAXN + 5]; // 1 为根节点时，节点 i 为黑色，子树 i 的涂色方案数
int g[MAXN + 5]; // i 为根节点时，节点 i 为黑色的涂色方案数
void dfs(int u, int fa)
{
    f[u] = 1;
    for (int i = 0; i < e[u].size(); i++)
    {
        int v = e[u][i];
        if (v == fa)
            continue;
        dfs(v, u);
        f[u] = f[u] * (f[v] + 1);
    }
}
void dfsdfs(int u, int fa)
{
    if (fa)
        g[u] = f[u] * (g[fa] / (f[u] + 1) + 1);
    for (int i = 0; i < e[u].size(); i++)
    {
        int v = e[u][i];
        if (v == fa)
            continue;
        dfsdfs(v, u);
    }
}
signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= n - 1; i++)
    {
        int u, v;
        cin >> u >> v;
        e[u].push_back(v);
        e[v].push_back(u);
    }
    dfs(1, 0);
    g[1] = f[1];
    dfsdfs(1, 0);
    for (int i = 1; i <= n; i++)
        cout << g[i] % m << "\n";
    return 0;
}
```

### 满分代码

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int MAXN = 100000;
int n, m;
vector<int> e[MAXN + 5];
int f[MAXN + 5]; // 1 为根节点时，节点 i 为黑色，子树 i 的涂色方案数
int g[MAXN + 5]; // i 为根节点时，节点 i 为黑色的涂色方案数
vector<int> mul[MAXN + 5];
vector<int> lum[MAXN + 5];
void dfs(int u, int fa)
{
    f[u] = 1;
    for (int i = 0; i < e[u].size(); i++)
    {
        int v = e[u][i];
        if (v == fa)
            continue;
        dfs(v, u);
        f[u] = f[u] * (f[v] + 1) % m;
    }
}
int cal(int u, int pos)
{
    int l = 1;
    int r = 1;
    if (pos != 0)
        l = mul[u][pos - 1];
    if (pos != (int)lum[u].size() - 1)
        r = lum[u][pos + 1];
    return l * r % m;
}
// faW 父节点去掉 u 之后且父节点涂黑的方案数
void dfsdfs(int u, int fa, int faW)
{
    g[u] = f[u] * (faW + 1) % m;
    for (int i = 0; i < e[u].size(); i++)
    {
        int v = e[u][i];
        if (v == fa)
        {
            mul[u].push_back(faW + 1);
            lum[u].push_back(faW + 1);
        }
        else
        {
            mul[u].push_back(f[v] + 1);
            lum[u].push_back(f[v] + 1);
        }
    }
    for (int i = 0; i + 1 < mul[u].size(); i++)
        mul[u][i + 1] = mul[u][i + 1] * mul[u][i] % m;
    for (int i = (int)lum[u].size() - 1; i >= 1; i--)
        lum[u][i - 1] = lum[u][i - 1] * lum[u][i] % m;
    for (int i = 0; i < e[u].size(); i++)
    {
        int v = e[u][i];
        if (v == fa)
            continue;
        dfsdfs(v, u, cal(u, i));
    }
}
signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= n - 1; i++)
    {
        int u, v;
        cin >> u >> v;
        e[u].push_back(v);
        e[v].push_back(u);
    }
    dfs(1, 0);
    g[1] = f[1];
    dfsdfs(1, 0, 0);
    for (int i = 1; i <= n; i++)
        cout << g[i] << "\n";
    return 0;
}
```
