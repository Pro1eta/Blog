---
title: 「DSA」Chapter2 - 算法分析
published: 2025-09-14
description: 算法分析
image: ""
tags:
  - Algorithm
category: 笔记
draft: true
pinned: false
lang: zh_CN
series: DSA in C++ 读书笔记
---

# Chapter 2 Algorithm Analysis

## Mathematics Basics

**Definition 2.1**

$T(N) = O(f(N))$ if there are positive constants $c$ and $n_0$ such that $T(N) \leq cf(N)$ when $N \geq n_0$.

**Definition 2.2**

$T(N) = \Omega(h(N))$ if there are positive constants $c$ and $n_0$ such that $T(N) \geq cg(N)$ when $N \geq n_0$.

**Definition 2.3**

$T(N) = \Theta(h(N))$ iff $T(N) = O(h(N))$ and $T(N) = \Omega(h(N))$

**Definition 2.4**

$T(N) = o(p(N))$ if, $\forall c > 0 ,\exists n_0$ such that $T(N) < cp(N)$ when $N > n_0$. Less formally, $T(N) = o(p(N))$ if $T(N) = O(p(N))$ and $T(n) \neq \Theta(p(N))$.

**Rule 1**

if $T_1(N) = O(f(N))$ and $T_2(N) = O(g(N))$, then
P
(a) $T_1(N) + T_2(N) = O(f(N)+g(N))=O(\max(f(N), g(N)))$

(b) $T_1(N) * T_2(N) = O(f(N) * g(N))$

**Rule2**

If $T(N)$ is a polynomial of degree $k$, then $T(N) = \Theta(N^k)$.

![2025 12 chapter2 algorithm analysis 107bd7c6](https://image.proleta.org/2025/12/2025-12-chapter2-algorithm-analysis-107bd7c6.webp)

**Rule 3**

$\log^k N = O(N)$ for any constant $k$. This tells us that logarithms grow very slowly. 

## Running-Time Calculations

**Rule 1—FOR loops**

The running time of a for loop is at most the running time of the statements inside the for loop (including tests) times the number of iterations. 

**Rule 2—Nested loops**

The total running time of a statement inside a group of nested loops is the running time of the statement multiplied by the product of the sizes of all the loops. 

**Rule 3—Consecutive Statements**

See Mathematics Basics - **Rule1**.

**Rule 4—If/Else** 

The running time of an if/else statement is never more than the running time of the test plus the larger of the running times of S1 and S2(S1, S2 are the options).

## Logarithms in the Running Time

**An algorithm is $O(\log N)$ if it takes constant $(O(1))$ time to cut the problem size by a fraction, which is usually $\frac{1}{2}$.**

On the other hand, if constant time is required to merely reduce the problem by a constant amount (such as to make the problem smaller by $1$), then the algorithm is $O(N)$.

### Binary Search 二分搜索

>**描述**
>
>Given an integer $X$ and integers $A_0, A_1, . . . , A_{N−1}$, which are **presorted** and **already in memory**, find i such that $A_i = X$, or return $i$ = −1 if $X$ is not in the input.

**代码（泛型编程）：**

```cpp
template <typename Comparable>
int binarySearch(const vector<Comparable> & a, const Comparable & x)
{
    int low = 0, high = a.size() - 1;
    while (low <= high)
    {
        int mid = (low + high) / 2;

        if (a[mid] < x)
            low = mid + 1;
        else if (a[mid] > x)
            high = mid - 1;
        else
            return mid;
    }
    return NOT_FOUND;
}
```

**分析：**

每次迭代，在 loop 内的所有操作（包括比较、加法语句、测试语句等等）记作 $O(1)$。

于是要（大致地）计算 loop 迭代了多少次，最坏情况下（目标不在所给序列中），loop 从 $\text{high - low} = N-1$ 减少到 $\text{high - low} = -1$。$\text{high - low}$ 每次至少减半，出于计算方便，我们视作它正好减半。从 $N-1$ 到 $1$ 将经历 $\lceil \log(N-1) \rceil$ 次迭代， 最后额外处理上界与下界相等，以及上界大于下界的特殊情况，所以合计 $\lceil \log(N-1) \rceil + 2$ 。

上面的分析是粗糙但正确的，实际分析的时候，你会发现会多1次或者少1次。这不会影响 Big-Oh 下的结果。

因此总的运行时间可记作 $O(\log N)$。

### Euclid’s Algorithm 欧几里得算法

**代码：**

对于学过高等代数/数论/离散数学的朋友，对这个算法应该是烂熟于心的。

```cpp
long long gcd(long long m, long long n)
{
    while(n != 0)
    {
        long long rem = m % n;
        m = n;
        n = rem;
    }
    return m;
}
```

**分析：**

> **Theorem 2.1**
>
> If $M > N$, then $M \, \text{mod} \, N < M/2$.

如果 $N \leq M/2$，由于 remainder 小于 $N$， 成立；如果 $N >M/2$，remainder 一定是 $M-N< M/2$，成立。

这说明经过两次运算之后，Euclid’s Algorithm 得出的 remainder 大致是一半一半地固定减小，最后能得出运算时间大概是（计算会非常复杂，书上亦没有给出详尽的推导）
$$
O(\log N)
$$

### Exponentiation 幂运算

**代码：**

```cpp
long long pow(long long x, int n)
{
    if (n == 0)
        return 1;
    if (n == 1)
        return x;
    if (isEven(n))
        return pow(x * x, n / 2);
    else
        return pow(x * x, n / 2) * x;
}
```

**分析：**

$n$ 仍然以固定的因子倍减，运行时间大概是 $O(\log N)$

书中给出了一个**例题**，我们将代码进行如下更改：

```cpp ins={9} del={8}
long long pow(long long x, int n)
{
    if (n == 0)
        return 1;
    if (n == 1)
        return x;
    if (isEven(n))
        return pow(x * x, n / 2);
        return pow( x, n / 2 ) * pow( x, n / 2 );
    else
        return pow(x * x, n / 2) * x;
}
```

考虑最坏情况，也就是说总要运行第9行，第1次运行到第8行**之前**是 $1=2^0$；接下来运行第8行调用2次函数，第2次运行到第8行**之前**是 $2^1$，以此类推，这样的递归调用将有 $\log N$ 次（忽略掉细枝末节的测试、运算、调用函数等），所以总的运行时间可以这样计算：
$$
2^0+2^1+ \cdots+2^{\log N} = N-1
$$
也即是
$$
O(N)
$$
