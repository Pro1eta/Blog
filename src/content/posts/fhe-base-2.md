---
title: 【同态加密的数学基础2】环论相关的基础知识
published: 2026-03-03
description: 理想的概念，环论的基本知识等
image: ""
tags:
  - Math
  - FHE
category: 笔记
draft: false
pinned: false
lang: zh_CN
series: 同态加密的数学基础
---
> **定义 16** （理想）
> 
> 设 $R$ 是一个环，非空子集 $I \subseteq R$ 称为 $R$ 的一个理想，如果：
> 1.  $(I, +) \le (R, +)$。
> 2. 强吸收律： $\forall a \in I, \forall r \in R$，有 $ra \in I$ 且 $ar \in I$。

> **定义 17** （商环）
> 
> 设 $I$ 是环 $R$ 的一个理想，商集 $R/I = \{a+I \mid a \in R\}$ 在下列运算下构成环：
> 1. 加法： $(a+I) + (b+I) = (a+b) + I$
> 2. 乘法： $(a+I)(b+I) = (ab) + I$
> 
> 称 $(R/I, +, \cdot)$ 为 $R$ 对 $I$ 的商环。

> **定理 10** （商环运算的良定义性）
> 
> 上面的定义中，加法和乘法是良定义的，即确实是二元运算。

**证明：**

取 $\forall a' \in a+I, b' \in b+I$，则有 $a' = a+i_1, b' = b+i_2$ ($i_1, i_2 \in I$)。

1. 加法良定义：$a'+b' = (a+i_1) + (b+i_2) = (a+b) + \underbrace{(i_1+i_2)}_{\in I}$
   
   $\implies (a'+b') + I = (a+b) + I$。
1. 乘法良定义：$a'b' = (a+i_1)(b+i_2) = ab + \underbrace{ai_2 + bi_1 + i_1i_2}_{\in I \text{ (由强吸收律)}}$
   
   由强吸收率 $ai_2 \in I, bi_1 \in I, i_1i_2 \in I$ （$I$ 是子环）$\implies a'b' \in ab+I \implies a'b' + I = ab + I$。

> **定义 18** （环同态）
> 
> 从环到环的映射 $\phi: R \to S$ 被称为环同态，如果：
> 1. $\phi(a+b) = \phi(a) + \phi(b)$
> 2. $\phi(ab) = \phi(a)\phi(b)$

:::important
不同环上的加法和乘法使用同样的符号不会造成歧义，因此这里不加以区分。
:::

> **定理 11** （环的第一同构定理）
> 
> 设 $\phi: R \to S$ 为环同态，设 $I = \ker\phi$，有：
> 1. $I$ 是 $R$ 的理想。
> 2. $R/I \cong \text{Im}\phi$。

**证明：**

1. $I$ 是理想：
   
   $\forall a, b \in I, \phi(a-b) = \phi(a)-\phi(b) = 0_S \implies a-b \in I \implies I \le (R, +)$。 对加法构成子群。
   
   $\forall a \in I, r \in R, \phi(ar) = \phi(a)\phi(r) = 0_S \cdot \phi(r) = 0_S \implies ar \in I$；同理 $ra \in I$。满足强吸收律。
1. $R/I \cong \text{Im}\phi$ 的证明：
   
   定义 $\psi: R/I \to \text{Im}\phi,\,\psi(r+I) = \phi(r)$。
   
   $r_1 + I = r_2 + I \iff r_1 - r_2 \in I \iff \phi(r_1 - r_2) = 0_S \iff \phi(r_1) = \phi(r_2) \iff \psi(r_1+I) = \psi(r_2+I)$。正向说明了良定义，反向说明了单射。满射性质显见。
   
   根据商环运算的良定义性，$\psi((a+I) + (b+I)) = \psi((a+b)+I) = \phi(a+b) = \phi(a) + \phi(b) = \psi(a+I) + \psi(b+I)$。
   
   同样 $\psi((a+I)(b+I)) = \psi(ab+I) = \phi(ab) = \phi(a)\phi(b) = \psi(a+I)\psi(b+I)$。因此该映射为商同态。
   
   综上，$\psi$ 是同构，所以 $R/\ker\phi \cong \text{Im}\phi$。

> **定义 19** （主理想）
> 
> 设 $R$ 是交换环。由 $R$ 中单个元素 $a$ 生成的理想称为主理想或者单理想 (Principal Ideal)。记作 $(a)$ 或 $Ra$，其定义为：$(a) = \{ra \mid r \in R\}$。

:::important
简单起见，我的笔记里的主理想指的是双侧主理想，用于定义的环是交换环。同时，如果 $R$ 不含有单位元，则定义需要修正成：

$\{ra + na \mid r \in R, n \in \mathbb{Z}\}$
:::

> **定义 20** （主理想整环）
> 
> 若一个整环 (Integral Domain) 的每一个理想都是主理想，则称该环为主理想整环 (Principal Ideal Domain, PID)。

:::tip
零因子 ： 若 $a, b \in R \setminus \{0\}$ 满足 $ab = 0$，则称 $a, b$ 为零因子。简单来说，就是乘积为零的非零元素。零指的是加法单位元。

整环：整环是无零因子的含元交换环，即：
1. $R$ 是交换的且存在单位元 $1 \neq 0$。
2. 无零因子 (即 $ab=0 \implies a=0$ 或 $b=0$)。
:::

> **定理 12**
> 
> 整数环 $\mathbb{Z}$ 是 PID。

**证明：**

设 $I$ 是 $\mathbb{Z}$ 的任一理想。

1. 若 $I = \{0\}$，则 $I = (0)$ 是主理想，结论显然。
2. 若 $I \neq \{0\}$，则 $I$ 中必存在非零整数 $n$。由理想对加法的逆元封闭性，$n \in I \implies -n \in I$，故 $I$ 中必含有正整数。
   
   设 $d$ 是 $I$ 中最小的正整数，$\forall k \in \mathbb{Z}$，由吸收律 $kd \in I$，故 $(d) \subseteq I$。
   
   $\forall n \in I$，由带余除法：$n = qd + r$，其中 $0 \le r < d$。由于 $n \in I$ 且 $qd \in (d) \subseteq I$，有 $\therefore r \in I$。若 $r > 0$，则 $r$ 是 $I$ 中的正整数且 $r < d$，与 $d$ 是最小正整数矛盾。故必有 $r = 0$，即 $n = qd \in (d)$。因此 $I \subseteq (d)$。
   
   综上，$I = (d)$。

$\mathbb{Z}$ 的任一理想均为主理想，故 $\mathbb{Z}$ 是 PID。 

> **定理 13**
> 
> 若 $F$ 是一个域，则多项式环 $F[x]$ 是 PID。

**证明：** 和上一条定理的思路相似，略。

> **定义 21** （素理想）
> 
> 交换环 $R$ 的理想 $P$ 称为素理想，如果 $P \neq R$，且满足：
对于任意的 $a, b \in R$，若 $ab \in P$，则必有 $a \in P$ 或 $b \in P$。

> **定理 14**
> 
> 设 $I$ 是含单位元交换环 $R$ 的理想。
> 
> 商环 $R/I$ 是整环 $\iff I$ 是素理想。

**证明：**

由定义，商环 $R/I$ 为整环等价于 $R/I$ 无零因子。

在 $R/I$ 中，考虑乘法运算 $(a+I)(b+I) = ab+I = 0_{R/I} = I$。

由于无零因子，上述等式说明 $a+I = I$ 或 $b+I = I$。

即 $ab \in I \implies a \in I$ 或 $b \in I$。这就是素理想的定义。

> **定义 22** （极大理想）
> 
> 设 $M$ 是含单位元交换环 $R$ 的一个理想。称 $M$ 为 $R$ 的极大理想，若其满足以下条件：
> 
> 1. $M \neq R$；
> 2. 不存在 $R$ 的理想 $J$，使得 $M \subsetneq J \subsetneq R$。

