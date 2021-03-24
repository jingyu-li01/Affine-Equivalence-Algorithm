<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
<script type="text/x-mathjax-config">
    MathJax.Hub.Config({ tex2jax: {inlineMath: [['$', '$']]}, messageStyle: "none" });
</script>

<!-- TOC -->

- [Introduction](#introduction)
  - [Contributions](#contributions)
  - [Results](#results)
  - [Analysis](#analysis)
    - [grey-box techniques](#grey-box-techniques)
    - [CEJO framework](#cejo-framework)
- [Self-Equivalences](#self-equivalences)
- [Affine Equivalence Problems](#affine-equivalence-problems)
- [CEJO Implementations](#cejo-implementations)
  - [Security of CEJO Implementations——only three generic reduction attacks to CEJO implementations](#security-of-cejo-implementationsonly-three-generic-reduction-attacks-to-cejo-implementations)
- [Self-Equivalence Implementations](#self-equivalence-implementations)
- [Diagonal self-equivalences](#diagonal-self-equivalences)
- [The generic attack on self-equivalence encodings](#the-generic-attack-on-self-equivalence-encodings)
  - [The Centralizer Problems](#the-centralizer-problems)
  - [The Asymmetric Problems](#the-asymmetric-problems)
- [References](#references)

<!-- /TOC -->

# Introduction

Most of the white-box designs follow `the CEJO framework`, where each round is encoded by composing it with small random permutations.

While several generic attacks have been proposed on the CEJO framework, no generic analysis has been performed on `self-equivalence encodings`

a different design where only `the affine layer` of each round is encoded with random self-equivalences of the S-box layer, that is, affine permutations commuting with the non-linear layer

## Contributions

analyse the security of white-box implementations based on self-equivalence encodings for a broad class of `SPN ciphers`

1. characterize the self-equivalence groups of `S-box layers`
   illustrate how CEJO implementations can be efficiently transformed into self-equivalence implementations
2. prove that all the self-equivalence groups of S-box layers, and prove that all the self-equivalences of a cryptographically strong S-box layer have a `diagonal shape`
   
3. propose the first generic attack on self-equivalence encodings

   $\bullet$ ***partically recovers the self-equivalence encodings*** up to some unknown affine perrmutations belonging to a small subgroup of the self-equivalence group

   $\bullet$ ***the key is then recovered*** by `a brute force search` over the small subgroup

   $\bullet$ attack based on `affine equivalence problems`, identifies the connnection between ***the security of self-equivalence encodings*** and ***the self-equivalence structure of the cipher components***
   
   our analysis provides the foundations to secure a different class of ciphers with self-equivalence encodings.

## Results

1. the traditional SPN cipher with `cryptographically strong` S-box layers cannot be secured with self-equivalence encodings
2. self-equivalence encodings resist the generic attack if ***the cipher components satisfy some properties***, revealing the potential of self-equivalence encodings to secure other types of ciphers

## Analysis 

Some `generic attacks` have been proposed on the `CEJO framework`, showing CEJO implementations are not suitable for a broad class of `SPN ciphers`

self-equivalence encodings have ***only*** been considered for AES, and the security of this type of encodings has not been analysed for other ciphers

### grey-box techniques

1. DFA: as long as the external encodings are chosen carefully

### CEJO framework

`Key-extraction attacks` on CEJO implementations typically consists of two main steps:
1. ***reducing the round encoding space***
   the adversary obtains new encoded round functions with the same underlying round keys but with round encodings restricted to a smaller encoding space
2. ***guessing the round keys***
   exhaustive search over the reduced-round encoding space to recover the round keys

For most SPN ciphers, the ***key schedule can be inverted*** and few round keys can uniquely determine the master key

# Self-Equivalences

----
*Definition 1.* Let $F$ be an $n$-bit function. A pair of $n$-bit affine permutations $(A,B)$ such that 

$$F= B\circ F\circ A$$

is called an `(affine) self-equivalence` of $F$.

If $A$ and $B$ are `linear`, $(A,B)$ is called a `linear self-equivalence`.

If $(A,B)=(\oplus_a,\oplus_b)$ are `constants`, $(A,B)$ is called an `additive self-equivalence`.


*Definition 2.* Two $n$-bit functions $F$ and $G$ are said to be `affine (resp. linear) equivalent` if there exists a pair of $n$-bit affine (resp. linear) permutations $(A,B)$ such that 

$$ G=B\circ F\circ A$$

----

$GL_n$: the general linear group consisting of all $n$-bit linear permutations 
$AGL_n$: the affine general linear group of $\mathbb{F}_2^n$

$SE(F)$: the set of self-equivalences of $F$
$RSE(F)$: the set of right self-equivalences
$LSE(F)$: the set of left self-equivalences

$$ SE(F)=\left\{(A,B)\in AGL_n\times AGL_n:F=B\circ F\circ A \right\}$$

$$ RSE(F)=\left\{A:\exists B \text{ s.t. } (A,B)\in SE(F) \right\}$$

$$ LSE(F)=\left\{B:\exists A \text{ s.t. } (A,B)\in SE(F) \right\}$$

----

***Lemma 1.*** $SE(F)$ is a subgroup of $AGL_n\times AGL_n$, and $LSE(F)$ and $RSE(F)$ are subgroups of $AGL_n$. Moreover, if $G$ is a function affine equivalent to $F$, i.e.,

$$ G=B\circ F\circ A$$

then the self-equivalence groups of $F$ and $G$ are conjugates, that is,
 
$$ RSE(G)=A^{-1}\circ RSE(F)\circ A$$

$$LSE(G)=B \circ LSE(F) \circ B^{-1}$$

----

# Affine Equivalence Problems

----

*Definition 3.*  An `affine (resp. linear) equivalence problem` is a functional equation of the form 

$$G=Y\circ F\circ X$$

where $F$ and $G$ are known $n$-bit functions and $(X,Y)$ are unknown affine (resp. linear) permutations.


the unknowns $(X,Y)$ are restricted to given subgrounds $(\mathcal{A},\mathcal{B})$, the soulution set:

$$\mathcal{S}=\left\{(A\circ X_0, Y_0\circ B):(A,B)\in SE(F)\cap(\mathcal{A}\times \mathcal{B}) \right\}$$

----

***Solutions of the affine case***

1. Biryukov et al. proposed an algorithm: $\mathcal{O}(n^32^{2n})$
   can be applied to find all the solutions by repeating the algorithm for each possible initial guess
   $\bullet$ for the linear case: $2^{2n}$
   $\bullet$ for the affine case: $2^{3n}$
   $\bullet$ also lower bounded by the number of solutions

2. Dinur proposed an algorithm for random permutations: $\mathcal{O}(n^32^n)$

$$\downarrow$$

assume the complexity of solving an affine equivalence problem with $n$-bit permutations: $\mathcal{O}(n^32^{2n})$

3. Derbez et al. obtained an algorithm to solve the affine equivalence problem: $\mathcal{O}(2^mn^3+n^4/ m + 2^{2m}mn^2)$
   For $n$-bit functions $F=F_1||\cdots||F_{\rho}$ composed of $m$-bit cryptographic S-box $F_i$
   (assume that the S-boxes do not have non-trivial linear components, i.e., there does not exist a non-zero $(m,1)$-bit linear function $B$ such that $(B\circ S)(x)=\sum b_iS(x)_i$ is a linear function 
   Most SPN ciphers employ cryptographically strong S-boxes satisfying this requirement.)
   For $n$-bit affine functions $F$, the affine equivalence problem can be represented as a linear system of $n+1$ equations and solved with `Gaussian elimination` in time $\mathcal{O}((n+1)^{\omega})$, where $2<\omega<3$ is the matrix multiplication constant

4. $C(F)=\left\{A\in GL_n:A\circ F=F\circ A\right\}$
   $G=X^{-1} \circ M_{\alpha}\circ X$ 
   $\mathcal{S}=C(M_{\alpha})\circ X_0$  for any particular solution $X_0$

# CEJO Implementations

## Security of CEJO Implementations——only three generic reduction attacks to CEJO implementations

<font color=red>

the reduction of the round encoding space
</font>

1. Michiels et al. proposed the first reduction attack
   composed of one algorithm to remove the non-linear part of the encodings and another algotithm to partically recover the remianing affine encodings
2. Baek et al. generalized the algorithm to remove affine encodings 
3. Derbez et al. improved the recovering of the affine encodings by an efficient algorithm to solve affine equivalence problem of S-box layers

$$\Downarrow$$

----
*Proposition 1.* Let $E_k$ be the encryption function of an $n$-bit `SPN cipher` with `linear layer` $LL$ and `S-box layer` $SL$ consisting of $m$-bit cryptographic S-boxes and let $\overline{E_k}$ be a CEJO implementation of $E_k$ with $m_n$-bit non-linear encodings and $m_l$-bit linear encodings. Given black-box access to three consecutive encoded round functions $\overline{E^{(r-2)}}$, $\overline{E^{(r-1)}}$ and $\overline{E^{(r)}}$, one can find another encoded $E^{(r)}$ with round encoding space $LL\circ LSE(SL)$, i.e.,

$$\widehat{E^{(r)}}=\widehat{O}\circ E^{(r)}\circ \widehat{I}, \quad \widehat{I},\widehat{O^{-1}} \in LL\circ LSE(SL)$$

in time $\mathcal{O}((n/ m_n)2^{3m_n}+2^mn^3+n^4/ m +2^{2m}mn^2)$.

$$\Uparrow$$

An encoded implementation of $E_k$ is an encoded $E_k$ composed of encoded round functions, that is,

$$\overline{E_k}=\overline{E^{(n_r)}}\circ \cdots \circ \overline{E^{(1)}}=(O^{(n_r)}\circ E^{(r)}\circ I^{(n_r)})\circ \cdots \circ (O^{(1)}\circ E^{(1)}\circ I^{(1)})$$

 for some $n$-bit permutations $(I^{(r)},O^{(r)})$ satisfying $I^{(r+1)}=(O^{(r)})^{-1}$


$$\Downarrow$$

$$\overline{E_k}=O^{(n_r)}\circ E_k\circ I^{(1)}$$

the encodings $(I^{(1)},O^{(n_r)})$ are called the `external encodings`

<font color=blue>

***Proof***

1. recover the non-linear part of the output encodings of $\overline{E^{(i)}}$, for $i\in\left\{r-2,r-1,r\right\}$
   each $m_n$-bit non-linear function is recovered up to an affine transformation in time $\mathcal{O}((n/m_n)2^{3m_n})$
   
   new encoded round functions are obtained,

   $$\widetilde{E^{(i)}}=\widetilde{O^{(i)}}\circ E^{(i)}\circ \widetilde{I^{(i)}}, \quad i \in \left\{r-1,r\right\}$$

   the input and output round encodings are ***unknown affine permutations***

2. $\widetilde{E^{(r-1)}}$ and $\widetilde{E^{(r)}}$ are affine equivalent to the S-box layer $SL$
   the corresponding affine equivalence problems can be solved with the algotithm by Derbez et al. in time $\mathcal{O}(2^mn^3+n^4/m+2^{2m}mn^2)$
   two pairs of affine permutations, $(A^{(r-1)},B^{(r-1)})$ and $(A^{(r)},B^{(r)})$, are obtained such that
   $$\widetilde{E^{(i)}}=B^{(i)}\circ SL\circ A^{(i)}, \quad i \in \left\{r-1,r\right\}$$

3.
$$\widetilde{E^{(i)}}=\widetilde{O^{(i)}}\circ E^{(i)}\circ \widetilde{I^{(i)}}, \quad i \in \left\{r-1,r\right\}$$ 

$$\widetilde{E^{(i)}}=B^{(i)}\circ SL\circ A^{(i)}, \quad i \in \left\{r-1,r\right\}$$

$$\Downarrow$$

$$\begin{aligned}
B^{(i)}\circ SL\circ A^{(i)}
&=\widetilde{O^{(i)}}\circ E^{(i)}\circ \widetilde{I^{(i)}}\\
&\xlongequal{E^{(i)}=LL\circ SL\circ \oplus_{k^{(i)}}}\widetilde{O^{(i)}}\circ LL \circ SL \circ \oplus_{k^{(i)}} \circ \widetilde{I^{(i)}}\\
&=(\widetilde{O^{(i)}}\circ LL) \circ SL \circ (\oplus_{k^{(i)}} \circ \widetilde{I^{(i)}})\\
\end{aligned}$$

Note that $(A^{(i)},B^{(i)})$ is not necessarily equal to $(\oplus_{k^{(i)}}\circ \widetilde{I^{(i)}},\widetilde{O^{(i)}}\circ LL)$

$$\downarrow$$

there exists a self-euqivalence $(C^{(i)},D^{(i)})\in SE(SL)$ such that 

$$A^{(i)}=C^{(i)}\circ \oplus_{k^{(i)}} \circ \widetilde{I^{(i)}}$$

$$B^{(i)}=\widetilde{O^{(i)}}\circ LL\circ D^{(i)}$$

$$\downarrow$$

compose $\widetilde{E^{(r)}}$ with the `key-independent permutations` $B^{(r-1)}$ and $B^{(r)}$ $\rightarrow$ obtain an encoded $E^{(r)}$ with round encoding space $LL\circ LSE(SL)$

$$\begin{aligned}
\widehat{E^{(r)}}
&=(B^{(r)})^{-1}\circ \widetilde{E^{(r)}}\circ B^{(r-1)}\\
&\xlongequal{\widetilde{E^{(r)}}=\widetilde{O^{(r)}}\circ E^{(r)}\circ \widetilde{I^{(r)}}}(B^{(r)})^{-1}\circ \widetilde{O^{(r)}}\circ E^{(r)}\circ \widetilde{I^{(r)}}\circ B^{(r-1)}\\
&\xlongequal{B^{(r)}=\widetilde{O^{(r)}}\circ LL\circ D^{(r)}}(\widetilde{O^{(r)}}\circ LL\circ D^{(r)})^{-1}\circ \widetilde{O^{(r)}}\circ E^{(r)}\circ \widetilde{I^{(r)}}\circ (\widetilde{O^{(r-1)}}\circ LL\circ D^{(r-1)})\\
&\xlongequal{\widetilde{I^{(r)}}=(\widetilde{O^{(r-1)}})^{-1}}( LL\circ D^{(r)})^{-1}\circ E^{(r)}\circ (LL\circ D^{(r-1)})\\
\end{aligned}$$

$$\Downarrow$$


$$\widehat{E^{(r)}}=\widehat{O}\circ E^{(r)}\circ \widehat{I}, \quad \widehat{I},\widehat{O^{-1}} \in LL\circ LSE(SL)$$

</font>




----

$$\Downarrow$$

<font color=red>

given a CEJO implementation of an SPN cipher $\rightarrow$ find another encoded implementation where the round encoding space has been reduced to $LL\circ LSE(SL)$

</font>

the complexity of *Proposition 1* is similar to the memory complexity of a CEJO implementation

`BGE attack`, `Collision attack` and `On recovering affine encodings in white-box implementations`: propose that reduced the round encoding space to a single element

no generic attack has been proposed reducing the round encoding space further than `Proposition 1`

round encoding space is strongly related to the self-equivalence set of the S-box layer

# Self-Equivalence Implementations

McMillion et al. considered a white-box implementation of AES where the round encodings were self-equivalences of the S-box layer and showed it was insecure.

1. CEJO implementation (encoded implementations)
   the round encodings
   the round encodings space

$$\Downarrow \text{Proposition 1}$$

<font color=red>

2. self-equivalence implementations
   only encode the affine part of the round function
   the intermediate $\rightarrow$ the round encodings
   the round encodings space
</font>

an encryption function of an SPN cipher: $E_k=E^{(n_r)}\circ \cdots \circ E^{(1)}$

round function: $E^{(r)}=LL\circ SL\circ \oplus_{k^{(r)}}$

intermediate affine layers: $AL^{(r)}=\oplus_{k^{(r)}}\circ LL, \quad r=2,\dots, n_r$

the first affine layer: $AL^{(1)}=\oplus_{k^{(1)}}$

the last affine layer: $AL^{(n_r+1)}=LL$

the $r$th round includes the round key of the $r$th round but the linear layer of the previous round

----

***Definition 7.*** Let $E_k$ be the encryption function of `an SPN cipher`. `A self-equivalence implementation` is an encoded $E_k$ given by

$$\overline{E_k}=AL^{(n_r+1)}\circ SL\circ \overline{AL^{(n_r)}} \circ SL\circ  \overline{AL^{(n_r-1)}}\circ \cdots \circ  \overline{AL^{(2)}}\circ SL\circ  \overline{AL^{(1)}}$$

the intermediate encodings of the affine layers are self-equivalence of the S-box layer, i.e.,

$$\overline{AL^{(r)}}=O^{(r)}\circ AL^{(r)} \circ I^{(r)}, \quad (O^{(r)},I^{(r+1)})\in SE(SL)$$

and the `external encodings` $(I^{(1)},O^{(n_r)})$ are `affine permutations`

----

the last affine layer is not encoded since it is `not key-dependent`

$$\Downarrow$$

self-equivalence property

$$\Downarrow$$

$$\begin{aligned}
\overline{AL^{(r+1)}}\circ SL\circ \overline{AL^{(r)}}
&\xlongequal{\overline{AL^{(r)}}=O^{(r)}\circ AL^{(r)} \circ I^{(r)}}(O^{(r+1)}\circ AL^{(r+1)} \circ I^{(r+1)})\circ SL\circ (O^{(r)}\circ AL^{(r)} \circ I^{(r)})\\
&=O^{(r+1)}\circ AL^{(r+1)} \circ (I^{(r+1)}\circ SL\circ O^{(r)})\circ AL^{(r)} \circ I^{(r)}\\
&\xlongequal{I^{(r+1)}=(O^{(r)})^{-1}}O^{(r+1)}\circ AL^{(r+1)} \circ  SL\circ AL^{(r)} \circ I^{(r)}\\
\end{aligned}$$

Similar to encoded implementations, `the intermediate encodings` are called `the round encodings`, and the subgroup of self-equivalences from which the round encodings ***are sampled at random*** is called `the round encoding space` $\epsilon\leq SE(SL)$.

1. self-equivalence implementations: can be implemented in software in a simple and efficient way
2. an encoded affine layer: can be implemented by a single $n\times n$ binary matrix and an $n$-bit constant
   requiring $\mathcal{O}(n^2+n)$ bits of memory and $\mathcal{O}(n^2/\log_2n)$ bit operations to evaluate
3. the S-box layer does not need to be protected, and there are no restrictions on the bit-size of the S-boxes

<font color=red>

*Proposition 1*: transform a `CEJO implementation` into a `self-equivalence implementation`

a self-equivalence implementation can be considered as a CEJO implementation with $n$-bit linear encodings by defining the encoded round function as

$$\begin{aligned}
\overline{E^{(r)}}
&=SL\circ \overline{AL^{(r)}}\\
&=(LL\circ I^{(r+1)})^{-1}\circ E^{(r)}\circ (LL\circ I^{(r)})\\
\end{aligned}
$$

$\bullet$ the memory complexity of a CEJO implementation is exponential in terms of the bit-size of the encodings
$\bullet$ self-equivalence implementations can be efficiently implementated in all cases
$\bullet$ self-equivalence implementations $\rightarrow$ CEJO implementations
$\bullet$ CEJO implementations $\nrightarrow$ self-equivalence implementations (if the self-equivalences of the S-box layer are not composed of smaller affine permutations)

</font>

# Diagonal self-equivalences

$F=F_1||\cdots ||F_{\rho}$: an arbitrary $n$-bit permutation composed of smaller $m$-bit permutations $F_i$
$(A_i,B_i)$: a self-equivalence of $F_i$ for $i=1,\cdots,\rho$
$(A_1||\cdots || A_{\rho}, B_1||\cdots || B_{\rho})$: a self-equivalence of $F$

two of the smaller permutations $(F_i,F_j)$ are the same $\rightarrow$ the transposition $(i,j)$ is also a self-equivalence of the S-box layer

----

***Definition 8.*** A $(m\times \rho)$-bit linear permutation $P$ is called an $[m,\rho]$-block permutation if $P(x_1,\dots, x_{\rho})=(x_{\pi(1)},\dots,x_{\pi(\rho)})$ for some permutation $\pi$ of $\left\{1,\dots, \rho \right\}$. We denote the set of block permutations by $\mathcal{P}_{m,\rho}$.

----

If all the small permutations $F_i$ are the same, then the set 

$$\mathcal{D}=\left\{\big((A_1||\cdots || A_{\rho})\circ P,\quad P^{-1}\circ (B_1||\cdots ||B_{\rho})\big):(A_i,B_i)\in SE(F_i), \ P\in \mathcal{P}_{m,\rho}\right\}$$

is a subgroup of $SE(F)$

----
***Definition 9.*** Let $F=F_1||\cdots||F_{\rho}$ be an $n$-bit permutation composed of $m$-bit permutations $F_i$. `An (affine) diagonal self-equivalence` $(A,B)$ of $F$ is a pair of $n$-bit affine permutations $(A,B)\in SE(F)$ such that

$$ A=(A_1||\cdots || A_{\rho})\circ P, \qquad B =P^{-1}\circ (B_1||\cdots ||B_{\rho})$$

for some $m$-bit functions $A_i$ and $B_i$ and some $[m,\rho]$-block permutation $P$

----

$\bullet$ the set of diagonal self-equivalences is a subgroup of $SE(F)$
$\bullet$ the set of diagonal self-equivalences includes $\mathcal{D}$ when all $F_i$ are the same
$\bullet$ if $(A,B)$ is a diagonal self-equivalence, then $A_i$ and $B_i$ are permutations and the matrices corresponding to $Lin(A)$ and $Lin(B)$ are diagonal block matrices up to some permutation of the blocks, i.e.,
$$Lin(A)=
\begin{pmatrix}
Lin(A_1)  & \\
 &\ddots\\ 
 & &Lin(A_{\rho})\\
\end{pmatrix}\times  P$$

$Lin(A)$: the linear part of an affine function $A$
$A=\oplus_a\circ Lin(A)$

$G(x)=(G_1(x),\dots,G_m(x))$: $(G_1,\dots,G_m)$ is the canonical or coordinate components of an $(n,m)$-bit function $G$ (the set of $n$-bit `Boolean functions`)

$$\left\{\sum_{i=1}^{m}a_iG_i \ | \ (a_1,\dots, a_m)\in \mathbb{F}_2^m \right\}$$

1. exclude the trivial component: $(a_1,\dots,a_m)=(0,\dots,0)$, $r$ components $\sum_ia_i^{(1)}G_i,\dots,\sum_ia_i^{(r)}G_i$ are `linear independent` if the $\mathbb{F}_2$-vectors $(a^{(1)},\dots,a^{(r)})$ are `linear independent`
   
2. $G=\oplus_b\circ G\circ \oplus_a$
$\bullet$ $a$: an $n$-bit constant
$\bullet$ $b$: an $m$-bit constant
$\bullet$ $(\oplus_a,\oplus_b)$: an additive self-equivalence of an $(n,m)$-bit function $G$

----
***Definition 10.**** Let $G: \mathbb{F}_2^n\rightarrow \mathbb{F}_2$ be a Boolean function. An $n$-bit vector $a$ is called a `linear structure` of $G$ if there exists a bit $b$ such that 
$$\oplus_b\circ G=G\circ \oplus_a$$

----
----

***Definition 11.*** Let $G$ be an $(n,m)$-bit function. An $n$-bit vector $a$ is a linear structure of $G$ if $a$ is a linear structure of some canonical component of $G$.

----

 Any (vectorial) Boolean function has the trivial linear structure $a=0$ and the trivial additive self-equivalence $(\oplus_0,\oplus_0)$. 

----

***Theorem 1.*** Let $F=F_1||\cdots || F_{\rho}$ be an $n$-bit permutation, where each $F_i$ is an $m$-bit permutation. If $F$ has a non-diagonal self-equivalence, then $F$ contains three permutations $(F_i,F_j,F_h),j\neq h$, such that

(a) $F_i$ has a non-trivial additive self-equivalence
(b) $F_j$ has a non-trivial linear component
(c) $F_h$ has $2^{m-r}$ linear structures that are common to $r$ linear indepedent components, for some $0<r<m$

----

$\bullet$ if $F_i=F_h$, the condition $(c)$ is redundant since $(a)$ would imply $(c)$
$\bullet$ if $F_i=F_j$, neither $(a)$ implies $(b)$ nor vice-versa

<font color=red>

1. Cryptographically strong S-boxes do not have non-trivial linear components or additive self-equivalences $\leftarrow$ they correspond to linear approximations and differentials with probability one, respectively
2. most SPN ciphers employ S-box layers $SL$ given by the concatenation of a single cryptographically  strong S-box $S$ $\rightarrow$ characterize the self-equivalence group of $SL$
   
$$\downarrow$$

----

***Corollary 1.*** Let $S$ be an $m$-bit permutation without non-trivial additive self-equivalences or linear components, and consider the $(m\times \rho)$-bit permutation $SL=S\circ \cdots \circ S$. Then, the self-equivalence group of $SL$ is given by

$$SE(SL)=\left\{\big((A_1||\cdots || A_{\rho})\circ P,\quad P^{-1}\circ (B_1||\cdots || B_{\rho})\big):(A_i,B_i)\in SE(S), \ P\in \mathcal{P}_{m,\rho}\right\}$$

In addition, $|SE(SL)|=\rho! \times |SE(S)|^{\rho}$

----

*Theorem 1*: characterize and count the number of solutions of affine equivalence problem $G=Y\circ F\circ X$
 $F$: is given by the concatenation of cryptographically strong S-boxes

</font>


# The generic attack on self-equivalence encodings 

assume: the specifications of the block cipher and the self-equivalence implementation are public, but the key and the encodings are unknown to the adversary

analyse the security of self-equivalence implementations against `key-extraction attacks` in the white-box model

the round key material is hidden in the encoded affine layer

$\bullet$ ***the binary matrix***: masks the linear part of the input and output encodings with each other
$\bullet$ ***the binary constant***: masks the round key with the round encodings

If many pairs of round keys and self-equivalences lead to the same encoded affine layer, each encoded affine layer is individually secure and the adversary is forced to solve a `disambiguation problem` involving several rounds.

<font color=blue>

describe the first generic attack on self-equivalence implementations $\rightarrow$ analyse the security of self-equivalence encodings

1. focus on the class of SPN ciphers where the S-box layer does **not** have both `a non-trivial additive self-equivalence` and `a linear component`
2. Otherwise, the S-box layer will have `a differential and a linear approximation` with probability one, which are usually avoided in most SPN designs
3. our generic attack: a reduction attack (a method to obtain new encoded affine layers for which the round encodings are restricted to a smaller encoding space)
4. perform an exhaustive search over the reduced encoing space $\rightarrow$ recover the key
5. the complexity to brute force the reduced encoding space, where the latter step is lower bounded by the cardinality of the reduced encoding space
   
6. the reduction step is based on equivalence problems ***involving the linear part of the round encodings with small solution sets***
7. these equivalence problems consider unknowns restricted to some self-equivalence subgroup of the S-boxes, and estimating the complexity of these problems highly depends on the structure of the subgroup
8. our complexity metric is the cardinality of the reduced round encoding space, which provides a lower bound of the complexity of the whole attack

</font>

<font color=darkorange>

$\bullet$ $E_k$: the encryption function for a fixed key $k$ of an arbitrary SPN cipher
$\bullet$ $SL$: the S-box layer, does not have a non-trivial additive self-equivalence of a linear component
$\bullet$ $\overline{E_k}$: a self-equivalence implementation of $E_k$
$\bullet$ $\overline{AL^{(1)}},\cdots, \overline{AL^{(n_r)}}$: encoded affine layers
$\bullet$ $\overline{AL^{(r)}}=O^{(r)}\circ (\oplus_{k^{(r)}}\circ LL)\circ I^{(r)}$: the $r$th intermediate encoded affine layer
$\bullet$ $LL$: the linear layer
$\bullet$ $(I^{(r)},O^{(r)})$: the round encodings satisfying $(O^{(r)},I^{(r+1)})\in SE(SL)$

</font>

for this class of SPN ciphers all the self-equivalences of the S-box layer are `diagonal`

consider `diagonal self-equivalences without block permutations` $\leftarrow$ they do not significantly impact the reduction step

the round encoding space of the self-equivalence implementation is $SE(S_1)||\cdots||SE(S_{\rho})$


<font color=red>

1. the attack does not target the external encodings, which are assumed to be random affine permutations 
2. a self-equivalence implementation without external encodings is trivially insecure

</font>

$$\downarrow$$

given an intermediate encoded affine layer $\overline{AL^{(r)}}$, the core step in the reduction attack is to ***obtain another encoded affine layer differing only in the $i$th block of the output encoding***, i.e.,

$$\widehat{AL^{(r)}}=O'^{(r)}\circ AL^{(r)}\circ I^{(r)}, O'^{(r)}_h=O_h^{(r)} \forall h\neq i$$

$Lin(O'^{(r)}_i)$ belongs to a proper subgroup of $Lin(RSE(S_i))$

apply this step for all output encoding blocks of $\overline{AL^{(r)}}$ and $\overline{AL^{(r-1)}}$ and use the self-equivalence property $SL=I^{(r)}\circ SL\circ O^{(r-1)}$  $\rightarrow$ reduce the encoding space of the whole round encoding $(I^{(r)},O^{(r)})$

<font color=red>

1. the reduction of the linear part of the output encoding is based on equivalence problem
2. do not reduce the constant part of the round encodings to avoid equivalence problems dependent on the round key

</font>

$$\downarrow$$

1. describe two generic classes of equivalence problems that contain the output round encoding as a particular solution 
   (1) the centralizer problems
   (2) the  asymmetric problems

   $$\downarrow$$

   combine and do not assume any particular structure in the self-euqivalences groups of the S-boxes

   $$\downarrow$$

   other equivalence problems can be considered by exploiting specific properties of the self-equivalence groups


2. reduce the round encoding space to their solution sets



## The Centralizer Problems

<font color=red>

***the centralizer problem***: a class of `univariate linear equivalence problems` that allow reducing the round encoding space to the centralizer of linear layer blocks

the centralizer of an $n$-bit function $F$ is defined as 

$$C(F)=\left\{A\in GL_n: \ A\circ F=F\circ A\right\}$$

----
***Proposition 2.*** Let $\overline{AL}=O\circ AL\circ I$ be the encoded affine layer of an intermediate round and consider the linear layer block $L=LL_{i,j}\circ (LL^{-1})_{j,i}$. Then, the univariate linear equivalence problem, 

$$ Lin(\overline{AL})_{i,j}\circ Lin(\overline{AL}^{-1})_{j,i}=X\circ L\circ X^{-1}, \quad X\in Lin(RSE(S_i))  \qquad (1)$$

contains $X_0=Lin(O_i)$ as a solution. In particular, its solution set is given by

$$ \mathcal{S}=Lin(O_i)\circ \big(C(L)\cap Lin(RSE(S_i))\big)$$.

----

</font>

1. ***without the restriction*** $X\in Lin(RSE(S_i))$, a solution of *Eqn(1)* could be easily obtained with complexity $\mathcal{O}(m^{\omega})$, for $2<\omega<3$
2. ***the restriction*** makes the complexity harder to estimate, which strongly depends on the strucure of $Lin(RSE(S_i))$
   <font color=red>

   `linear equivalence algorithm`:
   in general, one can perform an exhaustive search over $Lin(RSE(S_i))$ using the algorithm of ***Birykov et al***. 
   the complexity of this approach is at least $2^{3m}$ and is also lower bounded by $|Lin(RSE(S_i))|$

   </font>

$X_0=Lin(O_i)\circ H$: an arbitrary solution of *Eqn(1)*, for some unknown $H\in C(L)\cap Lin(RSE(S_i))$

$X_0\in Lin(RSE(S_i))$ $\rightarrow$ exists an $m$-bit constant $x_0$ such that $\oplus_{x_0}\circ X_0 \in RSE(S_i)$

$C=(C_1||\cdots||C_{\rho})$: the right self-equivalence of $SL$ where $C_i=(\oplus_{x_0}\circ X_0)^{(-1)}$ and $C_h=Id \quad \forall h\neq i$

$$\downarrow$$

obtain another encoded affine layer

$$\begin{aligned}
\widehat{AL}
&=C\circ \overline{AL}\\
&=O'\circ AL\circ I, \quad O'_h=O_h \quad \forall h \neq i\\
\end{aligned}
$$

with (unknown) self-equivalences as round encodings and that only differs in the $i$th block of the output encoding
the linear part of $O'_i$ is restricted to the subgroup $Lin(RSE(S_i))\cap C(L)$

<font color=red>

reduce the encoding space associated to the linear part of the $i$th output encoding block, $Lin(RSE(S_i))$, to the subgroup $Lin(RSE(S_i))\cap C(L)$

this subgroup is related to the self-equivalences of the linear layer blocks

$$\Downarrow$$

the security of self-equivalence implementations depends on:
1. the self-equivalences of the S-box layer
2. the self-equivalence structure of the linear layer  

</font>
 
consider similar equivalence problems involving other blocks of $\overline{AL}$ and $LL$

$$A=A_{i_1,j_1}A_{i_2,j_2}\cdots A_{i_s,j_s}A_{j_s,i_{s+1}}, \quad i_1=i_{s+1}$$

$$A_{i_h,j_h}\in \left\{Lin(AL)_{i_h,j_h}, \big(Lin(AL^{-1})_{i_h,j_h}\big)^{-1}\right\}$$

$$A_{j_h,i_{h+1}}\in \left\{Lin(AL^{-1})_{j_h,i_{h+1}}, \big(Lin(AL)_{j_h,i_{h+1}}\big)^{-1}\right\}$$

## The Asymmetric Problems



# References

Baek C H, Cheon J H, Hong H. White-box AES implementation revisited[J]. Journal of Communications and Networks, 2016, 18(3): 273-287.

Derbez P, Fouque P A, Lambin B, et al. On recovering affine encodings in white-box implementations[J]. IACR Transactions on Cryptographic Hardware and Embedded Systems, 2018: 121-149.

Biryukov A, De Canniere C, Braeken A, et al. A toolbox for cryptanalysis: Linear and affine equivalence algorithms[C]. International Conference on the Theory and Applications of Cryptographic Techniques. Springer, Berlin, Heidelberg, 2003: 33-50.

Dinur I. An improved affine equivalence algorithm for random permutations[C]. Annual International Conference on the Theory and Applications of Cryptographic Techniques. Springer, Cham, 2018: 413-442.

Michiels W, Gorissen P, Hollmann H D L. Cryptanalysis of a generic class of white-box implementations[C]. International Workshop on Selected Areas in Cryptography. Springer, Berlin, Heidelberg, 2008: 414-428.

Billet O, Gilbert H, Ech-Chatbi C. Cryptanalysis of a white box AES implementation[C]. International Workshop on Selected Areas in Cryptography. Springer, Berlin, Heidelberg, 2004: 227-240.

