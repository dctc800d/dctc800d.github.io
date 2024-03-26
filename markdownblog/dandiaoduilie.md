# [滑动窗口 /【模板】单调队列](http://luogu.com.cn/problem/P1886)

## 题目描述

有一个长为 $n$ 的序列 $a$，以及一个大小为 $k$ 的窗口。现在这个从左边开始向右滑动，每次滑动一个单位，求出每次滑动后窗口中的最大值和最小值。

例如，对于序列 $[1,3,-1,-3,5,3,6,7]$ 以及 $k = 3$，有如下过程：

$$
\def\arraystretch{1.2}\begin{array}{|c|c|c|}\hline\textsf{窗口位置} & \textsf{最小值} & \textsf{最大值} \\ \hline\verb![1 3 -1] -3 5 3 6 7 ! & -1 & 3 \\ \hline\verb! 1 [3 -1 -3] 5 3 6 7 ! & -3 & 3 \\ \hline\verb! 1 3 [-1 -3 5] 3 6 7 ! & -3 & 5 \\ \hline\verb! 1 3 -1 [-3 5 3] 6 7 ! & -3 & 5 \\ \hline\verb! 1 3 -1 -3 [5 3 6] 7 ! & 3 & 6 \\ \hline\verb! 1 3 -1 -3 5 [3 6 7]! & 3 & 7 \\ \hline\end{array}
$$

## 输入格式

输入一共有两行，第一行有两个正整数 $n,k$。
第二行 $n$ 个整数，表示序列 $a$

## 输出格式

输出共两行，第一行为每次窗口滑动的最小值

第二行为每次窗口滑动的最大值

## 样例 #1

### 样例输入 #1

```
8 3
1 3 -1 -3 5 3 6 7
```

### 样例输出 #1

```
-1 -3 -3 -3 3 3
3 3 5 5 6 7
```

## 提示

【数据范围】

对于 $50\%$ 的数据，$1 \le n \le 10^5$；  

对于 $100\%$ 的数据，$1\le k \le n \le 10^6$，$a_i \in [-2^{31},2^{31})$。

---

---

> 如果一个选手比你小还比你强，你就可以AFO了。

本题是一道单调队列的模板题。

首先澄清一下什么是单调队列。

> 顾名思义，单调队列的重点分为**单调**和**队列**。
> 
> **单调**指的是元素的**规律**——递增（或递减）。
> 
> **队列**指的是元素只能从队头和队尾进行操作。
> 
> ——OI-Wiki

但是，单调队列的队列又和普通的队列不一样。

普通的队列是FIFO的，也就是说只能从队头入队，从队头出队。

单调队列的队列是**双端队列**。

双端队列是什么意思呢？

**双端队列**的队头和队尾都可以出队及入队。

---

好，说完单调队列的队列和普通的队列的区别，再说一说**单调**。

换个方法讲，当序列$r$满足以下条件时，就可以成为单调的：

1. $\forall i,0<i<|r|,r_i<r_{i+1}$（单调递增）；

2. $\forall i,0<i<|r|,r_i\le r_{i+1}$（不严格单调递增）；

3. $\forall i,0<i<|r|,r_i>r_{i+1}$（单调递减）；

4. $\forall i,0<i<|r|,r_i\ge r_{i+1}$（不严格单调递减）。

---

解释完单调队列，再来讲这道题。

当我们不知道单调队列时，我们会用$O(nm)$的时间复杂度来写这道题：

```cpp
#include <bits/extc++.h>
using namespace std;
using namespace __gnu_pbds;
int n, m, r[1000006], s[1000006];
int main()
{
    cin >> n >> m;
    for (int i{1}; i <= n; i++)
        cin >> r[i];
    for (int i{1}; i + m <= n + 1; i++)
    {
        int a{r[i]}, b{r[i]};
        for (int j{i + 1}; j < i + m; j++)
            if (r[j] > a)
                a = r[j];
            else if (r[j] < b)
                b = r[j];
        cout << b << ' ';
        s[i] = a;
    }
    cout << endl;
    for (int i{1}; i + m <= n + 1; i++)
        cout << s[i] << ' ';
    return 0;
}
```

而用单调队列，我们就可以用$O(n-m)$的时间复杂度完成这道题。

**单调队列**，其中心思想就是在队列里保存**可能**成为区间极值的元素。

对于每一个新元素，都会经历一下过程：

1. 如果队头元素超出作用域了，就把它pop掉；

2. 如果队尾元素的优先级小于当前元素，就把它pop掉，否则来到第4步；

3. 回到第3步；

4. 把当前元素从队尾push进来吧！

5. 如果现在可以产生答案了，就输出一下吧！

由此便可以实现单调队列。

---

Show一下代码：

```cpp
#include <bits/extc++.h>
using namespace std;
using namespace __gnu_pbds;
int n, k, r[1000006];
deque<int> d;
int main()
{
    cin >> n >> k;
    for (int i{1}; i <= n; i++)
        cin >> r[i];
    for (int i{1}; i <= n; i++)
    {
        if (!d.empty() && i - d.front() >= k)
            d.pop_front();
        while (!d.empty() && r[d.back()] > r[i])
            d.pop_back();
        d.push_back(i);
        if (i >= k)
            cout << r[d.front()] << ' ';
    }
    d.clear();
    cout << endl;
    for (int i{1}; i <= n; i++)
    {
        if (!d.empty() && i - d.front() >= k)
            d.pop_front();
        while (!d.empty() && r[d.back()] < r[i])
            d.pop_back();
        d.push_back(i);
        if (i >= k)
            cout << r[d.front()] << ' ';
    }
    return 0;
}
```

---

最后，谢谢大家的观看！

**谢谢[谢博士](https://www.luogu.com.cn/user/63075)的指导！**
