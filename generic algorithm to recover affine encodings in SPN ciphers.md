<!-- TOC -->

- [CEJO framework](#cejo-framework)
- [Conclusion](#conclusion)
- [Baek et al's Affine Equivalence Algorithm with Multiple S-boxes <sup>1</sup>](#baek-et-als-affine-equivalence-algorithm-with-multiple-s-boxes-sup1sup)
  - [Theorem 3](#theorem-3)
  - [Prove](#prove)
    - [Two Suppose](#two-suppose)
    - [Set](#set)
    - [Consider affine equivalence problem](#consider-affine-equivalence-problem)
  - [Without the oracle of the inverse of $F$](#without-the-oracle-of-the-inverse-of-f)
  - [When $A$ is split](#when-a-is-split)
- [Introduction of the Generic Algorithm <sup>2</sup>](#introduction-of-the-generic-algorithm-sup2sup)
  - [Problem 1](#problem-1)
  - [Remark 1](#remark-1)
    - [access to $F$, but not to $F^{-1}$](#access-to-f-but-not-to-f-1)
    - [access to both $F$ and $F^{-1}$](#access-to-both-f-and-f-1)
  - [Remark 2](#remark-2)
  - [Remark 3](#remark-3)
  - [Remark 4](#remark-4)
- [Overview of the Algorithm](#overview-of-the-algorithm)
  - [Computing the $V_i$'s](#computing-the-v_is)
  - [Computing the $I_i$'s](#computing-the-i_is)
  - [Recovering affine layers](#recovering-affine-layers)
    - [the case of different S-boxes](#the-case-of-different-s-boxes)
- [Complexity of the algorithm](#complexity-of-the-algorithm)
- [Reference](#reference)

<!-- /TOC -->

# CEJO framework

Basic strategy: the whole cipher is decomposed into round functions and the round functions are represented by summation of _lookup tables with small size_.

Chow et al.'s strategy (a white-box AES implementation and a white-box DES implementation) provided a framework, called "`CEJO framework`", for designing white-box implementation of using table lookups.

# Conclusion

1. As we can see from previous implementations, it is very difficult to design a white-box implementation with a security level similar to the black-box model.
Hence, the practical objective of white-box implementations is to **increase the complexity of cryprtanalysis**.

2. All of the implementations mentioned above suffered unpredicted attacks soon after their designs were announced.
This is  mainly because **there are no standard attack tools** such as differential cryptanalysis and linear cryptanalysis for block ciphers.

# Baek et al's Affine Equivalence Algorithm with Multiple S-boxes [<sup>1</sup>](#refer-anchor-1)

## Theorem 3

Let $F$ and $S$ be two permutations on $n$ bits where $S=(S_1, \cdots, S_k)$ with nonlinear permutations $S_i$ on $m$ bits for $i=1,\cdots,k$. 
<u>Assume that we can easily access the inversion of $F$.</u>
Then, we can find all affine mappings $A$ and $B$ such that $F=B\circ S\circ A $ in time $O(kn^32^{3m})$ if they exists.

## Prove

First, we assume that $F$ and $S$ are `linear equivalent`.

Suppose that $A$ and $B$ are `invertible linear mapping` over $\mathbb{Z}^n_2$ with $F=B\circ S\circ A$.
$$A=
\begin{aligned} 
\begin{bmatrix}
    A_1  \\
    \vdots\\
    A_{k}
     \end{bmatrix}
    \end{aligned}, B^{-1}=
\begin{aligned} 
\begin{bmatrix}
    B_1  \\
    \vdots\\
    B_{k}
     \end{bmatrix}
    \end{aligned}, S=
\begin{aligned} 
\begin{bmatrix}
    S_1  \\
    \vdots\\
    S_{k}
     \end{bmatrix}
    \end{aligned}
 \qquad (1)$$

fine two sets:

$$\left\{x_1,x_2,\cdots,x_n\right\}$$

$$\left\{B_i\circ F(x_1),B_i\circ F(x_2),\cdots,B_i\circ F(x_n)\right\}$$

such that 

$$\left\{F(x_1),F(x_2),\cdots, F(x_n)\right\}^{-1}$$

is `linearly independent` in order to <u>recocer $B_i$</u>

$$B_i=[B_i\circ F(x_1),B_i\circ F(x_2),\cdots,B_i\circ F(x_n)][F(x_1),F(x_2),\cdots, F(x_n)]^{-1} \qquad (2)$$

### Two Suppose

1. ***Suppose***: two sets $\left\{x_1,\cdots,x_{\ell}\right\}$ and $\left\{y_1=B_i\circ F(x_1), \cdots, y_{\ell}=B_i\circ F(x_{\ell})\right\}$ such that $\left\{x_1,\cdots,x_{\ell}\right\}$ is `linearly independent`. 
$$x=\sum^{\ell}_{j=1}b_j x_j (b_j\in\left\{0,1\right\})$$

$$\Downarrow$$

compute $y=B_i \circ F(x)$ from $y_1, \cdots ,y_{\ell}$

$$\Downarrow$$

$$y=S_i\circ A_i(x)=S_i(\sum^{\ell}_{j=1}b_j A_i(x_j))=S_i(\sum^{\ell}_{j=1}b_jS_i^{-1}(y_i)) \qquad (3)$$

$F$ is a `nonlinear bijection`

$$\Downarrow$$

$$F(x)\notin \mathbb{Z}_2 F(x_1)+\cdots+\mathbb{Z}_2 F(x_{\ell})$$

with high probability by assuming $F$ is random bijection.

2. ***Suppose***: two sets $\left\{F(x_1),\cdots,F(x_{\ell})\right\}$ and $\left\{y_1=B_i\circ F(x_1), \cdots, y_{\ell}=B_i\circ F(x_{\ell})\right\}$ such that $\left\{F(x_1),\cdots,F(x_{\ell})\right\}$ is `linearly independent`. 

$$x'=F^{-1}(\sum^{\ell}_{j=1}b'_j F(x_j)) (b'_j\in\left\{0,1\right\})$$

$$\Downarrow$$

compute $y'=B_i \circ F(x')$ from $y_1, \cdots ,y_{\ell}$

$$\Downarrow$$

$$y'=B_i \circ F(F^{-1}(\sum^{\ell}_{j=1}b'_j F(x_j)))=\sum^{\ell}_{j=1}b'_jB_i\circ F(x_j)=\sum^{\ell}_{j=1}b'_jy_j  \qquad (4)$$

$F^{-1}$ is a `nonlinear bijection`

$$\Downarrow$$

$$x'\notin \mathbb{Z}_2 x_1+\cdots+\mathbb{Z}_2 x_{\ell}$$

with high probability by assuming $F^{-1}$ is random bijection.

### Set

$$ x_0 =0, y_0=B_i\circ F(x_0), F(x_1)=0\Rightarrow  x_1=F^{-1}(0)$$

$$y_0=S_i \circ A(x_0)=S_i(0),y_1\coloneqq B_i\circ F(x_1) =0$$

$$x_2\in \left\{0,1\right\}^{n}\backslash  \left\{x_0,x_1\right\},y_2 \coloneqq B_i\circ F(x_2)$$

$x_1,x_2$ are `linearly independent`.

$$x_3=x_1+x_2, F \text{ is nonlinear}, x_3\notin \left\{x_0,x_1,x_2\right\}\Rightarrow F(x_3)\notin \mathbb{Z}_2F(0)+\mathbb{Z}_2F(2)$$

1. repeat above process in the equation $y=S_i\circ A_i(x)$ and $y'=B_i\circ F(x')$ several times

2. obtain $n$ vectors whose $F$ values are linearly independent

3. For each successful guessing, get an $m\times n$ linear mapping $B_i$

$$F=B\circ S\circ A$$

$$\Downarrow$$

$$B_i \circ F(x) = S_i \circ A_{i}(x)$$

$$\Downarrow$$

$$S_i^{-1} \circ B_i \circ F(x) =  A_{i}(x)$$

4. Check whether the mapping $S_i^{-1} \circ B_i \circ F$ is **linear** and reject the incorrect guesses

5. $n^3$ operations for each guessing $\Rightarrow$ the complexity becomes $kn^32^m$ to find full matrix $B$.
   
### Consider affine equivalence problem

$$B_i \circ F(x) +b_i = S_i ( A_{i}(x)+a_i)$$

for $m\times n $ linear mappings $A_i,B_i$ and the $m$-bit constant vectors $a_i,b_i,i=1,\cdots,k$

For each pair $(a_i,b_i)\in \mathbb{Z}_2^m \times \mathbb{Z}_2^m$, inputs $F(x)$ and $S_i(x+a_i)+b_i$, solve the affine equivalence problem

Total complexity: $O(kn^32^{3m})$ < $O(n^32^{2n})$
(by additionally choosing two $m$-bit constant vertors)

The dominant parts of the complexities depend on $m$.

More efficient whenever ***$S$ is a concatenation of several $S$-boxes*** as in the white-box implementaton.

## Without the oracle of the inverse of $F$

<center>

When the oracle of inversion of $F$ is not given, ***use only the property in the equation (3)***

guess about $\log m_A$ vectors, instead of one vector

obtain $m_A$ linearly independent vectors</center>

$$\Downarrow$$

$$O(kn^32^{m(\log m_A+2)})=O(kn^{m+3}2^{2m})$$

On the other hand, we can use the relation (4) if we evaluate the requied inverse value of $F$ in the equation (4)

## When $A$ is split

consider $A\in (\mathbb{Z_2})^{n\times n}$ as a $\widetilde{A} \in (\mathbb{Z}^{m\times m}_2)^{k\times k}$ with $n=km$

If $\widetilde{A}$ is of form $
\begin{aligned} 
\begin{bmatrix}
    * & 0 & *\\
   0 & A^* & 0\\
    * & 0 & *
\end{bmatrix}
\end{aligned}$ for some $A^*\in (\mathbb{Z}^{m\times m}_2)^{k_0\times k_0}$ and $k_0 \geq 1$, we say that $A$ is ***split***, and ***unsplit*** otherwise.
<center>

$A$ is split

</center>

$$\Downarrow$$

<center>

recover the encoding that corresponds to $A^*$ with complexity $k_0(k_0m)^32^{3m}$ 
  
</center>

# Introduction of the Generic Algorithm [<sup>2</sup>](#refer-anchor-2)

For solving the affine equivalence problem in the case where the inner **non-linear layer** is composed of **parallel S-boxes**.

Our algorithm solves the following problem:

## Problem 1

Let $F$ be an $n$-bit to $n$-bit permutation such that $F = B \circ S \circ A$, where:

1. $A$ and $B$ are $n$-bit affine layers;

2. $S=(S_{1}, \cdots, S_{k})$ consists of the parallel application of $k$ permutations $S_{i}$ on $m$ bits each (called S-boxes). Note that $n=km$.

Knowing $S$, and given oracle access to $F$ (but not $F^{-1}$), find affine $A'$, $B'$ such that $F = B'\circ S \circ A'$.

## Remark 1

Our statement of the problem allows the algorithm to query $F$, but not $F^{-1}$.

### access to $F$, but not to $F^{-1}$

CEJO framwork
The output of $F$ is computed as a sum of sum hard-coded table outputs, and inverting $F$ would require knowing how to split a given output of $F$ into the appropriate sum.

<u>Baek et al. also propose an algorithm when only $F$ is accessible, but it is much slower.</u>

### access to both $F$ and $F^{-1}$

Our own algorithm:

1. **isolate the input and output space of each S-box**

2. **exhaust that space in $2^m$ operations for each S-box**, which will allow us to access the inverse mapping of each S-box.

Essentially, our algorithm will allow us to revert back to the case where the direct and inverse mappings are both available.

In particular, it is not obvious how our algorithm could be improved even if $F^{-1}$ were accessible.

<u>Baek et al. explicitly provide an algorithm  to solve Problem 1 when $F$ and $F^{-1}$ are both available, in $O(n^42^{3m}/m)$ operations.</u>
However this is slower than our algorithm for all reasonable parameter ranges, even though our algorithm does not require access to $F^{-1}$.

## Remark 2

Problem 1 asks to recover **some** affine encodings $A'$, $B'$ such that $F=B'\circ S\circ A'$, but not necessarily $A$ and $B$.

Reason: $A$ and $B$ may not be uniquely defined.

$(A,B)\rightarrow (P \circ A, B\circ P^{-1})$, where $P$ is any permutation swapping S-box inputs.

Problem 1 merely asks to recover **a solution**.

Use the algorithm by Dinur to solve the Problem 1, that algorothm is able enumerate all solutions if desired, it is straightforward to adapt our algorithm so that it outputs **every solution**.

## Remark 3

The special case: <u>encodings are linear</u> instead of affine

Our algorithm eventually reduced Problem 1 to the `affine equivalence` problem **for each S-box separately**.

Using a `linear equivalence algorithm` on each S-box, instead of an affine one.

## Remark 4

The special case: $k=1$, _i.e._ <u>$S$ is composed of a single S-box</u>.

Practical complexity for $n$ upwards of 128 bits, by using the fact that $S$ is split into relatively small $m$-bit S-boxes.

# Overview of the Algorithm

1. isolate the input and output subspaces of each S-box
2. apply the generic affine equivalence algorithm by Dinur to each S-box separately

The first step: find the input subspace of each S-box
a subspace of dimension $m$ of the input space
this subspace span all $2^m$ possible values at the input of a single fixed S-box
yields a constant value at the input of all other S-boxes.

Symbol | Notation
:---:|:---
$\Delta$| an input difference that uniformly picked at random, yields a zero difference at the input of a particular S-box
$V_i$| input space which active at most $k-1$ S-boxes
$U_i$| output space
$I_i$| input space which active **only one** of the S-boxes
$O_i$| output space

## Computing the $V_i$'s

Computing all input spaces $V_i$ which active at most $k-1$ S-boxes.

$$\Delta  \in V_i$$

The input of the $i$-th S-box:
$$A(x)\oplus A(x\oplus \Delta)=0$$

and **non-zero differences** at the output of all other $k-1$ S-boxes.

$$\Downarrow$$

<center>one S-box will be inactive</center>

$$\Downarrow$$

$$ dim(U_i) = n-m$$

![avatar](https://github.com/jingyulee98/Affine-Equivalence-Algorithm/blob/main/Generic%20algorithm_finding%20input%20subspace%20of%20each%20S-box%20Vi.png?raw=true)

Testing if $\Delta \in V_1$:

$$ X=\left\{x_i \in  \mathbb{F}^2_n, x_i  \;random  \right\}$$

$$U=\left\{F(x_i)\oplus F(x_i\oplus \Delta),  x_i \in X\right\}$$

If $\lim(Span(U_i))=n-m$, then $\Delta \in V_i$.

$$\Downarrow$$

build all $V_1, \cdots ,V_k$

![avatar](https://github.com/jingyulee98/Affine-Equivalence-Algorithm/blob/main/Generic%20algorithm_building%20V1.png?raw=true)

## Computing the $I_i$'s

Find all input difference spaces $I_i$ which **active only one of the S-boxes**.

for $i$ from $1$ to $k$:

$$\Delta  \in V_i, x\in \mathbb{F}_2^n$$

Except on $m$ consecutive bits corresponding to the input of the $i$-th S-box:
$$A(x)\oplus A(x\oplus \Delta)=0$$

$$\Downarrow$$

computer the intersection of $k-1$ spaces $V_i$
$$\cap V_i = I_i$$

$$\Downarrow$$

recover all the input spaces $I_i$

$$ dim(I_i) = m$$

$$ dim(O_i) = m$$

![avatar](https://github.com/jingyulee98/Affine-Equivalence-Algorithm/blob/main/Generic%20algorithm_finding%20input%20subspace%20of%20each%20S-box%20Ii.png?raw=true)

## Recovering affine layers

$$F = B\circ P^{-1} \circ (S,\cdots, S)\circ P \circ A$$

$P$ is a **permutation** over the consecutive blocks of $m$ bits.

<u>**Build block diagonal affine mapping with block size $m$**</u>: $\mathcal{D}_{A}$, $\mathcal{D}_{B}$

$$\mathcal{D}_{A} = A \circ \mathcal{P} = diag(A_1,\cdots,A_k)$$

$$\mathcal{D}_{B} = \mathcal{Q} \circ B=diag(B_1,\cdots,B_k)$$

<u>**Build two affine mappings $\mathcal{P}$ and $\mathcal{Q}$** </u>

$$\mathcal{P}=
\begin{aligned} 
\begin{pmatrix}
    \mathcal{P}_1  |
    \cdots|
    \mathcal{P}_{k}
     \end{pmatrix}
    \end{aligned}
$$

$\mathcal{P}_i$: from $\mathbb{F}_2^m$ to $I_i$

$$\mathcal{Q}=
\begin{aligned} 
\begin{pmatrix}
    \mathcal{Q}_1  \\
    ——\\
    \vdots\\
    ——\\
    \mathcal{Q}_{k}
     \end{pmatrix}
    \end{aligned}
$$

$\mathcal{Q}_i$: from  $O_i$ to  $\mathbb{F}_2^m$

$$\Downarrow$$

$$A' = \mathcal{D}_{A} \circ \mathcal{P}^{-1}$$

$$B' = \mathcal{Q}^{-1} \circ \mathcal{D}_{B}$$

$$\Downarrow$$

$$F = B' \circ (S,\cdots, S) \circ A' $$

### the case of different S-boxes

$$\mathbb{F}_2^m \stackrel{\mathcal{Q}_i} \longleftarrow O_i \stackrel{B \circ 
\begin{aligned} 
\begin{bmatrix}
    S_1  \\
    \vdots\\
    S_{k}
     \end{bmatrix}
    \end{aligned}
     \circ A}\longleftarrow I_i  \stackrel{\mathcal{P}_i} \longleftarrow \mathbb{F}_2^m 
$$

$$F = B \circ (S_1,\cdots,S_k) \circ A $$

$$F_i = \mathcal{Q}_i \circ F \circ \mathcal{P}_i$$

$$F_i = B_i \circ S_i \circ A_i$$

$$\Downarrow$$

$$F = B' \circ (S_1,\cdots,S_k) \circ A' $$

![avatar](https://github.com/jingyulee98/Affine-Equivalence-Algorithm/blob/main/Generic%20algorithm_recovering%20affine%20layers.png?raw=true)

![avatar](https://github.com/jingyulee98/Affine-Equivalence-Algorithm/blob/main/Generic%20algorithm_Algorithm%201.png?raw=true)

# Complexity of the algorithm

1. compute all vector spaces $V_i$

(1) compute the output space $O_i$

$\bullet$ check whether $\Delta\in \cup^k_{j=1}V_j$

$\bullet$ $k2^{-m}$ ($2^m$ values for $\Delta$ on average to determine all the $k$ output spaces)

(2) compute the rank of $O_i$
take $n-m+l$ elements in $X$

$\Delta$ activates all S-boxes:

$$rank(O_i)=n-m$$

$$ \Downarrow$$

$$(n-m+l)^{2}n=O(n^3)$$

$$ \Downarrow$$

the computation of the output spaces $O_1,O_2,\cdots, O_k$ has complexity

$$O(2^mn^3)$$

2. compute a basis of the input space $V_i$ which is of dimension $n-m$

$\bullet$   $2^m$ tries for $\Delta$

$\bullet$   each value of $\Delta$ will be tested using $l$ values of $x$

$\bullet$ the parity-check matrix of $O_i$ can be computed at size $m\times n$

$$ \Downarrow$$

check if one output difference belongs to $O_i$ costs about $O(mn)$ operations

$$ \Downarrow$$

$$ n= km$$

$$ \Downarrow$$

$$O(k(n-m)2^{m}lmn)=O(2^{m}klmn^{2})=O(2^{m}ln^{3})$$

3. the total complexity of our algorithm

computer all intersection of $k-1$ vevtor spaces $V_i$ can be done in $O(kn^3)$

make $k$ calls to the affine equivalence allgotirhm, which leads to a complexity of $O(km^32^m)$

$$ \Downarrow$$

$$O(2^mn^3+2^{m}ln^{3}+\frac{n^{4}}{m}+2^mm^2n).$$

$$ \Downarrow$$

the algorithm from Dinur fails, use the algorithm from Biryukov _et al_.

$$ \Downarrow$$

$$O(2^mn^3+2^{m}ln^{3}+\frac{n^{4}}{m}+2^{2m}m^2n).$$

# Reference

<div id="refer-anchor-1"></div>

[1] Baek C H, Cheon J H, Hong H. White-box AES implementation revisited[J]. Journal of Communications and Networks, 2016, 18(3): 273-287.

<div id="refer-anchor-2"></div>

[2] Derbez P, Fouque P A, Lambin B, et al. On recovering affine encodings in white-box implementations[J]. IACR Transactions on Cryptographic Hardware and Embedded Systems, 2018: 121-149.