---
title: 新春算法马拉松比赛
order: 5.5
---

## A.A乘B小于C吗

### 分析

- **难度**：简单条件判断语句
- **子任务 1（30 分）**：因为 $a$ 为 $1$，现在只需要比较 $b$ 和 $c$ 的大小关系了。
- **子任务 2（30 分）**：因为 $a\times b\le c$，所以答案从三个变成两个了，更简单一点了。但这意义不大，这个子任务最大的作用是隐含的 $a\times b\le c\le 10^9$，即只需要用 `int` 处理就好。
- **子任务 3（40 分）**：需要注意 $a\times b$ 是有可能超过 `int` 的，所以需要用 `long long`。当然也可以先判断 $a$ 与 $c/b$ 的关系，来规避这个问题，但是细节需要注意。 

### 满分参考代码

```cpp
#include <bits/stdc++.h>
using namespace std;
int main()
{
    int a, b, c;
    cin >> a >> b >> c;
    long long ans = (long long)a * b;
    if (ans < c)
        cout << '<';
    else if (ans == c)
        cout << '=';
    else if (ans > c)
        cout << '>';
    return 0;
}
```

## B.jonny 办比赛

### 分析

- **难度**：基础求和算法
- **子任务 1（30 分）**：只有一个人，这个人考多少分就收多少报名费就好。
- **子任务 2（30 分）**：每个人的得分在 $0\sim 100$ 以内，不会超 `int` 了。（33DAI 一直在提醒大家要注意算数据范围）
- **子任务 3（40 分）**：求和后，算平均数上取整即可。

### 满分参考代码

```cpp
#include <bits/stdc++.h>
using namespace std;
int main()
{
    int n, ai;
    cin >> n;
    long long sum = 0;
    for (int i = 1; i <= n; i++)
    {
        cin >> ai;
        sum += ai;
    }
    cout << sum / n + (sum % n > 0);
    return 0;
}
```

## C.分帽子

### 分析

- **难度**：数组综合运用。
- **子任务 1（30 分）**：$1\le n\le 1000$，可以使用 $O(n^2)$ 的做法通过。可以直接枚举所有数对，遇到一样的颜色就答案加一，然后把两个颜色改为非法颜色 `-1` 就好（后续判断遇到 `-1` 就跳过）。
- **子任务 2（30 分）**：$1\le a_i\le 1000$，可以直接开计数数组（或者有的同学学到的桶、或者叫做权值数组），计算每个颜色有几顶帽子。所有颜色的帽子数整除以 $2$ 再求和就好。  
- **子任务 3（40 分）**：这里不能再开计数数组了。可以通过 `map<int,int>` 处理。也可以直接对所有帽子排序，这样所有相同颜色的帽子就排到一起了。从前往后枚举计数就好。

### 满分参考代码

```cpp
#include <bits/stdc++.h>
using namespace std;
int n, ans;
int a[112345];
int main()
{
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    sort(a + 1, a + n + 1);
    for (int i = 1; i + 1 <= n;)
    {
        if (a[i] == a[i + 1])
        {
            ans++;
            i += 2;
        }
        else
        {
            i += 1;
        }
    }
    cout << ans;
    return 0;
}
```

## D.TooY0ung的16位内存系统

### 分析

首先这题的数据范围给的是十六进制，比较不好估算规模。一个比较关键的数据规模是：$0x10000 = 65536$，即已分配的块的范围最多是这么多，并且每次访问的也最多是这些位置。

很容易想到直接暴力给所有已分配的块标记上，然后读写的地址看看是否被标记过了就好。唯一的难点只在十六进制的转换上了。

有同学可能会想在极限数据下，$n=65536$ 次，每次最多 $0\sim 65535$ 的标记会不会超时呢？实际上是完全不会的，不同于“校门外的树”之类的区间覆盖题目，这题保证了分配时**数据保证不会有重合**，所以 $n$ 次分配块的标记加在一起也不超过 $65536$ 次标记。

- **难度**：进制转换，暴力枚举。
- **20 分**：其实没太看懂这 $20$ 分给的是啥分数。最终得分也只有 $0$ 和 $100$ 两种。$k$ 从 $10^5$ 降到 $10^4$ 感觉意义不大。

### 满分参考代码

#### TooY0ung 的代码

```cpp
#include<bits/stdc++.h>
using namespace std;
int helper(char a) 
{
    if(a>='A') 
	{
        return a-'A'+10;
    }
    return a-'0';
}
int a[100010];
int main() 
{
    int n,q;
	cin>>n>>q;
    for(int i=1;i<=n;i++) 
	{
        string s1,s2;
		cin>>s1>>s2;
        int l=0,r=0;
        for(int i=3;i>=0;i--) 
		{
            l+=helper(s1[i])*(1<<(4*(3-i)));
        }
        for(int i=3;i>=0;i--) 
		{
            r+=helper(s2[i])*(1<<(4*(3-i)));
        }
        for(int i=l;i<=r;i++)
		{
			a[i]=1;
        }
    }
    while(q--)
	{
        string s;
		cin>>s;
        int t=0;
        for(int i=3;i>=0;i--) 
		{
            t+=helper(s[i])*pow(16,(3-i));
        }
        if(!a[t])
		{
			cout<<"RE"<< endl;
            return 0;
        }
    }
    cout<<"AC"<<endl;
    return 0;
}
```


## E.TooY0ung的数学简单题

### 分析

首先需要了解关于同余的基础知识，为了方便理解这里我用 C++ 的语法来描述。

假设这题需要找到的两个数分别为 $x,y(x\gt y)$，那么显然需要 $(x-y)\% m == 0$。

单看这个式子有点不太好找到突破口，显然上面式子意味着 $x\%m==y\%m$。问题就转换成了找到一个 $x$，使得它除以 $m$ 的余数等于之前某个小于 $x$ 的 $y$ 除以 $m$ 的余数。

因此可以从小到大枚举数列中的每个数，依次算出来除以 $m$ 的余数，然后检查之前是否出现过即可。假设第 $i$ 个数除以 $m$ 的余数为 $a_i$，很容易得到递推式 $a_i=(a_{i-1}*10+5)\%m$

还有另一种思路，由于最终的差一定是 $5555000000$ 这样的模式，即 $1111\times 5\times 10^6 = 1111\times 5^7\times 2^6$。容易发现前面的一串 $1111$ 不可能提供因子 $2$ 和 $5$。所以可以把 $m$ 的因子 $2,5$ 剔除，得到最终差末尾几个 $0$，这样就只需要枚举前面的几个 $1$ 能达成 $m$ 剩余部分的倍数了。

- **难度**：难点主要在数学，需要一些基础的数学同余的概念。
- **10 分**：此时 $m$ 在 $1000$ 以内，所以余数最大范围是 $0\sim 999$。数列最多前 $1001$ 项就必然出现余数相同的项。只需要算出数列前 $1001$ 项除以 $m$ 的余数，然后 $O(n^2)$ 枚举找到是哪两个数即可。
- **100 分**：可以使用 $vis_i$ 记录上一次出现余数 $i$ 是数列的第几项。这样就能 $O(1)$ 判断当前项的余数是否出现过了。最多 $m+1$ 次的循环就能找到答案了。

### 满分参考代码

#### TooY0ung 的代码

```cpp
#include<bits/stdc++.h>
using namespace std;
int m,vis[10000010],res;
int main()
{
	//ios::sync_with_stdio(false);cin.tie(0);cout.tie(0);
	cin>>m;
	for(int i=1;;i++)
	{
		res=(res*10+5)%m;
		if(vis[res])
		{
			for(int j=i;j>vis[res];j--) cout<<'5';
			for(int j=vis[res];j>=1;j--) cout<<'0'; 
			return 0;
		}
		vis[res]=i;
	}
	return 0;	
}
```

#### 王彬铸的代码

这就是题解部分说的先剔除因子 $2,5$ 来找到答案后面有几个 $0$ 再来处理的做法。

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long int LL;
int main(){
	LL t,x,i,n=1,m,zero=0;
	cin >> m;
	while(m%10==0){
		m /= 10;
		zero++;
	}
	while(m%2==0){
		m /= 2;
		zero++;
	}
	x = t = 50 % m;
	for(n=1 ; t ; n++){
		x = x * 10 % m;
		t = (t+x) % m;
	}
	for(i=1 ; i<=n ; i++)
		cout << "5";
	if(zero){
		while(zero--)
			cout << "0";
	}else{
		cout << "0";
	}
	return 0;
}
```

## F.TooY0ung的贪吃蛇

### 分析

手玩一下很容易得出结论，答案只有可能是 $2n-2$ 或者 $n$，如果刚好撞到四个顶点就是后者。

那这个结论能不能证明呢？其实是可以的。

答案为 $n$ 的情况显而易见。其他情况下很容易发现每次碰撞都是一个 $45$ 度的碰撞，那么与顶点刚好能构成一个等腰直角三角形，容易看到其中隐含着一个对称的关系。最终必然就对称回原点了。

- **难度**：读题读懂之后，部分分就是暴力在二维数组上处理，满分需要自己多尝试找结论。
- **40 分**：二维数组上模拟走路即可，有同学会被题面给的时间吓到，其实标记一下走过的位置，经历了重复的状态就停下就好。
- **100 分**：根据上面的结论处理即可。

### 满分参考代码

#### TooY0ung 的代码

```cpp
#include<bits/stdc++.h>
int n,x,y;
string d;
int main()
{
	cin>>n>>x>>y>>d;
	if(x+y==n+1)
	{
		if(d=="DL"||d=="UR") cout<<n<<endl;
		else cout<<2*n-2<<endl;
	}
	else if(x==y)
	{
		if(d=="DR"||d=="UL") cout<<n<<endl;
		else cout<<2*n-2<<endl;
	}
	else cout<<2*n-2<<endl;
	return 0;	
}
```
