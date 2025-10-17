# 基础框架

## 简单框架

```cpp
#include <bits/stdc++.h>
using namespace std;
int main()
{
    


    return 0;
}
```
在中间空白部分写你的代码

## 全局 `int` 改 `long long` 框架

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
signed main()
{
    


    return 0;
}
```

`define` 是简单全局关键字替换，因为 `main` 函数返回值必须为 $32$ 位整数，所以为了避免 `int main()` 被改成 `long long main()`，就改成了 `signed main()`。

在比赛环境中 `signed` 和 `int` 完全等价，都是 $32$ 位有符号整数。

## 文件输入输出框架

NOI 系列赛事很多需要使用文件的形式输入输出。

```cpp
#include <bits/stdc++.h>
using namespace std;
int main()
{
    freopen("题目英文名.in", "r", stdin);
    freopen("题目英文名.out", "w", stdout);


    return 0;
}
```

题目英文名在正式比赛中在试卷第一页就会有，在 OJ 上一般标题下面会有文件 IO 的名称规则。

## 输入输出加速框架

```cpp
#include <bits/stdc++.h>
using namespace std;
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);

    return 0;
}
```

