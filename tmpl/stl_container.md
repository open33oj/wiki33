# STL 容器库常用内容

[C++ 文档：容器库](https://cpp.33dai.wiki/reference/zh/cpp/container.html)

## vector（向量、动态数组）

- `vector<int> a;`：定义了一个名字叫作 `a`，可以存 `int` 的动态数组。初始数组为空。
- `vector<int> a[100];`：定义了 `100` 个动态数组 `a[0]~a[99]` 都是动态数组。
- `a.push_back(x);`： 添加元素，把 `x` 添加到动态数组末尾。
- `a[pos]`：访问元素，访问下标为 `pos` 的元素。
- `a.clear()`：清空数组，清空整个数组，数组大小也归零了。
- `a.resize(100)`：更改数组大小，把数组大小修改为 `100`。
- `a.size()`：当前数组大小。
- `a.empty()`：数组是否为空，数组是空的就返回真。
- `a.begin()`：起始位置迭代器。
- `a.end()`：结束位置迭代器。

## deque（双端队列）

- `deque<int> q;`：定义了一个名字叫作 `q` 的双端队列。
- `q.front()`：返回队头（的引用）。
- `q.back()`：返回队尾（的引用）。
- `q.push_back(x);`：从队列末尾放入 `x`。
- `q.push_front(x);`：从队列开头放入 `x`。
- `q.pop_back();`：从队列末尾弹出元素。
- `q.pop_front();`：从队列开头弹出元素。
- `q[pos]`：访问元素，访问下标为 `pos` 的元素。
- `q.clear()`：清空双端队列，双端队列大小也归零了。
- `q.size()`：当前队列大小。
- `q.empty()`：队列是否为空，队列是空的就返回真。

## stack（栈）

- `stack<int> sta;`：定义了一个名字叫作 `sta` 的栈。
- `sta.top()`：返回栈顶（的引用）。
- `sta.push(x);`：把 `x` 压入栈。
- `sta.pop();`：弹出栈顶元素。
- `sta.size()`：当前栈大小。
- `sta.empty()`：栈是否为空，栈是空的就返回真。

## queue（队列）

- `queue<int> q;`：定义了一个名字叫作 `q` 的队列。
- `q.front()`：返回队头（的引用）。
- `q.back()`：返回队尾（的引用）。
- `q.push(x);`：把 `x` 压入队列。
- `q.pop();`：弹出队头。
- `q.size()`：当前队列大小。
- `q.empty()`：队列是否为空，队列是空的就返回真。

## 结构体重载运算符
- 重载运算的基本结构，一般参数会使用引用的形式来提高效率，叠加常量标记提高效率避免修改。
```
返回类型 类名::operator运算符(参数列表) {
    // 实现运算符逻辑
}
```
- 操作样例（重载 `<` 运算符：用于排序）
    - 定义结构体
    ```cpp
    struct Point {
        int x;  // x坐标
        int y;  // y坐标
    };
    ```
    - 重载 `<` 运算符
    ```cpp
    bool operator<(const Point& a, const Point& b) {
        if (a.x != b.x) return a.x < b.x;  // 先比x
        return a.y < b.y;                  // x相同再比y
    }
    ```
    - 排序示例（依赖 `<` 运算符）
    ```cpp
    vector<Point> points = {p3, p1, p2};
    sort(points.begin(), points.end()); 
    ```

## map（映射）/unordered_map

`map` 采用红黑树实现，`unordered_map` 则是采用哈希的形式。

- `map<int, string> m;`：定义了一个名字叫作 `m` 的映射，键是 `int` 类型，值是 `string` 类型（默认按键升序排列）。
- `map<string, int> mp[50];`：定义了 `50` 个映射 `mp[0]~mp[49]` ,每个都是键为 `string`，值为 `int` 的映射。
- `m.insert(pair<int, string>(1, "apple"));`：插入键值对，将键为 `1` 、值为 `"apple"` 的键值对插入映射（键重复时插入失败）。
- `m.insert({2, "banana"});`：插入键值对，用初始化列表形式插入键为 2、值为 "banana" 的键值对。
- `m[3] = "orange";`：插入或修改值，若键 `3` 不存在则插入（键 `3`，值 `"orange"` ）；若存在则修改值为 `"orange"`。
- `m.find(2);`：查找键，返回指向键为 `2` 的键值对的迭代器，若不存在，返回 `m.end()` 。
- `m.erase(1);`：删除键值对，删除键为 `1` 的键值对。
- `m.clear();`：清空映射，删除所有键值对，大小归零。
- `m.size();`：当前映射中键值对的数量。
- `m.empty();`：映射是否为空，为空返回真。
- `m.begin();`：起始位置迭代器（指向第一个键值对）。
- `m.end();`：结束位置迭代器（指向最后一个键值对的下一位）。
- 枚举 `map`
    - 使用迭代器枚举
    ```cpp
    for(map<int, string>::iterator it = m.begin(); it != m.end(); it++)
        cout << (*it).first << " " << (*it).second << "\n"; 
    ```
    - 自动推导类型
    ```cpp
    for(auto it = m.begin(); it != m.end(); it++)
        cout << (*it).first << " " << (*it).second << "\n"; 
    ```
    - 使用基于范围的 `for` 循环
    ```cpp
    for(auto now : m)
        cout << now.first << " " << now.second << "\n"; 
    ```


## set（集合）/unordered_set

`set` 采用红黑树实现，`unordered_set` 则是采用哈希的形式。

- `set<int> s;`：定义了一个名字叫作 `s` 的集合，存储 `int` 类型元素（默认升序排列，元素唯一）。
- `set<string> ss[30];`：定义了 `30` 个集合 `ss[0]~ss[29]`，每个都是存储 `string` 类型元素的集合。
- `s.insert(5);`：插入元素，将 `5` 插入集合（若 `5` 已存在则插入失败）。
- `s.find(5);`：查找元素，返回指向元素 `5` 的迭代器；若不存在，返回 `s.end()`。
- `s.erase(5);`：删除元素，删除集合中的元素 `5` 。
- `s.clear();`：清空集合，删除所有元素，大小归零。
- `s.size();`：当前集合中元素的数量。
- `s.empty();`：集合是否为空，为空返回真。
- `s.begin();`：起始位置迭代器（指向第一个元素）。
- `s.end();`：结束位置迭代器（指向最后一个元素的下一位）。
- 枚举 `set`
    - 使用迭代器枚举
    ```cpp
    for(set<int>::iterator it = s.begin(); it != s.end(); it++)
        cout << (*it) << "\n"; 
    ```
    - 自动推导类型
    ```cpp
    for(auto it = s.begin(); it != s.end(); it++)
        cout << (*it) << "\n"; 
    ```
    - 使用基于范围的 `for` 循环
    ```cpp
    for(auto now : s)
        cout << now << "\n"; 
    ```

## priority_queue（优先队列）

- `priority_queue<int> pq;`：定义一个名为 `pq` 的优先队列，存储 `int` 类型元素，默认最大元素在队顶（大顶堆）。
- `priority_queue<int, vector<int>, greater<int>> pq;`：定义一个小顶堆的优先队列（最小的元素在队顶），需指定底层容器（通常为 `vector` ）和比较器 `greater<int>` 。
- `pq.push(x);`：插入元素，将 `x` 放入优先队列，插入后队列会自动重新排序。
- `pq.top();`：返回队顶元素（的引用），即当前优先级最高的元素（大顶堆返回最大元素，小顶堆返回最小元素）。
- `pq.pop();`：弹出队顶元素，删除当前优先级最高的元素（弹出后队列会重新排序）。
- `pq.size();`：返回当前优先队列中元素的数量。
- `pq.empty();`：判断队列是否为空，为空返回真（ `true` ），否则返回假（ `false` ）。

