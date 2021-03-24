<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
<script type="text/x-mathjax-config">
    MathJax.Hub.Config({ tex2jax: {inlineMath: [['$', '$']]}, messageStyle: "none" });
</script>
<!-- TOC -->

- [Define 1 (Substitution-Linear Transformation Cipher (SLT cipher))](#define-1-substitution-linear-transformation-cipher-slt-cipher)
- [Problem Formulation and Notation](#problem-formulation-and-notation)
  - [Property 1](#property-1)
  - [Definition 2](#definition-2)
  - [Definition 3](#definition-3)
  - [Main Result](#main-result)
  - [Remark 1](#remark-1)
- [1. Determination of the Encodings Up to an Affine Part](#1-determination-of-the-encodings-up-to-an-affine-part)
  - [Theorem 1](#theorem-1)
  - [Theorem 2](#theorem-2)
  - [Property 2](#property-2)
- [2. Transformation into Table Network](#2-transformation-into-table-network)
- [3. Transformation into SAT Cipher](#3-transformation-into-sat-cipher)
  - [Definition 4 (Generic Substitution-Affine Transformation Cipher (generic SAT cipher))](#definition-4-generic-substitution-affine-transformation-cipher-generic-sat-cipher)
  - [description of this step](#description-of-this-step)
  - [A simple observation](#a-simple-observation)
  - [To do](#to-do)
- [4. Extracting the Key](#4-extracting-the-key)
  - [description the last step of our cryptanalysis](#description-the-last-step-of-our-cryptanalysis)
  - [more precise](#more-precise)
  - [Basic algorithm for finding the round key of a round $r$](#basic-algorithm-for-finding-the-round-key-of-a-round-r)
    - [Solving the Linear Equivalence Problem for Matrices](#solving-the-linear-equivalence-problem-for-matrices)
      - [Definition 5](#definition-5)
      - [Definition 6 (Linear Equivalence Problem of Matrices (LEPM))](#definition-6-linear-equivalence-problem-of-matrices-lepm)
      - [algotithm LEPM solver$(X,Y)$](#algotithm-lepm-solverxy)
      - [Theorem 3](#theorem-3)
- [Reference](#reference)

<!-- /TOC -->

# Define 1 (Substitution-Linear Transformation Cipher (SLT cipher)) 

A cipher is called an `SLT cipher`[<sup>1</sup>](#refer-anchor-1)  if it can be specified as folows.
   
1. consists of $R$ rounds for an $R>1$
2. a single round $r$ is a bijective funcion $\mathcal{F}_{SLT}^{(r)}(x_1,x_2,\cdots,x_s)$ on $GF(2)^n$, $x_i\in GF(2)^n$ and $n=m \cdot s$ 
**function consists of the following Operations:**
   ***<font color=red> Confusion  </font>***
   i. $xoring$ an $n$-bit round key $k^{(r)}=(k_1^{(r)},k_2^{(r)},\cdots,k_s^{(r)})$ to its input, the value $y_i=k_i^{(r)}\oplus x_i$ is computed
   ii. the round computes $z_i=S_i^{(r)}(y_i)$ for all $y_i$, where the (non-linear) invertible S-boxes $S_1^{(r)},S_2^{(r)},\cdots,S_s^{(r)}$ are part of the cipher specification and thus key-independent
   ***<font color=red>Diffusion </font>***
   iii. multiply the outcome $z=(z_1,z_2,\cdots,z_s)\in GF(2)^n$ of the S-boxes with an $n\times n$ invertible matrix $M^{(n)}$ over $GF(2)$
3. the `diffusion matrix` is also part of the cipher specification

<font color=red>

$$\mathcal{F}_{SLT}^{(r)}=M^{(r)}\circ S^{(r)}\circ \oplus_{k^{(r)}} \qquad (1)$$

$\bullet$ $M^{(r)}$ : the diffusion matrix
$\bullet$ $S^{(r)}$ : the S-box diagonal map with as components the S-boxes $S_i^{(r)}$ 
$\bullet$ $\oplus_{k^{(r)}}$ : the round-key addition map $x\mapsto x\oplus k^{(r)}$, a diagnoal map with as components the maps $x_i\mapsto x_i \oplus k_i^{(r)}$

</font>

# Problem Formulation and Notation

**what kind of information we can obtain from such an implementation by a white-box attack**

**how Chow et al. derive a white-box implementation of a block cipher**

1. in each round of the block cipher, only performs a sequence of table lookups
2. obfuscate the lookup tables by encoding theie inputs and outputs
$f_{in}$: bijective function to encode the input of a table $T$ 
$f_{out}$: bijective function to encode the output of a table $T$ 
use $f_{out}\circ T\circ f_{in}^{-1}$ replace table $T$
3. under a mild condition on the diffusion matrix $\Rightarrow$ extract the `round keys` from the white-box implemenration of any `SLT  cipher`  $\Rightarrow$ `key scheduling algorithm` is **invertible** $\Rightarrow$ derive the `main key`

## Property 1

1. In a white-box attack, an attacker has for each round $r$ and for each $m$-bit input word $x_i$ of round $r$ **access to the encoded version $f_i^{(r)}(x_i)$ of $x_i$**.
2. The attacker also **knows the values of $r$ and $i$** that are associated with $f_i^{(r)}(x_i)$.
3. The attacker **does not know the value of the $m$-bit input word $x_i$ nor the definition of the function $f_i^{(r)}$**, which is an *arbitrary $m$-bit bijective function*.

## Definition 2

$N$ is an $n\times n$ matrix with $n=m\cdot s$,consider $N$ to be partitioned into $s$ vertival stripes of size $n\times m$
the $j$th strip by $N_j$:

$$ (N_j)_{x,y}=N_{x,(j-1)\cdot m +y}$$

the rows and columns have indices in $\left\{1,2,\cdots, n\right\}$

consider each strip $N_j$ to be partitioned further into $s$ blocks $N_{i,j}$ of size $m\times m$.

$$(N_{i,j})_{x,y}=N_{(i-1)m+x,(j-1)m+y}$$

$$\Downarrow$$

$$ N=(N_1\cdots N_s)=\begin{pmatrix}
N_{1,1} &　N_{1,2} & \cdots & N_{1,s}　\\
N_{2,1} &　N_{2,2} & \cdots & N_{2,s}　\\
\vdots  &　\vdots  & \vdots & \vdots　\\
N_{s,1} &　N_{s,2} & \cdots & N_{s,s}　\\
\end{pmatrix}
$$

the $i$th row:

$$N(i)=\begin{pmatrix}N_{i,1} & N_{i,2} & \cdots & N_{i,s}\end{pmatrix}$$

of blocks of $N$ as the $i$th block row of $N$

## Definition 3

$N$ is an $n\times n$ matrix with $n=m\cdot  s$


a subset $U \subseteq \left\{1,2,\cdots,s\right\}$ is `a spanning block set` for block row $i$ if the collection of all the $m$-bit columns from the blocks $N_{i,j}$ with $j\in U$ spans $GF(2)^m$

two subsets $U,V\subseteq \left\{1,2,\cdots,s\right\}$ with $U\cap V=\emptyset$ both represent spanning block sets for block raw $i$

$$ \Downarrow$$

block row $i$ has `disjoint spanning block sets`

## Main Result

`MDS (Maximum Distance Separable)` matrices are often used as **diffusion matrix** in block ciphers because of their **good diffusion properties**.

In an MDS diffusion matrix $N$ each block $N_{i,j}$ is **non-singular**, which <u>implies that any pair of blocks from a block row $i$ defines a spanning block set.</u>

$\bullet$ an SLT cipher for which the diffusion matrices have the property that all their block rows have **disjoint spanning block sets**
$\bullet$ a white box implementaton for this cipher that **satisfies `Property 1`**

$$\Downarrow$$

present an algorithm for extracting the round key of any round $r$ with $1<r<R$
<u>does not cover the first and last round of the cipher</u>

$$\Uparrow$$

in order not to change the functionality of the white-box implementation, the input of the first round and the output of the last round cannot be encoded

$$\Downarrow$$

Chow et al. propose to add the `external encodings` and to either **undo these encodings elsewhere in the software** or to **include these encodings in the definition of the block ciper that is implemented**

$$\Downarrow$$

By applying our crypranalysis, an attacker can **determine the output encoding of the first round and the input encoding of the last round**

$$\Downarrow$$

gives the attacker:
$\bullet$ *the plain output of the first round* 
$\bullet$ *the plain input to the last round*

$$\Downarrow$$

the first and last round can be attacked

$$\downarrow$$

Let $g_1,g_2,\cdots,g_s$ be maps on $m$-bit vectors, and let $n=m\cdot s$.
The map $g=(g_1,\cdots, g_s)$ defined by 

$$ g(x)=(g_1(x_1),g_2(x_2),\cdots,g_s(x_s))$$

for each $n$-bit vector $x=(x_1,x_2,\cdots, x_s)\in GF(2)^n$ with $x_i\in GF(2)^m$ is called the `diagonal map` with components $g_1,\cdots,g_s$.

## Remark 1

$c$ is a vector, $N$ is a matrix, the addition map $\oplus_c$ is always **diagonal** and the matrix map $N$ is **diagonal** if and only if $N$ is a `block diagonal matrix`.

$N$ is called a `block diagonal matrix` if all off-diagonal blocks $N_{i,j},i\neq j$, are zero.

an affine map $\alpha:x \mapsto a \oplus Ax $ is a diagonal map if and only if $A$ is a block diagonal matrix

the $i$th component of the diagonal map $x \mapsto Nx$ associated with a block diagonal matrix $N$ is just the diagonal block $N_{i,i}$ of $N$

$$\Downarrow$$

<font color=red>

$$\mathcal{F}_{SLT}^{(r)}=M^{(r)}\circ S^{(r)}\circ \oplus_{k^{(r)}} \qquad (1)$$

$\bullet$ $M^{(r)}$ : the diffusion matrix
$\bullet$ $S^{(r)}$ : the S-box diagonal map with as components the S-boxes $S_i^{(r)}$ 
$\bullet$ $\oplus_{k^{(r)}}$ : the round-key addition map $x\mapsto x\oplus k^{(r)}$, a diagnoal map with as components the maps $x_i\mapsto x_i \oplus k_i^{(r)}$

</font>

# 1. Determination of the Encodings Up to an Affine Part

According to `Property 1`: **an attacker has access to the encoded version 

$$\widetilde{x}_i= f_i^{(r)}(x_i)$$

of each input word $x_i$ of a round $r$, $f_i^{(r)}$ is an unknown bijective function 

<font color=red>$\bullet$</font> ***<font color=red>how to determine the encodings up to an affine apart</font>***

Consider a fixed round $r$ of the white-box implementation with $1\leq r<R$

two disjoint spanning block sets for block row $i$ of $M$:

$U=\left\{u_1,u_2,\cdots,u_l\right\}$

$V=\left\{v_1,v_2,\cdots,v_{l'}\right\}$

assume $U\cup V=\left\{1,2,\cdots, s\right\}$, i.e., $l'=s-l$

partition the $s$ input words of a round input into two parts:
(i) words that are input to an S-box $S_i$ with $i\in U$
(ii) words that are input to an S-box $S_i$ with $i\in V$

$$\Downarrow$$

$\widetilde{x}'$: the vector containing all $l$ input words $\widetilde{x}$ with $i\in U$

$\widetilde{x}''$: the vector containing all $l'$ input words $\widetilde{x}$ with $i\in V$

the $i$th ***output word*** $\widetilde{z}_i$ of this round $r$:

$$\widetilde{z}_i=h(\widetilde{x}',\widetilde{x}'')=f_i^{(r+1)}(\psi_U(\widetilde{x}')\oplus \psi_V(\widetilde{x}''))$$

$$\Uparrow$$

$$\psi_U(\widetilde{x}')=\bigoplus\limits_{j\in U} \psi_j(\widetilde{x}_j)$$

$$\psi_V(\widetilde{x}'')=\bigoplus\limits_{j\in V} \psi_j(\widetilde{x}_j)$$

$$\psi_j(\widetilde{x}_j)=M_{i,j} \circ S_j \circ \oplus_{k_j} \circ (f_i^{(r)})^{-1}(\widetilde{x}_j)$$

According to `Property 1`: an attacker has access to function $h$, but not, for instance, to function $\psi_j$

both $\psi_U$ and $\psi_V$ are ***surjective*** on the vector space $GF(2)^m$

## Theorem 1

In $\mathcal{O}(s+m\cdot 2^m)$ time, we can construct  sets $W_U$ and $W_V$, with $|W_U|=|W_V|=2^m$, such that

$\bullet$ for each fixed $\widetilde{x}''$, the map $\widetilde{x}'\mapsto h(\widetilde{x}',\widetilde{x}'')$ is a bijection on $W_U$

$\bullet$ for each fixed $\widetilde{x}'$, the map $\widetilde{x}''\mapsto h(\widetilde{x}',\widetilde{x}'')$ is a bijection on $W_V$

$$\Downarrow$$

$h_c$: the bijective function $\widetilde{x}'\mapsto h(\widetilde{x}',c)$ from $W_U$ onto $GF(2)^m$

$$c_1,c_2 \in W_V$$

$$d=\psi_V(c_1)\oplus \psi_V(c_2)$$

$$\text{if  }\widetilde{z}_i=h_{c_2}(\widetilde{x}')=f_i^{(r+1)}(\psi_U(\widetilde{x}')\oplus \psi_V(c_2))$$

$$\psi_U(\widetilde{x}')= (f_i^{(r+1)})^{-1}(\widetilde{z}_i)\oplus \psi_V(c_2)$$

$$\downarrow$$

<font color=blue>

$$\begin{aligned}
h_{c_1}\circ h_{c_2}^{-1}(\widetilde{z}_i)
&\xlongequal{\widetilde{z}_i=h_{c_2}(\widetilde{x}')}h_{c_1}\circ h_{c_2}^{-1}(h_{c_2}(\widetilde{x}'_i))\\
&=h_{c_1}(\widetilde{x}'_i)\\
&=f_i^{(r+1)}(\psi_U(\widetilde{x}')\oplus \psi_V(c_1))\\
&\xlongequal{\psi_U(\widetilde{x}')= (f_i^{(r+1)})^{-1}(\widetilde{z}_i)\oplus \psi_V(c_2)}f_i^{(r+1)}((f_i^{(r+1)})^{-1}(\widetilde{z}_i)\oplus \psi_V(c_2)\oplus \psi_V(c_1))\\
&\xlongequal{d=\psi_V(c_1)\oplus \psi_V(c_2)}f_i^{(r+1)}((f_i^{(r+1)})^{-1}(\widetilde{z}_i)\oplus d)\\
&=f_i^{(r+1)}\circ \oplus_d\circ(f_i^{(r+1)})^{-1}(\widetilde{z}_i)\\
\end{aligned}
$$

</font>

$$h_{c_1}\circ h_{c_2}^{-1}(\widetilde{z}_i)=f_i^{(r+1)}(\psi_U(\widetilde{x}')\oplus \psi_V(c_1))=f_i^{(r+1)}\circ \oplus_d\circ(f_i^{(r+1)})^{-1}(\widetilde{z}_i)$$

$$\downarrow$$

$$\text{fix }c_1\in W_V$$

$$\text{to each } c_2\in W_V \text{, there corresponds a unique } d\in GF(2)^m$$

$$\downarrow$$

$\widetilde{x}'$ run through $W_U$, for each $d\in GF(2)^m$, 

<font color=red>

construct a lookup table for the function $f_i^{(r+1)}\circ \oplus_d \circ (f_i^{(r+1)})^{-1}$

$$\downarrow$$

use the result of Billet et al.[<sup>2</sup>](#refer-anchor-2) to determine $f_i^{(r+1)}$ up to an affine apart

</font>

## Theorem 2

$f_i^{(r+1)}$ be an arbitrary bijective function on $GF(2^m)$

$$ \downarrow$$

the set of functions $\left\{f_i^{(r+1)}\circ \oplus_d \circ (f_i^{(r+1)})^{-1} \quad |\quad d \in GF(2^m)\right\}$

construct in $\mathcal{O}(2^{3m})$ time a function $g_i^{(r+1)}$ for which the map $\alpha_i^{(r+1)} = g_i^{(r+1)} \circ f_i^{(r+1)}$ is ***affine***

$$ \Downarrow$$

Combine `Property 1` with `theorem 1` and `theorem 2`: for any input word $x_i$, an attacker can obtain the value of the affine map 

$$\alpha_i^{(r+1)} (x_i)= g_i^{(r+1)} \circ f_i^{(r+1)}(x_i)$$

$$ \Downarrow$$

## Property 2

$\bullet$ In a white-box attack, an attacker has for each round $r$ with $2 \leq  r \leq  R$ and for each $m$-bit input word $x_i$ of round $r$ **access to the encoded version $\widetilde{x}_i=\alpha_i^{(r)}(x_i)$ of $x_i$**

$\alpha_i^{(r)}(x_i)=A_i^{(r)}x\oplus a_i^{(r)}$ is an $m$-bit affine function

$\bullet$ The attacker also **knows the values of $r$ and $i$** that are associated with $f_i^{(r)}(x_i)$

# 2. Transformation into Table Network

for each round $r$ with $1<r<R$, an attacker has access to the input-output behavior of the function:

<font color=red>

$$\mathcal{G}_{SLT}^{(r)}=\alpha^{(r+1)}\circ \mathcal{F}_{SLT}\circ (\alpha^{(r)})^{-1} \qquad (2)$$

</font>

<center>

fix a round $r$

</center>

$$ \Downarrow$$

$$\mathcal{G}_{SLT}=\alpha^{(r+1)}\circ \mathcal{F}_{SLT}\circ (\alpha^{(r)})^{-1}$$

$$\mathcal{F}_{SLT}=M\circ S\circ \oplus_k$$

$$N=A^{(r+1)}M$$

$$R=S\circ \oplus_k \circ (\alpha^{(r)})^{-1}$$

<font color=blue>

$$\begin{aligned}
\mathcal{G}_{SLT}
&=\alpha^{(r+1)}\circ \mathcal{F}_{SLT}\circ (\alpha^{(r)})^{-1}\\
&\xlongequal{\mathcal{F}_{SLT}=M\circ S\circ \oplus_k}\alpha^{(r+1)}\circ M\circ S\circ \oplus_k \circ (\alpha^{(r)})^{-1}\\
&\xlongequal{R=S\circ \oplus_k \circ (\alpha^{(r)})^{-1}}\alpha^{(r+1)}\circ M\circ R\\
&\xlongequal{\alpha^{(r+1)}=A^{(r+1)}\oplus a^{(r+1)}}(A^{(r+1)}\oplus a^{(r+1)})\circ M\circ R\\
&=\oplus_{a^{(r+1)}}\circ A^{(r+1)}\circ M\circ R\\
&\xlongequal{N=A^{(r+1)}M}\oplus_{a^{(r+1)}}\circ N\circ R
\end{aligned}
$$

</font>

$$ \Downarrow$$

<font color=red>

$$\mathcal{G}_{SLT}(\widetilde{x})=\oplus_{a^{(r+1)}}\circ N\circ R(\widetilde{x}) =a^{(r+1)}\oplus \bigoplus_{j=1}^sN_j\circ R_j(\widetilde{x}_j)\qquad (3)$$

$N_j$: the $j$th strip of matrix $N$ 

$R_j$: the $j$th component of diagonal map $R$

</font>

$$ \downarrow$$

$\bullet$ define tables $T_1,T_2,\cdots,T_s$ such that 

$$ \mathcal{G}_{SLT}(\widetilde{x}_1,\widetilde{x}_2,\cdots,\widetilde{x}_s)=\bigoplus_{i=1}^sT_i(\widetilde{x}_i)$$

$\bullet$ define $T_j$ as

$$T_j(\widetilde{x}_j)=\begin{cases} 
\mathcal{G}_{SLT}((\widetilde{x}_1,0,\cdots,0)), & \text{if } j=1;\\
\mathcal{G}_{SLT}((0,\cdots,0,\widetilde{x}_j,0,\cdots,0))\oplus \mathcal{G}_{SLT}(0), &\text{otherwise}.
\end{cases}$$

$$\mathcal{G}_{SLT}(0)=\oplus_{a^{(r+1)}}\circ N\circ R(0)$$

for all $j\geq 1$,

<font color=blue>

$$\begin{aligned}
\mathcal{G}_{SLT}((0,\cdots,0,\widetilde{x}_j,0,\cdots,0))
&\xlongequal{\mathcal{G}_{SLT}(\widetilde{x})=\oplus_{a^{(r+1)}}  \circ \bigoplus_{j=1}^sN_j\circ R_j(\widetilde{x}_j)}\oplus_{a^{(r+1)}}\circ N\circ R(0)\oplus (N_j \circ R_j(0))\oplus (N_j \circ  R_j(\widetilde{x}_j ))\\
&\xlongequal{\mathcal{G}_{SLT}(0)=\oplus_{a^{(r+1)}}\circ N\circ R(0)}\mathcal{G}_{SLT}(0)\oplus (N_j \circ R_j(0))\oplus (N_j \circ  R_j(\widetilde{x}_j ))\\
&=\mathcal{G}_{SLT}(0)\oplus N_j \circ R_j(0)\oplus N_j \circ  R_j(\widetilde{x}_j )
\end{aligned}
$$

</font>

$$\mathcal{G}_{SLT}((0,\cdots,0,\widetilde{x}_j,0,\cdots,0))=\mathcal{G}_{SLT}(0)\oplus N_j \circ R_j(0)\oplus N_j \circ  R_j(\widetilde{x}_j )$$

$$\downarrow$$

$$T_j(\widetilde{x}_j)=\begin{cases} 
a^{(r+1)}\oplus N_1 \circ R_1(\widetilde{x}_1)\oplus \bigoplus\limits^s_{i=2}N_i \circ R_i(0), & \text{if } j=1;\\
N_j\circ R_j(0)\oplus N_j \circ R_j(\widetilde{x}_j),&\text{for }j=2,\cdots,s.
\end{cases} \qquad (4)$$

$$\downarrow$$

<font color=darkorange>

$$\mathcal{G}_{SLT}(\widetilde{x})=\bigoplus\limits_{i=j}^sT_j(\widetilde{x}_j)$$

</font>

# 3. Transformation into SAT Cipher

`SLT cipher` $\rightarrow$ merge a key-addition operation into the S-box operation
$$S_i \circ \oplus_{k_i}$$

$\rightarrow$ viewed as a `generic SAT cipher`

## Definition 4 (Generic Substitution-Affine Transformation Cipher (generic SAT cipher))

A cipher is called an `generic SAT cipher` if it can be specified as folows.

1. consists of $R$ rounds for an $R\geq 1$
2. a single round $r$ is a bijective function $\mathcal{F}_{gen-SAT}(x_1,x_2,\cdots,x_s)$ on $GF(2)^n$ with $x_i\in GF(2)^m$ and $n=m\cdot s$
**function consists of the following Operations:**
   ***<font color=red> First  </font>***
   the values $y_i=Q_i^{(r)}(x_i)$ are computed for all input words $x_i$
   the specification of an S-box $Q_i^{(r)}$ is derived from the key
   ***<font color=red>Next </font>***
   an invertible affine function $\epsilon^{(r)}(y)=E^{(r)}\cdot y \oplus e^{(r)}$ is applied to the outcome $y=(y_1,y_2,\cdots,y_s)\in GF(2)^{n}$ of the S-boxes
   The specification of this affine function $\epsilon^{(r)}$ is also derived from the key 

$$\downarrow$$

<font color=red>

$$\mathcal{F}_{gen-SAT}=\epsilon \circ Q$$

$\bullet$ $\epsilon$: affine, specified by the key 
$\bullet$ $Q$: a diagnal map, specified by the key 

</font>

## description of this step

According to `Expression (3)` 

<font color=blue>

$$\begin{aligned}
\mathcal{G}_{SLT}
&=\oplus_{a^{(r+1)}}\circ N\circ R \\
&\xlongequal{\theta=\oplus_{a^{(r+1)}}\circ N}\theta \circ R
\end{aligned}
$$

</font>

$$ \mathcal{G}_{SLT}=\theta \circ R$$

$\bullet$ $\theta=\oplus_{a^{(r+1)}}\circ N$: affine
$\bullet$ $R$: a diagnal map

$$\downarrow$$

<center>
<u>

$\theta, R$ are not accessible to an attacker, the form $\mathcal{G}_{SLT}=\theta \circ R$ is not suitable for cryptanalysis

</u>

$\epsilon, Q$ are both accessible to an attacker

</center>

$$\Downarrow$$

***<font color=red>use $\mathcal{G}_{SLT}=\epsilon \circ Q$ to attack the round key of the original SLT cipher $\mathcal{F}_{SLT}$</font>***

## A simple observation

the diffusion matrix $M$: ***invertible***
the diagonal matrix $A^{(r+1)}$ from the affine map $\alpha ^{(r+1)}$: ***invertible***

$$\Downarrow$$

the matrix $N=A^{(r+1)}M=(N_1\cdots N_s)$: ***invertible*** 

$$\Downarrow$$

the columns of each of the $n\times m$ matrices $N_j$ are also ***independent***

$$\downarrow$$

<u>

$U_j$: the $m$-dimensional vector space spanned by the columns of $N_j$

</u>

consider again `Expression (4)` 

$$T_j(\widetilde{x}_j)=\begin{cases} 
a^{(r+1)}\oplus N_1 \circ R_1(\widetilde{x}_1)\oplus \bigoplus\limits^s_{i=2}N_i \circ R_i(0), & \text{if } j=1;\\
N_j\circ R_j(0)\oplus N_j \circ R_j(\widetilde{x}_j),&\text{for }j=2,\cdots,s.
\end{cases} \qquad (4)$$

for the lookup tables $T_j$

$$\downarrow$$

$$\omega_1=a^{(r+1)}\oplus  N\circ R(0)\oplus N_1 \circ R_1(0)$$

$$\omega_j=N_j\circ R_j(0), \qquad j=2,\cdots,s$$

<font color=blue>

$$\begin{aligned}
T_j(\widetilde{x}_j)
&=\begin{cases} 
a^{(r+1)}\oplus N_1 \circ R_1(\widetilde{x}_1)\oplus \bigoplus\limits^s_{i=2}N_i \circ R_i(0), & \text{if } j=1;\\
N_j\circ R_j(0)\oplus N_j \circ R_j(\widetilde{x}_j),&\text{for }j=2,\cdots,s.
\end{cases}\\
&\xlongequal{\mathcal{G}_{SLT}(0)=\oplus_{a^{(r+1)}}\circ N\circ R(0)}\begin{cases} 
a^{(r+1)}\oplus N\circ R(0)\oplus N_1 \circ R_1(0)\oplus N_1 \circ R_1(\widetilde{x}_1), & \text{if } j=1;\\
N_j\circ R_j(0)\oplus N_j \circ R_j(\widetilde{x}_j),&\text{for }j=2,\cdots,s.
\end{cases}\\
&\xlongequal{\omega_j=\begin{cases}a^{(r+1)}\oplus  N\circ R(0)\oplus N_j \circ R_j(0),& j=1\\
N_j\circ R_j(0), &j=2,\cdots,s
\end{cases}}\omega_j \oplus N_j \circ R_j(\widetilde{x}_j)
\end{aligned}
$$

$$\Downarrow$$

$$T_j(\widetilde{x}_j)=\omega_j \oplus N_j \circ R_j(\widetilde{x}_j)$$

</font>

$$\text{im}(T_j)=\omega_j \oplus U_j$$ 

($\text{im}(T_j)$: ***the range of function*** $T_j$)

the $2^m$ rows of lookup table $T_j$ together comprises all vectors from $\omega_j \oplus  U_j$

## To do

(1)
select an arbitrary row $v_j$ from each table $T_j$, and define

$$e=\bigoplus\limits_{j=1}^s v_j$$

$$\downarrow$$

***<u><font color=darkorange>for each $\widetilde{x}_j$, $v_j\oplus T_j(\widetilde{x}_j)\in U_j$</font></u>***

$$\Uparrow$$

$v_j$ and $T_j(\widetilde{x}_j)$ are rows of $T_j$, $u_j$ and $u'_j$ in $U_j$

$$v_j=\omega_j\oplus u_j$$

$$T_j(\widetilde{x}_j)=\omega_j \oplus u'_j$$

$$\Downarrow$$

$$v_j\oplus T_j(\widetilde{x}_j)=u_j\oplus u'_j \in U_j$$

(2)
select words $\widetilde{x}_{j,1},\cdots,\widetilde{x}_{j,m}$ for which the vectors $e_{j,i}=v_j \oplus T_j(\widetilde{x}_{j,i})$ in $U_j$ are independent $\Rightarrow$ 

construct a basis $e_{j,1}\cdots e_{j,m}$ for each $U_j$ $\Rightarrow$ 

define the submatrices $E_j$ of $E=(E_1,E_2,\cdots,E_s)$: $E_j=(e_{j,1}\cdots e_{j,m})$

<font color=blue>

$$v_j\oplus T_j(\widetilde{x}_j)\in U_j$$

$$\Downarrow$$     

$$T_j(\widetilde{x}_j)\in v_j \oplus U_j$$

</font>
<font color=darkorange>

<center>

the $m$ columns of $E_j$ span $U_j$

$T_j(\widetilde{x}_j)\in v_j \oplus U_j$

</center>

$$\Downarrow$$

for each $\widetilde{x}_j  \in GF(2)^m$, there is a vector $Q_j(\widetilde{x}_j)\in GF(2)^m$ such that

$$T_j(\widetilde{x}_j)=v_j\oplus E_jQ_j(\widetilde{x}_j)$$

</font>

(3)
consider $Q_j$ as a map from $GF(2)^m$ to $GF(2^m)$, is a bijection

let $Q=(Q_1,\cdots,Q_s)$: diagonal map with components $Q_j$

define the affine map $\epsilon = \oplus_e\circ E$

$$\Downarrow$$

<font color=blue>

$$\begin{aligned}
\epsilon \circ Q(\widetilde{x})
&\xlongequal{\epsilon = \oplus_e\circ E}e\oplus E\circ Q(\widetilde{x})\\
&\xlongequal{e=\bigoplus\limits_{j=1}^s v_j}\bigoplus\limits_{j=1}^s v_j \oplus \bigoplus\limits_{j=1}^sE_jQ_j(\widetilde{x}_j)\\
&\xlongequal{T_j(\widetilde{x}_j)=v_j\oplus E_jQ_j(\widetilde{x}_j)}\bigoplus_{j=1}^sT_j(\widetilde{x}_j)\\
&\xlongequal{\mathcal{G}_{SLT}(\widetilde{x})=\bigoplus\limits_{j=1}^sT_j(\widetilde{x}_j)}\mathcal{G}_{SLT}(\widetilde{x})\\
\end{aligned}
$$

</font>

$$\epsilon \circ Q(\widetilde{x})=e\oplus E\circ Q(\widetilde{x})=\bigoplus\limits_{j=1}^s v_j \oplus \bigoplus\limits_{j=1}^sE_jQ_j(\widetilde{x}_j)=\bigoplus_{j=1}^sT_j(\widetilde{x}_j)=\mathcal{G}_{SLT}(\widetilde{x})$$

$$\Downarrow$$

<font color=red>

$$\mathcal{G}_{SLT}=\epsilon \circ Q$$

construct the `generic SAT cipher`

</font>

# 4. Extracting the Key

## description the last step of our cryptanalysis

derive a relation between ***the S-boxes $S_i^{(r)}$ of the white-boxed `SLT cipher`*** and ***the S-boxes $Q_i^{(r)}$ of the `generic SAT cipher`***

$$\downarrow$$

$$Q_i^{(r)}=\gamma_i^{(r)}\circ S_i^{(r)}\circ \delta_i^{(r)}$$

$\gamma_i^{(r)},\delta_i^{(r)}$: affine functions

(1) The diagonal map $\delta^{(r)}=(\delta^{(r)}_1,\delta^{(r)}_2,\cdots,\delta^{(r)}_s)$ depends on both 

$\bullet$ the round key $k^{(r)}$ of $\mathcal{F}^{(r)}_{SLT}$ and 

$\bullet$ the affine encoding $\alpha^{(r)}$ that $\mathcal{G}^{(r)}_{SLT}$ puts on the ***input*** of round $\mathcal{F}^{(r)}_{SLT}$

(2) The function $\gamma^{(r)}=(\gamma^{(r)}_1,\gamma^{(r)}_2,\cdots,\gamma^{(r)}_s)$ depends on 

$\bullet$ encoding $\alpha^{(r+1)}$ that $\mathcal{G}^{(r)}_{SLT}$ puts on the ***output*** of round $\mathcal{F}^{(r)}_{SLT}$

$$\Downarrow$$

<center>

compare the functions $\gamma^{(r-1)}$ and $\delta^{(r)}$

$$\Downarrow$$

recover the key $k^{(r)}$ contained in $\delta^{(r)}$

</center>

## more precise

Fix a round $r$

$$\mathcal{G}_{SLT}=\epsilon \circ Q$$

<font color=blue>

$$\mathcal{F}_{SLT}^{(r)}=M^{(r)}\circ S^{(r)}\circ \oplus_{k^{(r)}}$$

$$\mathcal{G}_{SLT}^{(r)}=\alpha^{(r+1)}\circ \mathcal{F}_{SLT}\circ (\alpha^{(r)})^{-1}$$

</font>
<font color=darkorange>

$$\mathcal{G}_{SLT}=\alpha ^{(r+1)}\circ M\circ S\circ \oplus_k \circ (\alpha^{(r)})^{-1}$$

$\epsilon, \alpha ^{(r+1)},M,\oplus_k,\alpha ^{(r)}$: are all affine

$$Q=\gamma\circ S\circ \delta \qquad (5)$$

$\bullet$ $\gamma=\epsilon^{-1}\circ \alpha^{(r+1)}\circ M \qquad (6)$ : diagonal map

$\bullet$ $\delta= \oplus_k\circ (\alpha^{(r)})^{-1} \qquad (7)$ : diagonal map

</font>

$\delta,Q,S$: are all diagonal maps

<font color=red>

use an algorithm that Biryukov et al.[<sup>3</sup>](#refer-anchor-3) presented to determine the set $\Gamma$ contains the pait $(\gamma,\delta)$ that satisfy `(5)` $Q=\gamma\circ S\circ \delta$

</font>

this set $\Gamma$ contains the pair $(\gamma,\delta)$ that satisfies all of `(5)`, `(6)`, `(7)`

$$\downarrow$$

solve the following two problems:

—— Which pairs $(\gamma^{(r-1)},\delta^{(r-1)})\in \Gamma^{(r-1)}$ and $(\gamma^{(r)},\delta^{(r)})\in \Gamma^{(r)}$ of affine functions satisfy `(6)` and `(7)`?

—— If we have the pairs $(\gamma^{(r-1)},\delta^{(r-1)})\in \Gamma^{(r-1)}$ and $(\gamma^{(r)},\delta^{(r)})\in \Gamma^{(r)}$ that satisfy `(6)` and `(7)`, how can we derive round key $k^{(r)}$ from this?

## Basic algorithm for finding the round key of a round $r$

Known: $Q, E,S,M$.

**Step 1**: For a particular pair $(r-1,r)$ of successive rounds, construct for each S-box $S_i$ the set

$$\Gamma_i=\left\{(\gamma_i,\delta_i)\ | \ Q_i = \gamma_i \circ S_i\circ \delta_i \land \gamma_i,\delta_i \text{ affine}\right\}$$

and let $\Gamma$ be such that $(\gamma,\delta)\in \Gamma$ if $(\gamma_i,\delta_i)\in \Gamma_i$ for all $i$.

**Step 2**: <font color=red>Construct the subset $\Lambda^{(r)} \subseteq \Gamma^{(r-1)}\times \Gamma^{(r)}$ </font> of pairs of affine functions $\gamma^{(r-1)},\delta^{(r-1)}\in \Gamma^{(r-1)}$ and $\gamma^{(r)},\delta^{(r)}\in \Gamma^{(r)}$ such that w

<font color=blue>

$$\gamma^{(r)}=(\epsilon^{(r)})^{-1}\circ \alpha^{(r+1)}\circ M^{(r)} \Rightarrow \epsilon^{(r-1)}\circ \gamma^{(r-1)}=\alpha^{(r)}\circ M^{(r-1)}$$

$$\delta^{(r)}= \oplus_{k^{(r)}}\circ (\alpha^{(r)})^{-1}\Rightarrow  \alpha^{(r)}=(\delta^{(r)})^{(-1)}\circ \oplus_{k^{(r)}} $$

$$ \gamma^{(t)}=\oplus_{c^{(t)}} \circ C^{(t)}=C^{(t)}\oplus c^{(t)}$$

$$ \delta^{(t)}=\oplus_{d^{(t)}} \circ D^{(t)}$$

$$\epsilon^{(t)} = \oplus_{e^{(t)}}\circ E^{(t)}$$

$$\Downarrow$$

$$\oplus_{e^{(r-1)}}\circ E^{(r-1)}\circ \oplus_{c^{(r-1)}} \circ C^{(r-1)}=(\delta^{(r)})^{(-1)}\circ \oplus_{k^{(r)}} \circ M^{(r-1)}$$

$$\delta^{(r)} \circ \oplus_{e^{(r-1)}}\circ E^{(r-1)}\circ \oplus_{c^{(r-1)}} \circ C^{(r-1)}=\oplus_{k^{(r)}}\circ M^{(r-1)} $$

$$\oplus_{d^{(r)}} \circ D^{(r)}\circ \oplus_{e^{(r-1)}}\circ E^{(r-1)}\circ \oplus_{c^{(r-1)}} \circ C^{(r-1)}=\oplus_{k^{(r)}}\circ M^{(r-1)} $$

$$\Downarrow$$

$$(\oplus_{d^{(r)}} \circ  D^{(r)})\circ (\oplus_{e^{(r-1)}}\circ E^{(r-1)})\circ (\oplus_{c^{(r-1)}} \circ C^{(r-1)})=\oplus_{k^{(r)}}\circ M^{(r-1)}$$

$$( d^{(r)}\oplus D^{(r)})\circ (  e^{(r-1)}\oplus E^{(r-1)})\circ ( c^{(r-1)} \oplus C^{(r-1)})=k^{(r)}\oplus M^{(r-1)}$$

$$( d^{(r)}\oplus D^{(r)})\circ (e^{(r-1)} \oplus E^{(r-1)}(C^{(r-1)}\oplus  c^{(r-1)})  )=k^{(r)}\oplus M^{(r-1)}$$

$$( d^{(r)}\oplus D^{(r)})\circ (e^{(r-1)} \oplus E^{(r-1)}C^{(r-1)}\oplus  E^{(r-1)}c^{(r-1)})=k^{(r)}\oplus M^{(r-1)}$$

$$ d^{(r)}\oplus D^{(r)}(e^{(r-1)} \oplus E^{(r-1)}C^{(r-1)}\oplus  E^{(r-1)}c^{(r-1)})=k^{(r)}\oplus M^{(r-1)}$$

$$ d^{(r)}\oplus D^{(r)}e^{(r-1)} \oplus D^{(r)}E^{(r-1)}C^{(r-1)}\oplus  D^{(r)}E^{(r-1)}c^{(r-1)}=k^{(r)}\oplus M^{(r-1)}$$

$$\Downarrow$$

$$ d^{(r)}\oplus D^{(r)}e^{(r-1)} \oplus D^{(r)}E^{(r-1)}C^{(r-1)}y\oplus  D^{(r)}E^{(r-1)}c^{(r-1)}=k^{(r)}\oplus M^{(r-1)}y$$

$$\Downarrow$$

$$D^{(r)}E^{(r-1)}C^{(r-1)}=M^{(r-1)}$$

$$k^{(r)}=d^{(r)}\oplus D^{(r)}e^{(r-1)} \oplus D^{(r)}E^{(r-1)}c^{(r-1)} $$

</font>

<font color=red>

$$D^{(r)}E^{(r-1)}C^{(r-1)}=M^{(r-1)}, \qquad (8)$$

</font>

where matrices $C^{(r-1)}$ and $D^{(r)}$ define the **linear part** of $\gamma^{(r-1)}$ and $\delta^{(r)}$, respectively.

**Step 3**: The round key $k^{(r)}$ of round $r$ is contained in the set 

<font color=red>

$$K^{(r)}=\left\{D^{(r)}E^{(r-1)}c^{(r-1)}\oplus D^{(r)}e^{(r-1)}\oplus d^{(r)}\ | \ (\gamma^{(r-1)},\delta^{(r-1)},\gamma^{(r)},\delta^{(r)})\in \Lambda^{(r)}\right\}$$

</font>

### Solving the Linear Equivalence Problem for Matrices

#### Definition 5 

Let $X=X_1\times X_2\times \cdots \times X_s$, where $x_i$ consists of $m\times m$ matrices.
Then we denote by $\mathcal{D}(X)$: the collection of ***all block diagonal matrices*** with $i$th diagonal block contained in $X_i$, for all $i$

#### Definition 6 (Linear Equivalence Problem of Matrices (LEPM))
 
A problem instance is defined by $(M,E,X,Y)$ for invertible $n\times n$ matrices $M$ and $E$ and sets $X=X_1\times X_2\times \cdots \times X_s$ and $Y=Y_1\times Y_2\times \cdots \times Y_s$, where $X_i$ and $Y_i$ contain invertible $m\times m$ matrices and $n=m\cdot s$.

<font color=red>

Find all pairs of block-diagonal  $n\times n$ matrices $(C,D)\in \mathcal{D}(X)\times\mathcal{D}(Y)$ such that $M=D\cdot E\cdot C$

</font>

#### algotithm LEPM solver$(X,Y)$

---
begin 
$\qquad$   repeat
$\qquad \qquad$for all $X_j$ do 
$\qquad \qquad \qquad$for all $C_j\in X_j$ do
$\qquad \qquad \qquad \qquad$ if $\lnot\exists_{D_i\in Y_i}M_{i,j}=D_i \cdot E_{i,j}\cdot C_j $ then
$\qquad \qquad \qquad \qquad \qquad$$X_j\coloneqq X_j \setminus \left\{C_j\right\}$;
$\qquad \qquad$        for all $Y_i$ do 
$\qquad \qquad \qquad$            for all $D_i\in Y_i$ do
$\qquad \qquad \qquad \qquad$                if $\lnot\exists_{C_j\in X_j}M_{i,j}=D_i \cdot E_{i,j}\cdot C_j $ then
$\qquad \qquad \qquad \qquad \qquad$                    $Y_i\coloneqq Y_i \setminus \left\{D_i\right\}$;
$\qquad$    until $X$ and $Y$ do not change;
$\qquad$    if a set $X_j$ or $Y_i$ is empty then
$\qquad \qquad$        return $\emptyset$
$\qquad$   else if $\forall_j|X_j|=1 \land \forall_i|Y_i|=1$ then
$\qquad \qquad$        return $\left\{(C,D)\right\}$ With $C_j\in X_j$ and $D_i\in Y_i$;
$\qquad$    else $/^{\star} \text{case } \exists_j|X_j|>1 ^\star/$
$\qquad \qquad$        select smallest $j$ with $|X_j|>1$;
$\qquad \qquad$        return $\bigcup_{C_j\in X_j} \text{LEMP solver}(X(X_j=\left\{C_j\right\}),Y)$;
end;

---
$\forall_j|X_j|=1 \land \forall_i|Y_i|=1$: all sets $X_j$ and $Y_i$ ***contain exactly one linear mapping***, then the only candidate solution to the `LEPM` problem instance is the solution defined by these linear mappings; moreover, since no $X_j$ or $Y_i$ was further reduced, ***this solution must indeed be valid***. 
So in this case, the LEMP problem is solved.

$/^{\star} \text{case } \exists_j|X_j|>1 ^\star/$: a set $X_j$ or a set $Y_i$ exists that ***contains more than one linear mapping***. 

If all $X_j$ have size one, then $C$ is uniquely determined, and hence $D=E^{-1}C^{-1}M$ is also uniquely determined. As a consequence, all sets $Y_i$ must also have size one. 

So we may assume without loss of generality that some set $X_j$ has size bigger than one. 

In that case, for each matrix $C_j$ in $X_j$, we rerun the algorithm with $X_j$ replaced by the set $X'_j=\left\{C_j\right\}$. Obviously, in this way all solutions are found.

$$\downarrow$$

know whether the algorithm presented is effective for attacking a white-box implementation

$$\Downarrow$$

know an upper bound on 
$ \bullet$ **the number of solutions returned**: related the cardinality of the set $K$ of candidate round keys in the algorithm of `Basic algorithm for finding the round key of a round r`

$\bullet$ **the number of recursive invocations**: determines the time complexity of the algorithm of `LEMP solver(X,Y)`

$$\Downarrow$$

we want to answer the question for ***any*** white-box implementation of that block cipher

$$\Downarrow$$

<font color =red> derive upper bounds</font>
 on these numbers that only depend on the block cipher specification and not

$$\Downarrow$$

#### Theorem 3

For a round $r$ of an `SLT cipher`, let $I=(M,E,X,Y)$ be the problem instance of `LEMP` that is associated with the cryptanalysis of its white-box implementation. Furthermore, let $I'=(M,M,X',Y')$ be the problem instance in which $X'_i$ and $Y'_i$ are given by 

$$X'_i=\left\{L \ | \ S_i^{(r-1)}=\lambda \circ  S_i^{(r-1)}\circ \phi \text{ with } \lambda: x\mapsto l\oplus Lx \text{ and } \phi \text{ affine}\right\}$$

and

$$Y'_i=\left\{P \ | \ S_i^{(r)}=\lambda \circ  S_i^{(r)}\circ \phi \text{ with } \lambda \text{ and } \phi: x\mapsto p\oplus Px \text{ affine}\right\}$$

Then, applying the algorithm of `LEMP solver(X,Y)` to $I$ results in the same number of recursive invocations and the same number of solutions as when applying the algorithm to problem instance $I'$

# Reference

<div id="refer-anchor-1"></div>

[1] Michiels W, Gorissen P, Hollmann H D L. Cryptanalysis of a generic class of white-box implementations[C]. International Workshop on Selected Areas in Cryptography. Springer, Berlin, Heidelberg, 2008: 414-428.

<div id="refer-anchor-2"></div>

[2] Billet O, Gilbert H, Ech-Chatbi C. Cryptanalysis of a white box AES implementation[C]. International Workshop on Selected Areas in Cryptography. Springer, Berlin, Heidelberg, 2004: 227-240.

<div id="refer-anchor-3"></div>

[3] Biryukov A, De Canniere C, Braeken A, et al. A toolbox for cryptanalysis: Linear and affine equivalence algorithms[C]. International Conference on the Theory and Applications of Cryptographic Techniques. Springer, Berlin, Heidelberg, 2003: 33-50.