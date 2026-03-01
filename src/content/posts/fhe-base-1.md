---
title: 【同态加密的数学基础1】近世代数基本概念
published: 2026-02-01
description: 理解全同态加密方案的基本知识，包含抽象代数的一些基本概念。
image: ""
tags:
  - Math
  - FHE
  - Crypto
category: 站务
draft: true
pinned: false
lang: zh_CN
series: 同态加密的数学基础
---
> **定理 1**
> 
> 所有的环 $R$ 至少有 $\{0\}$ 和 $R$ 两个理想。

这是显然的。

> **定理2**
> 
> 设 $R$ 是交换、含元环，有：
> 
> $R$ 是一个域 $\iff$  $R$ 只有 $\{0\}$ 和 $R$ 两个理想。

**证明**：

"$\implies$"：

设 $I$ 是 $R$ 的任一理想，证明它是平凡理想即可。

如果是零理想，OK。

如果 $I \neq \{0\}$，取非零元 $a \in I$。因 $R$ 是域，故存在逆元 $a^{-1} \in R$。

由理想的吸收律：$a \in I, a^{-1} \in R \implies a^{-1}a = 1 \in I$。

由于 $1 \in I$，则对 $\forall r \in R$，有 $r \cdot 1 = r \in I \implies R \subseteq I$。又 $I \subseteq R$，故 $I = R$。

"$\impliedby$"：

任取非零元 $a \in R$ ($a \neq 0$)，证明可逆即可。

构造由 $a$ 生成的主理想 $(a) = \{ra \mid r \in R\}$。显然 $a = 1 \cdot a \in (a)$，由 $a \neq 0$ 知 $(a) \neq \{0\}$。

根据已知条件，$R$ 只有平凡理想，故必有 $(a) = R$。因 $1 \in R$，故 $1 \in (a)$。由 $(a)$ 的定义，必存在 $b \in R$ 使得 $ba = 1$。

这说明 $a$ 乘法可逆，逆元为 $b$，故 $R$ 是域。

> **定理3（理想对应定理）**
> 
>设 $I$ 是环 $R$ 的理想，$\pi: R \to R/I$ 是自然同态（$a \mapsto a+I$）。定义两个集合：
>
>
>$S = \{J \mid J \text{ 是 } R \text{ 的理想且 } I \subseteq J \subseteq R\}$  
>$\bar{S} = \{\bar{J} \mid \bar{J} \text{ 是 } R/I \text{ 的理想}\}$。
>
>
>有如下的结论：
>
>$S$ 与 $\bar{S}$ 之间存在一一对应关系，且保持包含关系（若 $J_1 \subseteq J_2 \iff  \bar{J}_1 \subseteq \bar{J}_2$）。

**证明**：这个结论的证明稍微有点长。咱们慢慢看。对于集合来说：

$$
\pi(J) = \{ \pi(a) \mid a \in J \} = \{ a + I \mid a \in J \}
$$
$$
\pi^{-1}(\bar{J}) = \{ a \in R \mid \pi(a) \in \bar{J} \} = \{ a \in R \mid a + I \in \bar{J} \}
$$

1. 映射首先是 $S$ 和 $\bar{S}$ 上良定义的，这里的 $\pi$ 映射的结果首先必须是 $R/I$ 的理想，逆映射亦然。
   
   $\pi$ 方向： $\forall J \in S$，证明 $\bar{J} = \pi(J)$ 是 $R/I$ 的理想。
   
   $\forall \bar{x}, \bar{y} \in \bar{J}$，存在 $x, y \in J$。则 $\bar{x} - \bar{y} = (x-y)+I$。因 $x-y \in J$，故 $\bar{x} - \bar{y} \in \bar{J}$，对加法构成子群。
   
   对 $\forall \bar{r} \in R/I, \bar{x} \in \bar{J}$，有 $\bar{r}\bar{x} = (rx)+I$。因 $rx \in J$，故 $\bar{r}\bar{x} \in \bar{J}$。满足强吸收律。所以 $\bar{J}$ 是 $R/I$ 的理想。
   
   $\pi^{-1}$ 方向： 对 $\forall \bar{J} \in \bar{S}$，证明 $J = \pi^{-1}(\bar{J})$ 是 $R$ 的理想且包含 $I$。
   
   根据商环运算的良定义，若 $a, b \in J$，则 $a+I, b+I \in \bar{J} \implies (a+b)+I \in \bar{J} \implies a+b \in J$。
   
   根据 $\bar{J}$ 是 $R/I$ 的理想，同理可证吸收律。
   
   同时 $\bar{J}$ 继承 $R/I$ 的加法单位元，由于 $0_{R/I} = I \in \bar{J}$，故对 $\forall i \in I$，$\pi(i) = I \in \bar{J}$，这说明这样的 $i$ 肯定在映射的输出结果之中，换句话说， $I \subseteq J$。

2. 证明互为逆映射（双射性）
   
   $\pi(\pi^{-1}(\bar{J})) = \bar{J}$：$\bar{J}$ 中每一个元素是一个陪集，逆映射实际上选出了陪集包含的所有元素，根据这些元素再构造陪集，结果是一样的。
   
   $\pi^{-1}(\pi(J)) = J$：每一个陪集可以有多个代表元，所以用 $J$ 的元素作为代表元来构造陪集，实际构成的陪集数肯定不多于代表元的个数，从而容易得到 $J \subseteq \pi^{-1}(\pi(J))$。
   
   若 $a \in \pi^{-1}(\pi(J))$，则 $\pi(a) \in \pi(J)$。这意味着存在 $j \in J$ 使得 $a+I = j+I$，即 $a-j \in I$。由于 $I \subseteq J$，故 $a-j \in J$。
   
   由 $a = (a-j) + j$ 且 $a-j, j \in J$，得出 $a \in J$。故 $\pi^{-1}(\pi(J)) = J$。

2. 包含关系
   
   设 $J_1, J_2 \in S$，且 $J_1 \subseteq J_2$。任取 $\bar{x} \in \bar{J}_1$，由定义存在 $x \in J_1$，使得 $\bar{x} = \pi(x) = x + I$。因 $J_1 \subseteq J_2$，故 $x \in J_2$。于是 $\bar{x} = \pi(x) \in \pi(J_2) = \bar{J}_2$。故 $\bar{J}_1 \subseteq \bar{J}_2$。反过来是一样的。

> **定理 3**
> 
> 设 $R$ 是含单位元的交换环，$I$ 是 $R$ 的理想，有：
> 
> $R/I$ 是域 $\iff I$ 是 $R$ 的极大理想。

**证明：**

"$\implies$"：

已知 $R/I$ 是域，证明 $I$ 是极大理想（即若 $I \subseteq J \subseteq R$ 且 $J$ 是理想，则 $J=I$ 或 $J=R$）。

考虑自然同态 $\pi: R \to R/I$，则 $\pi(J)$ 是 $R/I$ 的理想。
因为 $R/I$ 是域，其理想只有平凡理想：$\pi(J) = \{0+I\}$ 或 $\pi(J) = R/I$。

1. 若 $\pi(J) = \{0+I\}$，对 $\forall a \in J$，有 $a+I = I \implies a \in I$。故 $J \subseteq I$，结合已知 $I \subseteq J$，得 $J = I$。

2. 若 $\pi(J) = R/I$，则 $1+I \in \pi(J)$，即存在 $j \in J$ 使得 $j+I = 1+I$。
   
   这意味着 $j-1 \in I$。因 $I \subseteq J$，故 $j-1 \in J$。由 $1 = j - (j-1)$ 且 $j, j-1 \in J$ 知 $1 \in J$。根据吸收律容易进一步证明 $J = R$。

"$\impliedby$"：

已知 $I$ 是极大理想，证明 $R/I$ 是域（即证明 $R/I$ 中任一非零元 $a+I$ 可逆）。

任取 $a+I \neq 0+I$，则 $a \notin I$。构造集合 $J = \{ra + i \mid r \in R, i \in I\}$。易证 $J$ 是 $R$ 的一个理想。

考察包含关系：

取 $r=0$，得 $I \subseteq J$。取 $r=1, i=0$，得 $a \in J$。因 $a \notin I$，故 $I \subsetneq J$。因 $I$ 是极大理想，必有 $J = R$。

由于 $1 \in R$，故 $1 \in J$。即存在 $r \in R, i \in I$ 使得 $ra + i = 1$。写成 $ra = 1-i$。

在商环 $R/I$ 中观察该等式：

$$
(r+I)(a+I) = ra + I = (1-i) + I
$$

因 $i \in I$，故 $(1-i) + I = 1 + I$。即 $(r+I)(a+I) = 1+I$。

这说明 $r+I$ 是 $a+I$ 的逆元。所以 $R/I$ 是域。

> **定理 4**
> 
> 设 $I$ 是含单位元交换环 $R$ 的理想，有：
> 
> $R/I$ 是整环（无零因子的交换含元环） $\iff I$ 是素理想。

**证明：**

根据商环运算的良定义性，$R/I$ 上：$(a+I)(b+I) = (ab)+I = 0+I = I$（$I$ 是 $R/I$ 的零元素）。

整环则无零因子$\iff a+I = I$ 或 $b+I = I$；

上面的说法等价于

$ab \in I \implies a \in I$ 或 $b \in I$，即素理想的定义。

> **定理 5**
> 
> 极大理想一定是素理想。

**证明：**

由上面两个定理：$M$ 是 $R$ 的极大理想 $\iff R/M$ 是一个域。

域必然是一个整环。

$P$ 是 $R$ 的素理想 $\iff R/P$ 是一个整环。

既然 $R/M$ 是整环，那么 $M$ 满足素理想的定义。

> **定理 6**
> 
> 在主理想整环「 PID 」中，对于 $p \neq 0$：
> 
> 1. $(p)$ 是素理想 $\iff p$ 是素元。
> 
> 2. $(p)$ 是极大理想 $\iff p$ 是不可约元。

**证明：**
1. $(p)$ 是素理想 $\iff$
   
   $\forall a, b \in R, ab \in (p) \implies a \in (p) \text{ 或 } b \in (p)$。
   
   上面的命题等价于
   
   $p \mid ab \implies p \mid a \text{ 或 } p \mid b$
   
   也即是 $p$ 是素元。
   
1. "$\implies$"： 设 $(p)$ 为极大理想。若 $p=ab$，容易得到 $(p) \subseteq (a) \subseteq R$。由极大性，$(a)=(p)$ 或 $(a)=R$。若 $(a)=(p) \implies a$ 与 $p$ 相伴（$b$ 为单位）；若 $(a)=R \implies a$ 必然为单位。故 $p$ 不可约。
   
   "$\impliedby$"： 设 $p$ 不可约。取理想 $J$ 满足 $(p) \subseteq J \subseteq R$。因 $R$ 是 PID，设 $J=(m)$。则 $(p) \subseteq (m) \implies m|p$。因 $p$ 不可约，故 $m$ 要么是单位，要么与 $p$ 相伴。若 $m$ 是单位，$J=R$；若 $m$ 与 $p$ 相伴，$J=(p)$。故 $(p)$ 是极大理想。 

> **定理 7**
> 
> PID 都是唯一分解整环 (UFD)。
> 
> 每个 $p \in PID, p \neq 0$ 且非单位，可唯一分解为不可约元的乘积。

**证明：**（可能不够严格）

设存在 $a_1$ 不能分解为不可约元乘积。

那么 $a_1$ 一定不是不可约元， $a_1 = a_2 b_2$，其中 $a_2, b_2$ 均非单位。

要么 $a_2$ 不能分解为不可约元乘积，要么 $b_2$ 不能分解为不可约元乘积。设 $a_2$ 不能分解为不可约元乘积。由此 $(a_1) \subsetneq (a_2)$，重复这一过程：

$$
(a_1) \subsetneq (a_2) \subsetneq (a_3) \subsetneq \dots
$$

令 $I = \bigcup_{n=1}^\infty (a_n)$。在 PID 中，$I$ 是主理想，设 $I = (a)$。则 $a \in \bigcup (a_n) \implies \exists k$ 使得 $a \in (a_k)$。

于是当 $n > k$ 时，$(a_n) \subseteq (a) = (a_k)$，和上面的过程矛盾。因此，每一个非零非单位的元素都可以分解为不可约元乘积。

设 $p_1 p_2 \dots p_n = q_1 q_2 \dots q_m$，其中 $p_i, q_j$ 都是不可约元。

PID 中不可约元 $p_1$ 是素元。

由 $p_1 | q_1 q_2 \dots q_m \implies \exists q_j$ 使得 $p_1 | q_j$。因 $q_j$ 不可约，故 $p_1$ 与 $q_j$ 相伴。

消去该项并对因子个数进行归纳，即证在相伴意义下分解唯一。