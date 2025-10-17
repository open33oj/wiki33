# 循环语句

## while 语句

```cpp
while (条件表达式)
{
    条件成立时重复执行代码
}
```

## for 语句

- 在 `while` 的基础上，可以指定循环开始之前，执行初始化语句：`int i = 1;`
- 可以指定每次循环结束时，进行一个操作：`i++`

下面的代码可以重复执行 $n$ 次花括号的内容。

```cpp
for (int i = 1; i <= n; i++) 
{
    条件成立时重复执行代码
}
```

## 高级控制

- `break;` 立刻结束循环
- `continue;` 立刻进行下一次循环
- `return 0;` 退出函数并返回0
- `exit(0);` 结束程序并返回0

### break

```cpp
for (int i = 1; i <= 5; i++)
{
    cout << i;
    if (i == 3)
        break;
    cout << i;
}

```

上面代码输出 `11223`

### continue

```cpp
for (int i = 1; i <= 5; i++)
{
    cout << i;
    if (i == 3)
        continue;
    cout << i;
}
```

上面代码输出 `112234455`