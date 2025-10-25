# 线性代数相关常用模板


## 矩阵快速幂模板

[P1939. 矩阵加速（数列）](https://oj.33dai.cn/p/P1939)

已知一个数列 $a$，它满足：  

$$
a_x=
\begin{cases}
 1 & x \in\{1,2,3\}\\ 
 a_{x-1}+a_{x-3} & x \geq 4
\end{cases}
$$

求 $a$ 数列的第 $n$ 项对 $10^9+7$ 取余的值。

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int MAXR = 3; // 矩阵最大行数
const int MAXC = 3; // 矩阵最大列数
int MOD = 1000000000 + 7;
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
    int T;
    cin >> T;
    // 构造矩阵
    Mat now;
    now.r = 1, now.c = 3;
    now.m[1][1] = 1, now.m[1][2] = 1, now.m[1][3] = 1;
    Mat ans;
    ans.r = ans.c = 3;
    ans.m[1][1] = 0, ans.m[1][2] = 0, ans.m[1][3] = 1;
    ans.m[2][1] = 1, ans.m[2][2] = 0, ans.m[2][3] = 0;
    ans.m[3][1] = 0, ans.m[3][2] = 1, ans.m[3][3] = 1;
    while (T--)
    {
        int x;
        cin >> x;
        // 计算答案
        Mat ans_ = quick_pow(ans, x - 1);
        Mat now_ = now * ans_;
        cout << now_.m[1][1] % MOD << "\n";
    }
    return 0;
}
```

## 高斯消元模板

```cpp
//------------高斯消元模板-Start--------------
const double eps = 1e-12;
double a[105][105]; // 高斯消元的方程，a[i][0] 记录第 i 个方程的主元
// 无解：返回 -1
// 多解：返回 0
// 有解：返回 1
int gauss(int x)
{
    int line = 1;                // 当前方程的编号
    for (int i = 1; i <= x; i++) // 主元编号
    {
        // 当前方程是第 line 个方程
        // 试图把当前方程主元变量(第i个变量)系数调整为 1
        // 找到下面主元系数最大的方程
        int row = line;
        for (int j = line + 1; j <= x; j++)
            if (fabs(a[row][i]) < fabs(a[j][i]))
                row = j;
        // 用系数最大的那个作为当前方程
        if (row != line)
            std::swap(a[row], a[line]);
        // 如果最大的系数都是 0 了，下面所有方程的就都是 0 了，不用往下调整了
        double div1 = a[line][i];
        if (fabs(div1) < eps)
            continue;
        a[line][0] = i;
        // 调整当前方程
        for (int j = 1; j <= x + 1; j++)
            a[line][j] /= div1;
        // 用当前方程调整下面的方程，把下面方程的第 i 个系数小调
        for (int j = line + 1; j <= x; j++)
        {
            double div2 = a[j][i];
            for (int k = 1; k <= x + 1; ++k)
                a[j][k] -= a[line][k] * div2;
        }
        line++;
    }
    // 判无解和多解
    if (line <= x)
    {
        for (int i = line; i <= x; i++)
            if (fabs(a[i][x + 1]) > eps)
                return -1;
        return 0;
    }
    // 有唯一解时依次处理
    for (int i = x; i >= 1; i--)
    {
        for (int j = i - 1; j >= 1; j--)
        {
            a[j][x + 1] -= a[j][i] * a[i][x + 1];
            a[j][i] = 0;
        }
    }
    return 1;
}
//------------高斯消元模板-End--------------
```

## 线性基模板

[P3812. 【模板】线性基](https://oj.33dai.cn/p/P3812)

给定 $n$ 个整数（数字可能重复），求在这些数中选取任意个，使得他们的异或和最大。

```cpp
#include <bits/stdc++.h>
using namespace std;
// template from men.ci
const int MAXL = 50;
struct LinearBasis
{
    long long a[MAXL + 1];
    LinearBasis()
    {
        std::fill(a, a + MAXL + 1, 0);
    }
    LinearBasis(long long *x, int n)
    {
        build(x, n);
    }
    void insert(long long t)
    {
        for (int j = MAXL; j >= 0; j--)
        {
            if (!t)
                return;
            if (!(t & (1ll << j)))
                continue;
            if (a[j])
                t ^= a[j];
            else
            {
                for (int k = 0; k < j; k++)
                    if (t & (1ll << k))
                        t ^= a[k];
                for (int k = j + 1; k <= MAXL; k++)
                    if (a[k] & (1ll << j))
                        a[k] ^= t;
                a[j] = t;
                break;
            }
        }
    }
    // 数组 x 表示集合 S，下标范围 [1...n]
    void build(long long *x, int n)
    {
        std::fill(a, a + MAXL + 1, 0);
        for (int i = 1; i <= n; i++)
        {
            insert(x[i]);
        }
    }
    long long queryMax()
    {
        long long res = 0;
        for (int i = 0; i <= MAXL; i++)
            res ^= a[i];
        return res;
    }
    void mergeFrom(const LinearBasis &other)
    {
        for (int i = 0; i <= MAXL; i++)
            insert(other.a[i]);
    }
    static LinearBasis merge(const LinearBasis &a, const LinearBasis &b)
    {
        LinearBasis res = a;
        for (int i = 0; i <= MAXL; i++)
            res.insert(b.a[i]);
        return res;
    }
} lb;
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    long long n, x;
    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        cin >> x;
        lb.insert(x);
    }
    cout << lb.queryMax() << "\n";
    return 0;
}
```