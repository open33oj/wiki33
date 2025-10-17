# 常见循环语句逻辑

## 最值，求和，计数

- `max(a, b)`：返回 $a,b$ 中的较**大**值。
- `min(a, b)`：返回 $a,b$ 中的较**小**值。

下面对于 $n$ 个 $1\sim 1000$ 范围内的数，执行几个经典操作。

### 求最大值

```cpp
int n, x, ans;
cin >> n;
ans = 0;
for (int i = 0; i < n; i++)
{
    cin >> x;
    if (x > ans)
        ans = x;
}
cout << ans << "\n";
```

### 求最小值

```cpp
int n, x, ans;
cin >> n;
ans = 1001;
for (int i = 1; i <= n; i++)
{
    cin >> x;
    ans = min(ans, x);
}
cout << ans << "\n";
```

### 求和

```cpp
int n, x, ans;
cin >> n;
ans = 0;
for (int i = 1; i <= n; i++)
{
    cin >> x;
    ans += x;
}
cout << ans << "\n";
```

### 统计奇数个数

```cpp
int n, x, cnt;
cin >> n;
cnt = 0;
for (int i = 1; i <= n; i++)
{
    cin >> x;
    if (x % 2 == 1)
        cnt++;
}
cout << cnt << "\n";
```

## 判断质数

```cpp
int n;
cin >> n;

bool flag = ture;             // 一开始认为是质数
if (n <= 1)                   // 大于1的自然数
    flag = false;

for (int i = 2; i <= n - 1; i++) // 除了 1 和它本身以外（2 ~ 本身-1）
    if (n % i == 0)              // 不再有其他因数的自然数
        flag = false;
```

## 数位分解

- `% 10`：得到个位的数值
- `/ 10`：得到去掉个位后的数值
- 个位：`a % 10`
- 十位：`a / 10 % 10`
- 百位：`a / 100 % 10`
- 千位：`a / 1000 % 10`

```cpp
while (x > 0)
{
    cout << x % 10 << ",";
    x /= 10;
}
```

```cpp
for (int i = x; i > 0; i /= 10)
{
    cout << i % 10 << ",";
}
```
