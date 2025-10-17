# 线段树

## 单点修改、区间查询

[【模板】树状数组 1](https://oj.33dai.cn/p/P3374)

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 500000;
int n, m;
int a[MAXN + 5];
int t[MAXN * 4 + 5];
void build(int now, int l, int r)
{
    if (l == r)
    {
        t[now] = a[l];
        return;
    }
    int mid = (l + r) / 2;
    build(now * 2, l, mid);
    build(now * 2 + 1, mid + 1, r);
    t[now] = t[now * 2] + t[now * 2 + 1];
}
void update(int now, int l, int r, int x, int y)
{
    if (l == r)
    {
        t[now] += y;
        return;
    }
    int mid = (l + r) / 2;
    if (x <= mid)
        update(now * 2, l, mid, x, y);
    if (x > mid)
        update(now * 2 + 1, mid + 1, r, x, y);
    t[now] = t[now * 2] + t[now * 2 + 1];
}
int query(int now, int l, int r, int x, int y)
{
    if (x <= l && r <= y)
        return t[now];
    int mid = (l + r) / 2;
    int res = 0;
    if (x <= mid)
        res += query(now * 2, l, mid, x, y);
    if (y >= mid + 1)
        res += query(now * 2 + 1, mid + 1, r, x, y);
    return res;
}
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    build(1, 1, n);
    for (int i = 1; i <= m; i++)
    {
        int op, x, y;
        cin >> op;
        if (op == 1)
        {
            cin >> x >> y;
            update(1, 1, n, x, y);
        }
        if (op == 2)
        {
            cin >> x >> y;
            cout << query(1, 1, n, x, y) << "\n";
        }
    }
    return 0;
}
```

### 另一种常见的 query

不需要判断 `[x,y]` 是否和左/右半边有重叠

```cpp
int query(int now, int l, int r, int x, int y)
{
    if (y < l || r < x)
        return 0;
    if (x <= l && r <= y)
        return t[now];
    int mid = (l + r) / 2;
    int res = 0;
    res += query(now * 2, l, mid, x, y);
    res += query(now * 2 + 1, mid + 1, r, x, y);
    return res;
}
```

## 区间修改、区间查询

[【模板】线段树 1](https://oj.33dai.cn/p/P3372)

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int MAXN = 100000;
int n, m;
int a[MAXN + 5];
// 线段树：单点修改、区间查询（区间和）
int t[4 * MAXN + 5], lazy[4 * MAXN + 5];
// 基于a[]建线段树
void build(int now, int l, int r)
{
    if (l == r)
    {
        t[now] = a[l];
        return;
    }
    int mid = (l + r) / 2;
    build(now * 2, l, mid);
    build(now * 2 + 1, mid + 1, r);
    t[now] = t[now * 2] + t[now * 2 + 1];
}
void down(int now, int l, int r)
{
    int mid = (l + r) / 2;
    // 左子树 now*2,l,mid
    // 右子树 now*2+1,mid+1,r
    lazy[now * 2] += lazy[now];
    t[now * 2] += (mid - l + 1) * lazy[now];
    lazy[now * 2 + 1] += lazy[now];
    t[now * 2 + 1] += (r - mid) * lazy[now];
    lazy[now] = 0;
}
// 当前做到了树上的now号节点，当前节点对应的区间为 [l,r]
// 需要给 [x,y] 这些数都加 z
void update(int now, int l, int r, int x, int y, int z)
{
    if (x <= l && r <= y)
    {
        t[now] += (r - l + 1) * z;
        lazy[now] += z;
        return;
    }
    int mid = (l + r) / 2;
    // 左子树 now*2,l,mid
    // 右子树 now*2+1,mid+1,r
    down(now, l, r); // 把之前欠的结清
    if (x <= mid)
        update(now * 2, l, mid, x, y, z);
    if (y > mid)
        update(now * 2 + 1, mid + 1, r, x, y, z);
    t[now] = t[now * 2] + t[now * 2 + 1];
}
int query(int now, int l, int r, int x, int y)
{
    //[l,r] 是 [x,y] 的子区间
    if (x <= l && r <= y)
        return t[now];
    int mid = (l + r) / 2;
    // 左子树 now*2,l,mid
    // 右子树 now*2+1,mid+1,r
    down(now, l, r); // 把之前欠的结清
    int res = 0;
    if (x <= mid)
        res += query(now * 2, l, mid, x, y);
    if (y > mid)
        res += query(now * 2 + 1, mid + 1, r, x, y);
    return res;
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
    }
    build(1, 1, n);
    while (m--)
    {
        int op, x, y, z;
        cin >> op;
        if (op == 1)
        {
            cin >> x >> y >> z;
            update(1, 1, n, x, y, z);
        }
        else
        {
            cin >> x >> y; // cout sum(a[x]~a[y])
            cout << query(1, 1, n, x, y) << "\n";
        }
    }
    return 0;
}
```

### 标记永久化

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int MAXN = 100000;
int n, m;
int a[MAXN + 5];
// 线段树
int t[4 * MAXN + 5], lazy[4 * MAXN + 5];
// 基于a[]建线段树
void build(int now, int l, int r)
{
    if (l == r)
    {
        t[now] = a[l];
        return;
    }
    int mid = (l + r) / 2;
    build(now * 2, l, mid);
    build(now * 2 + 1, mid + 1, r);
    t[now] = t[now * 2] + t[now * 2 + 1];
}
// 当前做到了树上的now号节点，当前节点对应的区间为 [l,r]
// 需要给 [x,y] 这些数都加 z
void update(int now, int l, int r, int x, int y, int z)
{
    t[now] += (min(y, r) - max(x, l) + 1) * z;
    if (x <= l && r <= y)
    {
        lazy[now] += z;
        return;
    }
    int mid = (l + r) / 2;
    // 左子树 now*2,l,mid
    // 右子树 now*2+1,mid+1,r
    if (x <= mid)
        update(now * 2, l, mid, x, y, z);
    if (y > mid)
        update(now * 2 + 1, mid + 1, r, x, y, z);
}
int query(int now, int l, int r, int x, int y, int lazySum)
{
    //[l,r] 是 [x,y] 的子区间
    if (x <= l && r <= y)
        return t[now] + (r - l + 1) * lazySum;
    int mid = (l + r) / 2;
    // 左子树 now*2,l,mid
    // 右子树 now*2+1,mid+1,r
    int res = 0;
    lazySum += lazy[now];
    if (x <= mid)
        res += query(now * 2, l, mid, x, y, lazySum);
    if (y > mid)
        res += query(now * 2 + 1, mid + 1, r, x, y, lazySum);
    return res;
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
    }
    build(1, 1, n);
    while (m--)
    {
        int op, x, y, z;
        cin >> op;
        if (op == 1)
        {
            cin >> x >> y >> z;
            update(1, 1, n, x, y, z);
        }
        else
        {
            cin >> x >> y; // cout sum(a[x]~a[y])
            cout << query(1, 1, n, x, y, 0) << "\n";
        }
    }
    return 0;
}
```

### 区间加、区间乘、区间和查询

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int MAXN = 100000;
int n, m, p;
int a[MAXN + 5];
// 线段树：单点修改、区间查询（区间和）
int t[4 * MAXN + 5];
int lazyAdd[4 * MAXN + 5], lazyMul[4 * MAXN + 5];
// 基于a[]建线段树
void build(int now, int l, int r)
{
    lazyAdd[now] = 0;
    lazyMul[now] = 1;
    if (l == r)
    {
        t[now] = a[l];
        return;
    }
    int mid = (l + r) / 2;
    build(now * 2, l, mid);
    build(now * 2 + 1, mid + 1, r);
    t[now] = t[now * 2] + t[now * 2 + 1];
    t[now] %= p;
}
void down(int now, int l, int r)
{
    int mid = (l + r) / 2;
    // 左子树 now*2,l,mid
    // 右子树 now*2+1,mid+1,r
    int add = lazyAdd[now];
    int mul = lazyMul[now];

    lazyMul[now * 2] *= mul;
    lazyMul[now * 2] %= p;
    lazyAdd[now * 2] *= mul;
    lazyAdd[now * 2] %= p;
    t[now * 2] *= mul;
    t[now * 2] %= p;
    lazyAdd[now * 2] += add;
    lazyAdd[now * 2] %= p;
    t[now * 2] += (mid - l + 1) * add;
    t[now * 2] %= p;

    lazyMul[now * 2 + 1] *= mul;
    lazyMul[now * 2 + 1] %= p;
    lazyAdd[now * 2 + 1] *= mul;
    lazyAdd[now * 2 + 1] %= p;
    t[now * 2 + 1] *= mul;
    t[now * 2 + 1] %= p;
    lazyAdd[now * 2 + 1] += add;
    lazyAdd[now * 2 + 1] %= p;
    t[now * 2 + 1] += (r - mid) * add;
    t[now * 2 + 1] %= p;

    lazyAdd[now] = 0;
    lazyMul[now] = 1;
}
// 当前做到了树上的now号节点，当前节点对应的区间为 [l,r]
// 需要给 [x,y] 这些数都加 z
void update_add(int now, int l, int r, int x, int y, int z)
{
    if (x <= l && r <= y)
    {
        t[now] += (r - l + 1) * z;
        t[now] %= p;
        lazyAdd[now] += z;
        lazyAdd[now] %= p;
        return;
    }
    int mid = (l + r) / 2;
    // 左子树 now*2,l,mid
    // 右子树 now*2+1,mid+1,r
    down(now, l, r); // 把之前欠的结清
    if (x <= mid)
        update_add(now * 2, l, mid, x, y, z);
    if (y > mid)
        update_add(now * 2 + 1, mid + 1, r, x, y, z);
    t[now] = t[now * 2] + t[now * 2 + 1];
    t[now] %= p;
}
void update_mul(int now, int l, int r, int x, int y, int z)
{
    if (x <= l && r <= y)
    {
        t[now] *= z;
        t[now] %= p;
        lazyMul[now] *= z;
        lazyMul[now] %= p;
        lazyAdd[now] *= z;
        lazyAdd[now] %= p;
        return;
    }
    int mid = (l + r) / 2;
    // 左子树 now*2,l,mid
    // 右子树 now*2+1,mid+1,r
    down(now, l, r); // 把之前欠的结清
    if (x <= mid)
        update_mul(now * 2, l, mid, x, y, z);
    if (y > mid)
        update_mul(now * 2 + 1, mid + 1, r, x, y, z);
    t[now] = t[now * 2] + t[now * 2 + 1];
    t[now] %= p;
}
int query(int now, int l, int r, int x, int y)
{
    //[l,r] 是 [x,y] 的子区间
    if (x <= l && r <= y)
        return t[now];
    int mid = (l + r) / 2;
    // 左子树 now*2,l,mid
    // 右子树 now*2+1,mid+1,r
    down(now, l, r); // 把之前欠的结清
    int res = 0;
    if (x <= mid)
        res += query(now * 2, l, mid, x, y);
    if (y > mid)
        res += query(now * 2 + 1, mid + 1, r, x, y);
    return res % p;
}

signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> m >> p;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
    }
    build(1, 1, n);
    while (m--)
    {
        int op, x, y, z;
        cin >> op;
        if (op == 1)
        {
            cin >> x >> y >> z;
            update_mul(1, 1, n, x, y, z);
        }
        else if (op == 2)
        {
            cin >> x >> y >> z;
            update_add(1, 1, n, x, y, z);
        }
        else if (op == 3)
        {
            cin >> x >> y; // cout sum(a[x]~a[y])
            cout << query(1, 1, n, x, y) << "\n";
        }
    }
    return 0;
}
```

## 动态开点线段树

下面是权值线段树的单点修改，区间查询，以及基础的线段树上二分。

如果要进行区间修改，一般来说会标记永久化。

[中位数](https://oj.33dai.cn/p/P1168)

### $O(\log^2n)$ 查询

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 100000;
const int MAXAI = 1'000'000'000;
const int MAXTOT = 30 * MAXN;
int n, m;
int a[MAXN + 5];
// 动态开点线段树维护权值数组
int tot = 1;                            // 节点数量
int lson[MAXTOT + 5], rson[MAXTOT + 5]; // 左右子节点
int t[MAXTOT + 5];
void update(int now, int l, int r, int x, int y)
{
    if (l == r)
    {
        t[now] += y;
        return;
    }
    int mid = (l + r) / 2;
    if (x <= mid)
    {
        if (!lson[now])
            lson[now] = ++tot;
        update(lson[now], l, mid, x, y);
    }
    if (x > mid)
    {
        if (!rson[now])
            rson[now] = ++tot;
        update(rson[now], mid + 1, r, x, y);
    }
    t[now] = t[lson[now]] + t[rson[now]];
}
int query(int now, int l, int r, int x, int y)
{
    if (x <= l && r <= y)
        return t[now];
    int mid = (l + r) / 2;
    int res = 0;
    if (x <= mid && lson[now])
        res += query(lson[now], l, mid, x, y);
    if (y >= mid + 1 && rson[now])
        res += query(rson[now], mid + 1, r, x, y);
    return res;
}
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    // 找中位数
    for (int i = 1; i <= n; i++)
    {
        update(1, 1, MAXAI, a[i], 1);
        if (i % 2 == 0)
            continue;
        int k = i / 2 + 1;
        // 找到排名为k的数是几
        int l = 1;
        int r = MAXAI;
        int ans = -1;
        while (l <= r)
        {
            int mid = (l + r) / 2;
            if (query(1, 1, MAXAI, 1, mid) >= k)
            {
                ans = mid;
                r = mid - 1;
            }
            else
                l = mid + 1;
        }
        cout << ans << "\n";
    }
    return 0;
}

```


### $O(\log n)$ 查询

```cpp
int dfs(int now, int l, int r, int k)
{
    if (l == r)
        return l;
    int mid = (l + r) / 2;
    if (t[lson[now]] >= k)
        return dfs(lson[now], l, mid, k);
    return dfs(rson[now], mid + 1, r, k - t[lson[now]]);
}
...

        int k = i / 2 + 1;
        // 找到排名为k的数是几
        cout << dfs(1, 0, MAXAI, k) << "\n";
```