<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
<script type="text/x-mathjax-config">
    MathJax.Hub.Config({ tex2jax: {inlineMath: [['$', '$']]}, messageStyle: "none" });
</script>

<!-- TOC -->

- [Affine Transformation](#affine-transformation)
- [Affine Equivalence](#affine-equivalence)
- [Problem](#problem)
  - [Problem 1. Linear equivalence](#problem-1-linear-equivalence)
  - [Problem 2. Affine equivalence](#problem-2-affine-equivalence)
- [The Linear Equivalence Algorithm <sup>1</sup>](#the-linear-equivalence-algorithm-sup1sup)
  - [Naive approach](#naive-approach)
  - [Improved Naive approach](#improved-naive-approach)
  - [Linear equivalence algorithm](#linear-equivalence-algorithm)
    - [Notation](#notation)
    - [Exploit two ideas](#exploit-two-ideas)
    - [Complexity of LE](#complexity-of-le)
- [The Affine Equivalence Algorithm  <sup>1</sup>](#the-affine-equivalence-algorithm--sup1sup)
  - [A straightforward solution](#a-straightforward-solution)
  - [A second approach](#a-second-approach)
  - [Finding the linear representative](#finding-the-linear-representative)
    - [Defination of linear representative](#defination-of-linear-representative)
    - [Construst the representative $R_S$](#construst-the-representative-r_s)
    - [Explain the algorithm](#explain-the-algorithm)
    - [Update the sets](#update-the-sets)
    - [Complexity of AE](#complexity-of-ae)
- [Complexities of linear and affine algorithms](#complexities-of-linear-and-affine-algorithms)
- [Extensions of LE and AE algorithms](#extensions-of-le-and-ae-algorithms)
  - [Self-Equivalent S-Boxes](#self-equivalent-s-boxes)
  - [Equivalence of Non-invertible S-Boxes](#equivalence-of-non-invertible-s-boxes)
  - [Almost Affine Equivalent S-Boxes](#almost-affine-equivalent-s-boxes)
- [Apply AE and LE to AES](#apply-ae-and-le-to-aes)
- [An Improved Affine Equialence Algorithm (Dinur) <sup>2</sup>](#an-improved-affine-equialence-algorithm-dinur-sup2sup)
  - [Multivariate Polynomials](#multivariate-polynomials)
  - [Half-Space Masks and Coefficients](#half-space-masks-and-coefficients)
  - [Canonical Affine Transformations](#canonical-affine-transformations)
  - [Rand Tables and Histograms](#rand-tables-and-histograms)
  - [The New Affine Equivalence Algorithm](#the-new-affine-equivalence-algorithm)
- [Reference](#reference)

<!-- /TOC -->

# Affine Transformation

An `affine transformation` $A:\left\{0,1\right\}^{m}\rightarrow\left\{0,1\right\}^{n}$ over $GF\left(2\right)^{m}$ is defined using a Boolean matrix $L_{n\times m}$ and a word $a \in \left\{0,1\right\}^{n}$ as

$$A \left(x\right)= L\left(x\right)+ a$$

 $L\left(x\right)$ is simply matrix multiplication.

The transformation is **invertible** if $m=n$ and $L$ is an invertible matrix.

If $a=0$, then the $A$ is called a `linear transformation` (such functions are a subclass of affine functions),that is,

$$A \left(x\right)= L\left(x\right)$$

# Affine Equivalence

Two functions $\bm{F}:\left\{0,1\right\}^{n}\rightarrow \left\{0,1\right\}^{m}$, $\bm{G}:\left\{0,1\right\}^{n}\rightarrow \left\{0,1\right\}^{m}$ are `affine equivalent` is there exist two **invertible affine transformations** $A_{1}:\left\{0,1\right\}^{n}\rightarrow \left\{0,1\right\}^{n}$ and $A_{2}:\left\{0,1\right\}^{m}\rightarrow \left\{0,1\right\}^{m}$ such that

$$\bm{G} = A_{2}\circ \bm{F} \circ A_{1}$$

It is easy to show that the affine equivalence relation partitions the set of all functions into (affine) equivalence classes. We denote

$$\bm{F} \equiv \bm{G}$$

if $\bm{F}$ is affine equivalent to $\bm{G}$.

# Problem

## Problem 1. Linear equivalence

By linear mapping we mean a mapping $L(x)$ over $GF(2)^{n}$ that satisfies

$$L(x+y)=L(x)+L(y)$$

Let us consider the prblem of checking `linear equivalence` between two S-boxes $S_{1}$ and $S_{2}$. The problem is to find two **invertible linear mappings** $L_{1}$ and $L_{2}$, such that

$$L_{2} \circ S_{1} \circ L_{1} =S_{2}$$

## Problem 2. Affine equivalence

We want an algorithm that takes two $n \times n$-bit S-boxes $S_{1}$ and $S_{2}$ as input, and checks whether there exists a pair of **invertible affine mappings** $A_{1}$ and $A_{2}$ such that

$$A_{2}\circ S_{1}\circ A_{1}=S_{2}$$

# The Linear Equivalence Algorithm [<sup>1</sup>](#refer-anchor-1)

## Naive approach

Guess one of the mappings, for example $L_{1}$. Then one can extract $L_{2}$ from the equation:

$$L_{2} = S_{2} \circ L_{1}^{-1} \circ S_{1}^{-1}$$

and check if it is a linear, invertible mapping.

There are $O(2^{n^{2}})$ choices of invertible linear mappings over $n$-bit vectors.
For each guess one will need about $n^{3}$ steps to check for linearity and invertibility using `Gaussian elimination` [^1].

[^1]:高斯消元法算法的时间复杂度为 $O(n^{3})$

In total the naive algorithm would require $O(n^{3}2^{n^{2}})$ steps.

A similar naive affine equivalence algorrithm will use $O(n^{3}2^{n (n+1)})$.

## Improved Naive approach

We need only $n$ equations in order to check $L_{2}$ for invertibility and linearity.
If one guesses only $\log_{2}n$ vectors from $L_{1}$ one may span a space of $n$ points (by trying all linear combinations of the guessed vectors), evaluate the results through $L_{1}$, $S_{1}$ and $S_{2}$ and have $n$ constraints required to check for linearity of $L_{2}$.
If the $n$ new equations are not independent one will need to guess additional vectors of $L_{1}$.

Such an algorithm would require guessing of $n\log_2n$ bits of $L_{1}$ and the total complexity would be $O(n^{3}2^{n\log n})$

## Linear equivalence algorithm

### Notation

 Symbol | Notation  
:----:| :----:  
 $A,B^{-1}$| the linear mappings $L_{1}$ and $L_{2}$ respectively
 $C_{A},C_{B}$ | the sets of ***checked points*** for which the mapping ($A$ or $B$ respectively) is known
 $U_{A},U_{B}$  |  the sets of yet ***unknown points***
 $N_{A},N_{B}$  |  all the ***new points*** for which we know the mapping (either $A$ or $B$, respectively), but which are linearly independent from points of $C_{A}$ or $C_{B}$, respectively

$C,N,U$ are always disjoint.

### Exploit two ideas

1. `needlework effect`: in which guesses of portions from $L_{1}$ provide us with free knowledge of the values of $L_{2}$.
2. `exponential amplification of guesses`: due to the linear (affine) structure of the mappings, support that the new values from $L_{2}$ allow us to extract new free information about $L_{1}$.
3. knowing $k$ vectors from the mapping $L_{1}$, we know $2^k$ linear combinations of these vectors for free.

![avatar](https://github.com/jingyulee98/Affine-Equivalence-Algorithm/blob/main/linear%20equivalence.png?raw=true)

![avatar](https://github.com/jingyulee98/Affine-Equivalence-Algorithm/blob/main/The%20relations%20between%20the%20different%20sets%20for%20LE.png?raw=true)

### Complexity of LE

The complexity of this approach is about $n^3 \cdot 2^n$ steps (for S-boxes that do not map zero to zero, and $n^3 \cdot 2^{2n}$ otherwise).
The complexity of the linear equicalence algorithm (LE) is $O(n^3 2^{n})$.

# The Affine Equivalence Algorithm  [<sup>1</sup>](#refer-anchor-1)

$$A_{2} \circ S_{1} \circ A_{1} = S_{2}$$

$$\Downarrow$$

$$A_{2}(S_{1}(A_{1}(x)))=S_{2}(x)$$

$$A_{1}(x)=A \cdot x \oplus a$$

$$A_{2}(x)=B^{-1} \cdot x \oplus b$$

$$\Downarrow$$

$$B^{-1}S_{1}(A\cdot x \oplus a)\oplus b =S_{2}(x)$$

$$\Downarrow$$

$$B^{-1}\circ S_{1}(x\oplus a) \circ A = S_{2}(x) \oplus b$$

$$\Downarrow$$

$S_{1}(x\oplus a)$, $S_{2}(x) \oplus b$ are **linearly equivalent**

## A straightforward solution

![avatar](https://github.com/jingyulee98/Affine-Equivalence-Algorithm/blob/main/A%20straightforward%20solution%20of%20AE.png?raw=true)

This approach adds a factor $2^{2n}$ to the complexity of the linear algorithm, bringing the total to $O(n^{3}2^{3n})$.

## A second approach

Assign **a unique representative** to each linear equivalence class:

![avatar](https://github.com/jingyulee98/Affine-Equivalence-Algorithm/blob/main/A%20second%20solution%20of%20AE.png?raw=true)

The complexity of this second algorithm is about $2^n$ times the work needed for **finding the linear representative**.

## Finding the linear representative

The efficiency of an algorithm that finds the `linear representative` $R_S$ for an S-box $S$ depends on how this unique representative is chosen.

### Defination of linear representative

If all S-boxes in a linear equivalence class are ordered lexicographically according to their lookup tables, then the _smallest_ is called the representative of the class.

### Construst the representative $R_S$

1. Making an initial guess.

2. Incrementally build the linear mappings $A$ and $B$ such that $R'_{S}=B^{-1} \circ S \circ A$ is as small as possible.

3. The representative $R_S$ is obtained by taking the smallest $R'_{S}$ over possible guesses.

### Explain the algorithm

 Symbol | Notation  
:----:| :----:  
 $A,B^{-1}$| the linear mappings $L_{1}$ and $L_{2}$ respectively
 $D_{A},D_{B}$| values for which $A$ or $B$ are known respectively
 $C_{A},C_{B}$ | points of $D_{A}$ that have a corresonding point in $D_{B}$ and vice versa, i.e., $S \circ A(C_A)=B(C_B)$. For these values, $R'_S$ and $R'^{-1}_{S}$ are  known respectively
 $N_{A},N_{B}$  |  remaining points of $D_A$ and $D_B$. We have that $S \circ A(N_A) \cap B(N_B) = \varnothing$
 $U_{A},U_{B}$  |  values for which $A$ or $B$ can still be chosen

The main part of the algorithm that finds a candidate $R'_{S}$ consists in **repeatedly picking the smallest input** $x$ for which $R'_{S}$ is not known and trying to **assign it to the smallest available output** $y$.

![avatar](https://github.com/jingyulee98/Affine-Equivalence-Algorithm/blob/main/fine%20a%20candidate%20of%20linear%20representation_The%20affine%20equivalent%20algorithm.png?raw=true)

### Update the sets

1. Use the value of $B(y)$ to derive $B$ for all linear combinations of $y$ and $D_B$.
2. 
$$ D'_B \Leftarrow D_B \cup (D_B \oplus y)$$

$$ U'_B \Leftarrow U_B \setminus(D_B \oplus y)$$

2. Check whether any new point inserted in $D_B$ has a corresponding point in $D_A$ and update $C_B$, $D_B$, $C_A$ and $N_A$ accordingly:
3. 
$$ C'_B \Leftarrow C_B \cup B^{-1} [B(D_B \oplus y)\cap S \circ A(N_A)]$$

$$ N'_B \Leftarrow N_B \cup B^{-1} [B(D_B \oplus y)\setminus S \circ A(N_A)]$$

$$ C'_A \Leftarrow C_A \cup A^{-1}\circ S^{-1} [B(D_B \oplus y)\cap S \circ A(N_A)]$$

$$ N'_A \Leftarrow N_A \setminus A^{-1}\circ S^{-1} [B(D_B \oplus y)\cap S \circ A(N_A)]$$

![avatar](https://github.com/jingyulee98/Affine-Equivalence-Algorithm/blob/main/The%20relations%20between%20the%20different%20sets%20for%20AE.png?raw=true)
  
### Complexity of AE

Completely defining $R'_S$ for a particular guess takes about $2^n$ steps.
However, most guesses will already be rejected after having determined only slightly more than  $n$ values, because at that point $R'_S$ will usually turn out to be larger than the current smallest candidate.
The total complexity of finding the representative is expected to be $O(n^32^n)$.
The affine equivalence algorithm (AE) has complexity $O(n^32^{2n})$.

# Complexities of linear and affine algorithms

![avatar](https://github.com/jingyulee98/Affine-Equivalence-Algorithm/blob/main/Complexities%20of%20linear%20and%20affine%20algorithms.png?raw=true)
 
Note that we use $n^2$ for the complexity of the Gaussian elimination since $n \leq 32$ and we assume an efficient implementation using 32-bit operations.

# Extensions of LE and AE algorithms

## Self-Equivalent S-Boxes

$$A_2 \circ S \circ A_1 =S $$

## Equivalence of Non-invertible S-Boxes

a natura extension of our equivalence problem:
find an $n \times n$-bit affine mapping $A_1$ and an $m\times m$-bit affine mappin $A_2$ such that
$$A_2 \circ S_1 \circ A_1 = S_2$$
for two given $n \times m$-bit S-boxes $S_1$ and $S_2$.

## Almost Affine Equivalent S-Boxes

The S-boxes $S_1$ and $S_2$ are called `almost equivalent` if there exist two affine mappings $A_1$ and $A_2$ such that $ A_2 \circ S_1 \circ A_1 $ and $S_2$ **are equal, except in a few points** (e.g., two values in the lookup table are swapped, or some fixed fraction of the entrise are misplaced).

# Apply AE and LE to AES

When our AE tool is run for the 8-bit S-box $S$ used in AES, as many as **2040 different self-equivalence relations** are revealed.
Although this number might seem surprisingly high art first, we will show that it can easily be explained from **the special algebraic structure of the S-box of AES**.
Symbol | Notation
:----:|:----:
$[a]$| the $8\times 8$-bit matrix that corresponds to a multiplication by $a$ in $GF(2^8)$
$Q$ | the $8 \times 8$-bit matrix that performs the squaring operation in $GF(2^8)$

We can now derive a general expression for all pairs of affine mappings $A_1$ and $A_2$ such that $ A_2 \circ S \circ A_1 =S$:

$$A_1(x)=[a]\cdot Q^i \cdot x$$

$$A_2(x)=A(Q^{-i}\cdot [a] \cdot A^{-1}(x)), \text{with}\; 0 \leq i < 8 \; \text{and} \; a \in GF(2^8) \setminus \left\{0\right\}$$

Since $i$ takes on $8$ different values [^2] and there are $255$ different choices for $a$, we obtain exactly $2040 = 255 \times 8$ different solutions, which confirms the output of the AE algorithm.
[^2]: One can easily check that $Q^8=I$ and thus $Q^{-i}=Q^{8-i}$

# An Improved Affine Equialence Algorithm (Dinur) [<sup>2</sup>](#refer-anchor-2)

## Multivariate Polynomials

Any `Boolean function` $F:\left\{0,1\right\}^n \rightarrow \left\{0,1\right\}$ can be representend as a `multivariate polynomial` whose `algebraic mormal form (ANF)` is unique and given as

$$F(x[1],\cdots, x[n])=\sum_{u=(u[1],\cdots,u[n])\in\left\{0,1\right\}^n} \alpha_uM_u$$

where $\alpha_u \in \left\{0,1\right\}$ is the `coefficient`  of the `monomial` $M_u = \prod_{i=1}^n x[i]^{u[i]}$, and the sum is over $GF(2)$.

The `algebraic degree` of the function $F$ is defined as

$$ deg(F)=\max \left\{wt(u)| \alpha_u \neq 0\right\}$$

Given a function $F:\left\{0,1\right\}^n \rightarrow \left\{0,1\right\}$ represented by a polynomial 

$$P(x[1],\cdots,x[n])$$

define $F_{\geq d}:\left\{0,1\right\}^n\rightarrow \left\{0,1\right\}$ as the function represented by 

$$P_{(\geq d)}$$

## Half-Space Masks and Coefficients

Let $A:\left\{0,1\right\}^{n-1}\rightarrow \left\{0,1\right\}^{n}$  be an affine transformation such that $A(x)=L(x)+a$ for a matrix $L_{n\times(n-1)}$ with linearly independent columns. 

Then the (affine) range of $A$  is an $(n-1)$-dimensional affine subspace spanned by the columns of $L$ with the addition of $a$ .

The subspace orthogonal to the range of $A$ is of dimension $1$ and hence spanned by a single non-zero vector $h\in \left\{0,1\right\}^{n}$.

Namely, a vector $v\in \left\{0,1\right\}^{n}$ is in the range of $A$ if and only if $h(v+a)=0$ i.e., $v$ satisfies the linear equation $h(v)+h(a)=0$.

Sine $h$ partitions the space of $\left\{0,1\right\}^{n}$ into two halves, we call $h$ the `half-space mask (HSM)` of $A$ 

and call the bit $h(a)$ the `half-space free coefficient (HSC)` of $A$.

We call the linear subspace spanned by the columns of $L$ the `linear range` of $A$. A vector $v\in \left\{0,1\right\}^{n}$ is in the linear range of $A$ if and only if $h(v)=0$.

## Canonical Affine Transformations

$$ \text{non-zero } h\in \left\{0,1\right\}^n$$

$$c\in\left\{0,1\right\}$$

Define the `canonical affine transformation` $C_{|h,c}:\left\{0,1\right\}^{n-1}\rightarrow\left\{0,1\right\}^n$ with respect to $h,c$.
$\ell$: the index of the first non-zero bit of $h=(h[1],\cdots,h[n])$.

$$C_{|h,c}(x)=L(X)+a$$

$$a=c\cdot e_{\ell}$$

$e_{\ell}$: the $\ell$'th unit vector

$$L[i]=
\begin{cases}
e_i& \text{if } i < \ell\\
e_{i+1}+h[i+1]_{e_{\ell}}& \text{otherwise } (\ell \leq i \leq n-1)
\end{cases}$$

$$\Downarrow$$

the transformation $C_{|h,c}$ is defined by the symbolic form:

$$(x[1],x[2],\cdots,x[n])=(y[1],\cdots,y[\ell -1]), \sum^{n-1}_{i=\ell}h[i+1]y[i]+c,y[\ell],\cdots,y[n-1])$$

## Rand Tables and Histograms

prove $\bm{F}$ and $\bm{G}$ are affine equivalent $\Rightarrow$ the symbolic ranks of $\bm{P}$ and $\bm{Q}$ (as vectors) are equal
`rank table`

`rank group`: $(\max R, \min R)$ 

Although the _rank tables are different_, the size of each rank group $(\max R, \min R)$  of $\bm{F},\bm{G}$ is _identical_.

the `rank histogram` of $\bm{F}$ (with respect to $d$) as a mapping from each $(\max R, \min R)$ value to the corresponding **rank group size**.

## The New Affine Equivalence Algorithm

$$\bm{r}_1 =(n+1-\gamma_n, n-\gamma_n)$$

$$\bm{r}_2 =(n, n-\gamma_n)$$

$$\gamma_n = \left\lfloor(n/2)^{1/2}\right\rfloor$$

1. Given $\bm{F}:\left\{0,1\right\}^n\rightarrow \left\{0,1\right\}^{n}, \bm{G}:\left\{0,1\right\}^n\rightarrow \left\{0,1\right\}^{n}$
Compute their corresonding `ANF` representations $\bm{P}_{\geq (n-2)}$ and $\bm{Q}_{\geq (n-2)}$

2. Compute the `rank table` $\mathcal{T}_{\bm{F},n-2}$ and `rank histogram` $\mathcal{H}_{\bm{F},n-2}$ for $\bm{F}$ using $\bm{P}_{\geq (n-2)}$.
Compute $\mathcal{T}_{\bm{G},n-2}$ and $\mathcal{H}_{\bm{G},n-2}$ for $\bm{G}$ using $\bm{Q}_{\geq (n-2)}$.
If $\mathcal{H}_{\bm{F},n-2} \neq \mathcal{H}_{\bm{G},n-2}$, return "Not Equivalent".
***Algorithm to compute  the rank table and rank histogram***
For each non-zero HSM $h\in \left\{0,1\right\}^n$：
**(1)** Compute $R_{\bm{F},n-2,h}=(\max R,\min R)$ as follows. 
Compute $(\bm{P}_{\geq n-2}\circ C_{|h,0})_{\geq n-2}=(\bm{P}\circ C_{|h,0})_{\geq n-2}$ and calculate its symbolic rank $r_0$ using `Guassian elimination`.
Compute  $(\bm{P}_{\geq n-2}\circ C_{|h,1})_{\geq n-2}=(\bm{P}\circ C_{|h,1})_{\geq n-2}$ and calculate its symbolic rank $r_1$ using `Guassian elimination`.
Let $\max R=\max \left\{r_0,r_1\right\}$ and $\min R=\min \left\{r_0,r_1\right\}$.
**(2)** Insert $h$ into $\mathcal{T}_{\bm{F},n-2}(\max R,\min R)$, along with the value of the attached constant $c\in \left\{0,1\right\}$ such that $\max R = SR((\bm{F}\circ C_{|h,c})_{(\geq n-2)})$ (if $\max R >\min R$)  In addition, increment entry $\mathcal{H}_{\bm{F},n-2}(\max R, \min R)$.
**Note:** <u>The time complexity of the algorithm depends on how a polynomial is represented.</u>

3. Obtain the set $U_{\bm{F}}$ running the algorithm on inputs  $\mathcal{T}_{\bm{F},n-2}$ and $\bm{r}_1, \bm{r}_1$
Obtain the set $U_{\bm{G}}$ running the algorithm on inputs  $\mathcal{T}_{\bm{G},n-2}$ and $\bm{r}_1, \bm{r}_1$
***The Unique HSM Algorithm***
**(1)** For each $h \in \mathcal{T}_{\bm{F},n-2}(\bm{r})$, compute $\mathcal{HG}_{\bm{F},n-2,h,\bm{r'}}$ as follows:
**a.** for each $h'\in \mathcal{T}_{\bm{F},n-2}(\bm{r'})$:
    Compute $h+h'$, find its rank $\bm{r''}=R_{\bm{F},n-2,h+h'}$ in $\mathcal{T}_{\bm{F},n-2}$.
**b.** Insert $\mathcal{HG}_{\bm{F},n-2,h,\bm{r'}}$ along with $h$ and its attached constant $c$ into the multi-set $\mathcal{HM}_{\bm{F},n-2,\bm{r},\bm{r'}}$.
**(2)** For each unique HSM $h$ in $\mathcal{HM}_{\bm{F},n-2,\bm{r},\bm{r'}}$, add the triplet $(h,c ,\mathcal{HG}_{\bm{F},n-2,h,\bm{r'}})$ to $U_{\bm{F}}$.
**Note:** <u>The time complexity of the algorithm is product of sizes of the rank groups.</u>

4. On inputs $U_{\bm{F}}$ and $U_{\bm{G}}$ to recover affine transformation
If it returns "Not Equivalent", return the same output.
Otherwise, it returns a candidate for $A_1$.
***The Affine Transformation $A_1$ Recovery Algorithm***
**(1)** Allocate $n+1$ linear equation systems $\left\{E_i\right\}^{n+1}_{i=1}$, each of dimension $n\times n$: the first $n$ equation systems are on the columns $L[i]$ of $L$ and the final equation system $E_{n+1}$ is on $a$.
**(2)** Locate $n$ linearly independent HSMs in $U_{\bm{G}}$.For each such HSM $h$:
***Lemma 5*** 
**a.** Recover the  triplet $(h,c ,\mathcal{HG}_{\bm{G},n-2,h,\bm{r'}})$ from $U_{\bm{G}}$.
**b.** Search $U_{\bm{F}}$ for a triplet $(h',c' ,\mathcal{HG}_{\bm{F},n-2,h',\bm{r'}})$ such that $\mathcal{HG}_{\bm{F},n-2,h',\bm{r'}}=\mathcal{HG}_{\bm{G},n-2,h,\bm{r'}}$.
If no mathch exists, return "Not Equivalent".
**c.** Based on Lemma 5, for $i=1,2,\cdots, n$ add equation $h'(L[i])=h[i]$ to $E_i$.
**d.** Based on Lemma 5, add equation $h'(a)=c+c' $ to $E_{n+1}$.
**(3)** Solve each one of $\left\{E_i\right\}^{n+1}_{i=1}$, recover $A_1$ and return its matrix $L$ and vector $a$.

5. Recover a candidate for $A_2=L_2(x)+a_2$ by evaluating inputs $v\in  \left\{0,1\right\}^n$ to $\bm{F} \circ  A_1$ and $\bm{G}$:
each input $v$ gives $n$ linear equations on $L_2$ and $a_2$.
Hence, after a bit more than $n$ evaluations, we expect the linear equation system to have a single solution which gives a candidate for $A_2$.

6. Test the candidates $A_1,A_2$ by equating the evaluations of $\bm{G}$ and $A_2 \circ \bm{F} \circ A_1$ on all $2^n$ possible inputs.
If $\bm{G}(v)\neq A_2 \circ \bm{F} \circ A_1(v)$ for some $v\in \left\{0,1\right\}^n$, return "Not Equivalent".
Otherwise, return $A_1,A_2$.

# Reference

<div id="refer-anchor-1"></div>

[1] Biryukov A, De Canniere C, Braeken A, et al. A toolbox for cryptanalysis: Linear and affine equivalence algorithms[C]. International Conference on the Theory and Applications of Cryptographic Techniques. Springer, Berlin, Heidelberg, 2003: 33-50.

<div id="refer-anchor-2"></div>

[2] Dinur I. An improved affine equivalence algorithm for random permutations[C]. Annual International Conference on the Theory and Applications of Cryptographic Techniques. Springer, Cham, 2018: 413-442.
