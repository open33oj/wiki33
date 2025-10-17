# 排序算法

## 选择排序

发现了小的就交换

```cpp
#include <bits/stdc++.h>
using namespace std;
int n;
int a[1005];
int main()
{
    //输入
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    //选择排序
    for (int i = 1; i <= n; i++)
        for (int j = i + 1; j <= n; j++)
            if (a[j] < a[i])
                swap(a[j], a[i]);
    //输出
    for (int i = 1; i <= n; i++)
        cout << a[i] << " ";
    return 0;
}
```

## 冒泡排序

无脑做 $n-1$ 轮

```cpp
#include <bits/stdc++.h>
using namespace std;
int n;
int a[1005];
int main()
{
    //输入
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    //冒泡排序
    for (int i = 1; i <= n - 1; i++)
        for (int j = 1; j <= n - 1; j++)
            if (a[j] > a[j + 1])
                swap(a[j], a[j + 1]);
    //输出
    for (int i = 1; i <= n; i++)
        cout << a[i] << " ";
    return 0;
}
```

## 插入排序

```cpp
#include <bits/stdc++.h>
using namespace std;
int n;
int a[1005];
int main()
{
    //输入
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    //插入排序
    for (int i = 2; i <= n; i++)
        for (int j = i; j >= 2; j--)
            if (a[j] < a[j - 1])
                swap(a[j], a[j - 1]);
    //输出
    for (int i = 1; i <= n; i++)
        cout << a[i] << " ";
    return 0;
}
```

## 计数排序

```cpp
#include <bits/stdc++.h>
using namespace std;
int n;
int a[1005];
int cnt[2005];
int main()
{
    //输入
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    //计数排序
    for (int i = 1; i <= n; i++)
        cnt[a[i]]++;
    //输出
    for (int i = 1; i <= 2000; i++)
        for (int j = 1; j <= cnt[i]; j++)
            cout << i << " ";
    return 0;
}
```

## 地精排序

```cpp
#include <bits/stdc++.h>
using namespace std;
int n;
int a[1005];
int main()
{
    // 输入
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> a[i];

    // 排序
    int pos = 1; // 地精的位置
    while (pos != n)
    {
        if (a[pos] <= a[pos + 1])
            pos++;
        else
        {
            swap(a[pos], a[pos + 1]);
            if (pos != 1)
                pos--;
        }
    }
    // 输出
    for (int i = 1; i <= n; i++)
        cout << a[i] << " ";
    return 0;
}
```