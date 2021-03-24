<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
<script type="text/x-mathjax-config">
    MathJax.Hub.Config({ tex2jax: {inlineMath: [['$', '$']]}, messageStyle: "none" });
</script>
<!-- TOC -->

- [Introduction of BGE Attack](#introduction-of-bge-attack)
- [Definition](#definition)
    - [an encoded AES subround](#an-encoded-aes-subround)
    - [an encoded AES subround with byte permutations](#an-encoded-aes-subround-with-byte-permutations)
- [Original BGE attack](#original-bge-attack)
    - [Overview the Original BGE attack](#overview-the-original-bge-attack)
        - [Phase 1 Recovering Non-linear Parts](#phase-1-recovering-non-linear-parts)
        - [Phase 2](#phase-2)
        - [Phase 3](#phase-3)
    - [Description of the Cryptanalysis of WB-AES using `BGE Attack`](#description-of-the-cryptanalysis-of-wb-aes-using-bge-attack)
        - [Theorem 1](#theorem-1)
        - [an efficient algorithm——choose a tuple $(f_1,\dots,f_8)$, compute the corresponding mapping $\psi$ $\rightarrow$  time complexity is at most $2^{24}$](#an-efficient-algorithmchoose-a-tuple-f_1\dotsf_8-compute-the-corresponding-mapping-\psi-\rightarrow--time-complexity-is-at-most-2^24)
        - [Tolhuizen's algorithm to improve the `Phase 1` of `Original BGE Attack`](#tolhuizens-algorithm-to-improve-the-phase-1-of-original-bge-attack)
            - [Rephrase `Theorem 1`](#rephrase-theorem-1)
            - [Tolhuizen algorithm](#tolhuizen-algorithm)
                - [Pre-processing: labeling functions](#pre-processing-labeling-functions)
                    - [Tolhuizen Lemma 1](#tolhuizen-lemma-1)
                    - [Tolhuizen Lemma 2](#tolhuizen-lemma-2)
                - [Description of the Tolhuizen algorithm](#description-of-the-tolhuizen-algorithm)
                - [Complexity estimate](#complexity-estimate)
    - [Key Extraction](#key-extraction)
- [Reducing the Work Factor of the BGE Attack](#reducing-the-work-factor-of-the-bge-attack)
    - [Phases 1 and 2: Retrieve the Round Key Bytes $\overline{k}_i^{(r,j)}(0\leq i,j \leq 3)$ Associated with Round $r(2\leq r\leq 8)$](#phases-1-and-2-retrieve-the-round-key-bytes-\overlinek_i^rj0\leq-ij-\leq-3-associated-with-round-r2\leq-r\leq-8)
        - [Work factor of Phase 1](#work-factor-of-phase-1)
        - [Work factor of Phase 2](#work-factor-of-phase-2)
        - [Lemma 1](#lemma-1)
    - [Phase 3: Retrieve the Round Key Bytes $\overline{k}_i^{(r+1,j)}(0\leq i,j\leq 3)$ Assiciated with Round $r+1$](#phase-3-retrieve-the-round-key-bytes-\overlinek_i^r1j0\leq-ij\leq-3-assiciated-with-round-r1)
        - [Step 1](#step-1)
        - [Step 2](#step-2)
        - [Step 3](#step-3)
        - [Work factor of Phase 3](#work-factor-of-phase-3)
    - [Phase 4: Determine the Correct Order of the Round Key Bytes and Extract the Secret AES Key](#phase-4-determine-the-correct-order-of-the-round-key-bytes-and-extract-the-secret-aes-key)
        - [Step 1](#step-1-1)
        - [Step 2](#step-2-1)
        - [Step 3](#step-3-1)
        - [Work factor of Phase 4](#work-factor-of-phase-4)
- [Reference](#reference)

<!-- /TOC -->

# Introduction of BGE Attack

In 2002, Chow, Eisen, Johnson and van Oorschot[<sup>1</sup>](#refer-anchor-1) proposed a white-box implementation of AES.

In 2004, **Billet, Gilbert and Ech-Chatbi**[<sup>2</sup>](#refer-anchor-2) presented an efficient attack referred to as the `BGE` attack on this implementation.

$2^{30}$

In 2012, Tolhuizen[<sup>3</sup>](#refer-anchor-3) presented an improvement of **the most time-consuming phase** of the `BGE` attack.

$2^{19}\quad  \rightarrow \quad 2^{29}$

In 2013, De Mulder et al.[<sup>4</sup>](#refer-anchor-4) presented several improvements to ***the other phase*** of the `BGE` attack.

$2^{22}$

# Definition 

$P_i^{(r,j)}$: bijectice mappings on the vector space $\mathbb{F}_2^8$, `input encoding` in white-box cryptography

$Q_i^{(r,j)}$: bijectice mappings on the vector space $\mathbb{F}_2^8$, `output encoding` in white-box cryptography

The `encodings` are generated randomly and are kept secret in a white-box implementation

## an encoded AES subround

$$AES_{enc}^{(r,j)}=(Q_0^{(r,j)},Q_1^{(r,j)},Q_2^{(r,j)},Q_3^{(r,j)})\circ AES^{(r,j)}\circ (P_0^{(r,j)},P_1^{(r,j)},P_2^{(r,j)},P_3^{(r,j)})$$

## an encoded AES subround with byte permutations

Permutations $\Pi_i^{(r,j)}: (\mathbb{F}_2^8)^4 \rightarrow (\mathbb{F}_2^8)^4(i=1,2,\quad 1\leq r\leq 9, \quad 0\leq j \leq 3)$

<font  color=red>

$$\overline{AES}_{enc}^{(r,j)}=(Q_0^{(r,j)},Q_1^{(r,j)},Q_2^{(r,j)},Q_3^{(r,j)})\circ \overline{AES}^{(r,j)}\circ (P_0^{(r,j)},P_1^{(r,j)},P_2^{(r,j)},P_3^{(r,j)})$$

</font>

$$\overline{AES}^{(r,j)}=\Pi_2^{(r,j)}\circ AES^{(r,\pi^{(r)}(j))}\circ \Pi_1^{(r,j)}=MC^{(r,j)}\circ (S,S,S,S)\circ \oplus_{[\overline{k}_i^{(r,j)}]_{0\leq i \leq  3}}$$

$$[\overline{k}_i^{(r,j)}]_{0\leq i \leq  3}=(\Pi_1^{(r,j)})^{-1}([k_i^{(r,\pi^{(r)}(j)}]_{0\leq i \leq  3})$$

$$MC^{(r,j)}=\Pi_2^{(r,j)}\circ MC\circ \Pi_1^{(r,j)}$$

# Original BGE attack

## Overview the Original BGE attack

### Phase 1 Recovering Non-linear Parts

retrieves the encodings $Q_i^{(r,j)}(0\leq i \leq 3)$ up to an affine part for each encoded AES subround $j(0\leq j \leq 3)$

retrieves the encodings $P_i^{(r,j)}(0\leq i,j \leq 3)$ up to an affine part by applying the same technique to the encoded AES subrounds of the **previous round**

### Phase 2

first retrieves the affine encodings $Q_i^{(r,j)}(0\leq i \leq 3)$ for each encoded AES subround $j(0\leq j \leq 3)$

at the same time, the key-dependent affine mappings $\widetilde{P}_i^{(r,j)}(x)=P_i^{(r,j)}(x)\oplus \overline{k}_i^{(r,j)}(0\leq i,j \leq 3)$ are obtained as well

adversary to compute the round key bytes $\overline{k}_i^{(r,j)}=\widetilde{P}_i^{(r,j)}(0)\oplus P_i^{(r,j)}(0)$ for $(0\leq i,j \leq 3)$

### Phase 3

determine the `correct order` of the round key bytes and extracts the AES key

the adversary can use the property of the `AES key scheduling algorithm` that the AES key can be computed if one of the round keys is known

## Description of the Cryptanalysis of WB-AES using `BGE Attack`

$\bullet$ ***the mixing bijections and conceatnated encodings are combined***
$\bullet$ $P_i$: ***the combination of input encodings and mixing bijections***
$\bullet$ $Q_i$: ***the combination of mixing bijections and output encodings***
$\bullet$ ***the inverses of the output encodings become input encodings in the subsequent round***
$\bullet$ $x_0,x_1,x_2,x_3$: four input bytes
$\bullet$ $y_0,y_1,y_2,y_3$: the resulting output

$$MC=\begin{bmatrix}
02  & 01 &01 &03 \\
03  & 02 &01 &01 \\
01  & 03 &02 &01 \\
01  & 01 &03 &02 
\end{bmatrix}$$

$$T'_i=T_i\circ P_i$$

$$\Downarrow$$

$$y_0=Q_0(02 \cdot T'_0(x_0)\oplus 03 \cdot T'_1(x_1)\oplus 01 \cdot T'_2(x_2)\oplus 01 \cdot T'_3(x_3))$$

$$y_1=Q_1(01 \cdot T'_0(x_0)\oplus 02 \cdot T'_1(x_1)\oplus 03 \cdot T'_2(x_2)\oplus 01 \cdot T'_3(x_3))$$

$$y_2=Q_2(01 \cdot T'_0(x_0)\oplus 01 \cdot T'_1(x_1)\oplus 02 \cdot T'_2(x_2)\oplus 03 \cdot T'_3(x_3))$$

$$y_3=Q_3(03 \cdot T'_0(x_0)\oplus 01 \cdot T'_1(x_1)\oplus 01 \cdot T'_2(x_2)\oplus 02 \cdot T'_3(x_3))$$

$\bullet$ ***build an approximation:*** $\widetilde{Q}$
$A_i(x)=A(x)\oplus b$

$$\widetilde{Q}_i=Q_i\circ A_i$$

<font color=blue>

$$\begin{aligned}
y_0=f(x_0,x_1,00,00)
& =Q_0(02 \cdot T'_0(x_0)\oplus 03 \cdot T'_1(x_1)\oplus 01 \cdot T'_2(00)\oplus 01 \cdot T'_3(00))\\
& =Q_0(02 \cdot T'_0(x_0)\oplus 03 \cdot T'_1(x_1))\\
\end{aligned}$$

$$y_0=f_{00}(x_0)=Q_0(02 \cdot T'_0(x_0)\oplus \beta_{00})$$

$$y_0=f_{01}(x_0)=Q_0(02 \cdot T'_0(x_0)\oplus \beta_{01})$$

$$\downarrow$$

$$f_{00}=Q_0 \circ \oplus_{\beta_{00}}\circ 02\circ T'_0$$

$$f_{01}=Q_0 \circ \oplus_{\beta_{01}}\circ 02\circ T'_0$$

$$\downarrow$$

$$\begin{aligned}
f_{01}\circ f_{00}^{-1}
&=(Q_0\circ \oplus_{\beta_{01}}\circ 02 \circ T'_0)\circ ((02\cdot T'_0)^{-1}\circ \oplus_{\beta_{00}}\circ Q_0^{-1})\\
&=Q_0\circ \oplus_{\beta} \circ  Q_0^{-1}
\end{aligned}$$

$$\beta=\beta_{01}\oplus \beta_{00}$$

</font>

$$f_{01}\circ f_{00}^{-1}=Q_0\circ \oplus_{\beta} \circ  Q_0^{-1}$$

$$\beta=\beta_{01}\oplus \beta_{00}$$

There are $256=2^8 (00\rightarrow \text{ff})$ bijections of the form $Q_0\circ \oplus_{\delta}\circ Q_0^{-1}$ where $\delta$ is an 8-bit string.

compute $f_{00}\circ f_{00}^{-1}, f_{01}\circ f_{00}^{-1},\cdots, f_{\text{ff}}\circ f_{00}^{-1}$ to form the group $(G,\circ)$ (the group elements: look-up tables)

$\bullet$ ***build an `isomorphism`***
$$\psi:(G,\circ)\rightarrow (GF(2)^8,\oplus)$$

$g_1,g_2,\cdots , g_8$: 8 group elements, a subset of them can be composed to generate any group element

use this `isomorphism` to ***construct the approximation to*** $Q_0$: for any $g\in G$, set

$$ \widetilde{Q}_0(\psi(g))=g(00)$$

----

### Theorem 1

Given a set of functions 

$$\mathcal{S}=\left\{Q\circ \oplus_{\beta}\circ Q^{-1} \right\}_{\beta \in GF(2^8)}$$

given by values

$\bullet$ $Q$: a permutation of $GF(2)^8$ 

$\bullet$ $\oplus_{\beta}$: the translation by $\beta$ in $GF(2)^8$

one can construct a particular solution $\widetilde{Q}$ such that there exists an affine mapping $A$ so that 

$$\widetilde{Q}=Q\circ A$$

***Proof.*** 

recover a known `isomorphism` up to an unknown linear bijection

first: select from $\mathcal{S}$ a `tuple` $(f_1,\dots, f_8)$ of $8$ functions such that their `images` through $\varphi$ constitute a base of $GF(2)^8$

gradually select $f_1$ to $f_8$ so that they span the whole set $\mathcal{S}$ through composition, that is

$$\forall f\in \mathcal{S},\quad \exists!(\epsilon_1,\dots, \epsilon_8)\in \left\{0,1\right\}^8, \quad f=f_8^{\epsilon_8}\circ f_7^{\epsilon_7} \circ \cdots \circ f_1^{\epsilon_1}$$

$f_i^1=f_i$ and $f_i^0$: the identity function

$([\beta_i])_{i=1\dots 8}$: a base of $GF(2)^8$

exists a unique one-to-one linear change of base $L $ mapping $[e_i]$ onto $[\beta_i]$ for all $i=1,\dots, 8$

$\psi\xlongequal{\text{def}}L^{-1}\circ \varphi \qquad (G,\circ)\rightarrow \left(GF(2)^8,\oplus\right)$ 

successively applying $\varphi$ and $L^{-1}$ to $f$, one obtains

$$\psi(f)=L^{-1}(\varphi(f))=L^{-1} \left(\bigoplus_{i=1\dots 8}\epsilon_i[\beta_i]\right)  =\bigoplus_{i=1\dots 8}\epsilon_i[e_i] $$

the `isomorphism` $\psi$ is entirely determined

how to recover $ Q$ from the knowledge of $\psi$, up to an unknown affine transformation $A$

$$A(x)\xlongequal{\text{def}}L\left(x\oplus \left(Q\circ L\right)^{-1}\left([00]\right)\right) = L(x)\oplus Q^{-1}\left([00]\right)$$

$$\widetilde{Q}\xlongequal{\text{def}}Q\circ A$$

$$\widetilde{ Q}^{-1}\left(00\right)=\left[00\right]$$

$$f=\widetilde{Q}\circ \oplus_{\psi(f)}\circ \widetilde{Q}^{-1}$$

<font color=red>

$$f(00)=\widetilde{Q}\left(\psi(f)\right)$$

knowledge of $\psi$ and $f$ $\rightarrow$ compute $\widetilde{Q}=Q\circ A$

</font>

### an efficient algorithm——choose a tuple $(f_1,\dots,f_8)$, compute the corresponding mapping $\psi$ $\rightarrow$  time complexity is at most $2^{24}$

$\qquad \quad \text{INPUT: } \mathcal{S}$

$\qquad \text{OUTPUT: } \mathcal{R}\subset \mathcal{S} \times GF(2)^8 \text{ such that } \forall (f,\beta)\in \mathcal{R}, \psi(f)=[\beta]$

$\text{ALGORITHM: } \mathcal{R}\leftarrow \left\{(id,00)\right\}$

$\qquad \qquad \qquad \quad$ $\psi(id)=[00]$

$\qquad \qquad \qquad \quad$ $e\leftarrow 01$

$\qquad \qquad \qquad \quad$ $\textbf{while  } \#\mathcal{R}<2^8 \textbf{ do}$

$\qquad \qquad \qquad \qquad \quad $ $\mathcal{S}\leftarrow \mathcal{S} \backslash \left\{f\right\}$

$\qquad \qquad \qquad \qquad \quad $ $\textbf{if } (f,\cdot) \notin \mathcal{R} \textbf{ then}$

$\qquad \qquad \qquad \qquad \qquad \quad$ $e\leftarrow 02 \times e$

$\qquad \qquad \qquad \qquad \qquad \quad$ $\psi(f)=[e]$

$\qquad \qquad \qquad \qquad \qquad \quad$ $\textbf{foreach } (g,\eta)\in \mathcal{R} \textbf{ do}$

$\qquad \qquad \qquad \qquad \qquad \qquad \quad $ $\mathcal{R}\leftarrow \mathcal{R}\cup \left\{(f\circ g, [e]\oplus [\eta])\right\}$

$\qquad \qquad \qquad \qquad \qquad \qquad \quad $ $\psi(f\circ g)=[e]\oplus [\eta]$

$\qquad \qquad \qquad \qquad \qquad \quad $ $\textbf{enddo}$

$\qquad \qquad \qquad \qquad \quad $ $\textbf{endif}$

$\qquad \qquad \qquad \quad $ $\textbf{endwhile}$

<font color=red>

`Theorem 1` $\Rightarrow$ recover for any round $r=1,\dots,9$, the ***non-linear part*** $\widetilde{Q}_i^{(r,j)}$ of $Q_i^{(r,j)}$

$$ (\widetilde{Q}_i^{(r,j)})^{-1} \circ Q_i^{(r,j)}=(A_i^{(r,j)})^{-1}$$

the input encoding $P_i^{(r+1,j)}$ must match the output encoding $Q_i^{(r,j)}$ of the previous round——$P_i^{(r+1,j)}\circ Q_i^{(r,j)}$ must be the identity

$$P_i^{(r+1,j)}\circ \widetilde{Q}_i^{(r,j)}=A_i^{(r,j)}$$

<font color=blue>

$$ (\widetilde{Q}_i^{(r,j)})^{-1} \circ Q_i^{(r,j)}=(A_i^{(r,j)})^{-1}$$

$$\Downarrow$$

$$(Q_i^{(r,j)})^{-1}\circ \widetilde{Q}_i^{(r,j)}=A_i^{(r,j)}$$

$$P_i^{(r+1,j)}=(Q_i^{(r,j)})^{-1}$$

$$\Downarrow$$

$$P_i^{(r+1,j)}\circ \widetilde{Q}_i^{(r,j)}=A_i^{(r,j)}$$

</font>

$$\downarrow$$

Next Step: recover those affine mappings

</font>

### Tolhuizen's algorithm to improve the `Phase 1` of `Original BGE Attack` 

#### Rephrase `Theorem 1`

given $2^8$ tables $T_0,\dots, T_{255}$ (corresponding to the function in $\mathcal{S}$)

each such table has $2^8$ entries, each entry being an 8-bits string 

an known permutation $Q$ of $\left\{0,1\right\}^8$

an known bijection $\beta:\left\{0,\dots, 255\right\}\mapsto \left\{0,1\right\}^8$

for each $i,j\in \left\{0,1,\dots,255\right\}$, $T_i(j)=Q\left(\beta(i)\oplus Q^{-1}\left(bin \left(j\right)\right)\right)$

$bin(j)\in \left\{0,1\right\}^8$: binary representation of $j$

#### Tolhuizen algorithm

select from $\mathcal{S}$ a collection of $\mathcal{s}$ that span $S$ through function composition

for each $f\in \mathcal{S}$, there is a unique $\mathcal{s}$-tuple $\Psi(f)=(\epsilon_1,\epsilon_2,\dots, \epsilon_s)\in \left\{0,1\right\}^{\mathcal{s}}$ such that

$$f=f_1^{\epsilon_1}\circ f_2^{\epsilon_2}\circ \cdots\circ f_s^{\epsilon_{\mathcal{s}}}, \text{ where } f_i^1=f_i \text{ and } f_i^0 =id(\text{identity function})$$

$$\downarrow$$

<center>

find the functions $f_1,\dots,f_{\mathcal{s}}$ 

$$f(\mathbf{0})=\widetilde{Q}(\Psi(f))$$

$$\left\{f(\mathbf{0}) \ | \ f\in \mathcal{S}\right\}=\left\{0,1\right\}^{\mathcal{s}}$$

</center>

##### Pre-processing: labeling functions

<font color=red>

the function in $\mathcal{S}$ are labeled in a particular order $\rightarrow$ algorithm becomes much faster

</font>

----
###### Tolhuizen Lemma 1

$\bullet$ $Q$: a permutation of $\left\{0,1\right\}^{\mathcal{s}}$ 

$\bullet$ $x_0, \alpha, \beta \in \left\{0,1\right\}^{\mathcal{s}}$

$$Q(\alpha \oplus Q^{-1}(x_0))=Q(\beta\oplus Q^{-1}(x_0)) \quad \Rightarrow \quad  \alpha =\beta$$

----

denote the functions in $\mathcal{S}$ as $g_0,g_1,\dots,g_{2^{\mathcal{s}}-1}$, where the `ordering` of the functions is such that 

$$g_i(\mathbf{0})=bin(i)$$

`Tolhuizen Lemma 1` guarantees that such an ordering exists

----

###### Tolhuizen Lemma 2

$0\leq i,j\leq 2^{\mathcal{s}-1}$

$$g_i \circ g_j =g_k\text{ for the integer }k \text{ with } bin(k)=g_i(bin(j))$$

----

##### Description of the Tolhuizen algorithm

<font color=red>

labeling functions $\Rightarrow$ apply the following algorithm

</font>

<center>

$$\Psi(g_i)=\Phi[i] \text{ for } 0\leq i\leq 2^{\mathcal{s}-1}$$

$$\downarrow$$

obtain the function $\Psi$

combine with $f(\mathbf{0})=\widetilde{Q}(\Psi(f))$

$$\downarrow$$

find for $0\leq i,j\leq 2^{\mathcal{s}-1}$, $\widetilde{Q}(\Phi[i])=\widetilde{Q}(\Psi(g_i))=g_i(\mathbf{0})=bin(i)$

$$\downarrow$$

use the array $\Phi$ $\Rightarrow$ obtain $\widetilde{Q}$
</center>

----

$\textbf{Algorithm}$

$\text{inR}[0]\coloneqq \text{true};\ \Phi[0]\coloneqq 0;$

$\textbf{for } i=1 \textbf{ to }2^{\mathcal{s}}-1 \textbf{ do begin }\text{inR}[i]\coloneqq \text{false};\ \Phi[i]\coloneqq 0 \textbf{ end };$

$j\coloneqq 0;$

<font color=blue>

$/^{**} \text{The set of functions }g_i \text{ for which inR}[i]=\text{true consists of }2^j \text{functions and is closed under function composition. For each }i,k\text{ with inR}[i]=\text{inR}[k]=\text{true}, \Phi[i] \text{ is an } \mathcal{s}\text{-bits vector, and } \Psi(g_i \circ g_k)=\Psi(g_m)=\Phi[m]=\Phi[i]\oplus\Phi[k]=\Psi(g_i)\oplus\Psi(g_k),\text{ where } m=g_i(bin(k))^{**}/$

</font>

$\textbf{while }j\neq s \textbf{ do}$

$\textbf{begin }i\coloneqq 1; \textbf{ while } \text{inR}[i] \textbf{ do } i\coloneqq i+1;$

<font color=blue>

$/^{**} \text{so }i \text{ the smallest indexwith inR}[i]=\text{false}^{**}/$

</font>

$ \qquad \text{inR}[i]\coloneqq\text{true}; \  j\coloneqq j+1; \  \Phi[i]=\mathbf{e}_j;$

<font color=blue>

$\qquad  /^{**} \mathbf{e}_j \text{ is the }j \text{-th unit vector}^{**}/$

</font>


$ \qquad \textbf{for }k\coloneqq 1 \textbf{ to } 2^{\mathcal{s}}-1 \textbf{ do}$

$ \qquad \textbf{if }\text{inR}[k] \textbf{ then begin }m\coloneqq g_i[k]; \  \text{inR}[m]\coloneqq \text{true}; \  \Phi[m]\coloneqq \Phi[k]\oplus \Phi[i] \textbf{ end};$

$\textbf{end.}$

----
$R_j$: the set of functions for which the $\Phi$ (or $\Psi$) value has been determined after iteration $j$

The set $R_j$ consists of all functions of the form $f_1^{\epsilon_1}\circ \cdots \circ f_j^{\epsilon_j}$

In step $j$, we extend the set $R_{j-1}$ to the set $R_j$

$$R_j=R_{j-1}\cup \left\{f\circ f_j\ | \ f \in R_{j-1}\right\}$$

$f_j$: the function $g_i$ where $i$ is ***the smallest index*** for which $\text{inR}[i]=\text{flase}$

##### Complexity estimate

1. the pre-processing (labeling functions): $2^{\mathcal{s}}$ steps $\Rightarrow$ determine $f(\mathbf{0})$ for each $f\in \mathcal{S}$
2. the initialisation of $\text{inR}$ and $\Phi$: $2^{\mathcal{s}+1}$ steps
3. for each of the $\mathcal{s}$ value of "$j$": find $i$ : $2^{\mathcal{s}}-1$ steps
   (In fact, at most $2^{j-1}$ steps in step $j$, as $R_{j-1}$ has $2^{j-1}$ elements, i.e., $\text{inR}[i]=\text{true}$ for $2^{j-1}$ values of $i$)
4. set $\text{inR}[i]$: $1$ setp
5. set $\Psi[i]$: $1$ setp
6. for each of the $k$: $2^{\mathcal{s}}-1$ values, $3$ operations 
   (or, to be more precise: one comparison for all value of $k$, and $3$ operations for at most $2^j$ elements $k$)
 $\Rightarrow$ $3 \cdot (2^{\mathcal{s}}-1)$ steps

$$\Downarrow$$

<font color=red>

time complexity is at most $3\cdot 2^{\mathcal{s}}+\mathcal{s}\cdot 4\cdot 2^{\mathcal{s}}=(3+4 \mathcal{s})2^{\mathcal{s}}$

complexity reduction as compared to `original BGE` is due to two reason:
1. pre-processing step: make it easy to find, for every two functions $f,g$ in $\mathcal{S}$ the label of the function $h$ in $\mathcal{S}$ such that $f\circ g=h$ 
2. we iterate over $j$ instead of over the set $\mathcal{S}$ as done in `original BGE`.

</font>

----

$\widetilde{Q}_0$ is constructed as a look-up table

an analogous process builds approcimation for $Q_1,Q_2,Q_3$

$$ (\widetilde{Q}_i)^{-1}\circ Q_i\xlongequal{\widetilde{Q}=Q\circ A}(A_i)^{-1}\circ (Q_i)^{-1} \circ Q_i=(A_i)^{-1}$$

$\bullet$ ***the inverse of an affine bijection is an affine bijection***

----
$\textbf{Proposition 1.(original BGE)}$

For any pair $(y_i,y_j)$ as introduced above, there exists a unique linear mapping $L$ and a unique constant $c$ such that

$$ \forall x_0\in GF(2^8), y_i(x_0,00,00,00)=L(y_j(x_0,00,00,00))\oplus c$$

***Proof.***
<font color=blue>

$$y_j(x_0,x_1,x_2,x_3)=Q_j(\alpha_{j,0} \cdot T'_0(x_0)\oplus c_j)
\xlongequal{Q_j=M_j(x)\oplus q_j} M_j(\alpha_{j,0} \cdot T'_0(x_0)\oplus c_j)\oplus q_j$$

$$\begin{aligned}
y_i(x_0,x_1,x_2,x_3) &=Q_i(\alpha_{i,0} \cdot T'_0(x_0)\oplus \alpha_{i,1} \cdot T'_1(00)\oplus \alpha_{i,2} \cdot T'_2(00)\oplus \alpha_{i,3} \cdot T'_3(00))\\
& =Q_i(\alpha_{i,0} \cdot T'_0(x_0)\oplus c_i)\xlongequal{Q_i=M_i(x)\oplus q_i} M_i(\alpha_{i,0} \cdot T'_0(x_0)\oplus c_i)\oplus q_i=M_i \circ  \Lambda_{\alpha_{i,0}}\oplus M_i(c_i)\oplus q_i\\
& = L(y_j(x_0,00,00,00))\oplus c\\
& \xlongequal{y_j(x_0,x_1,x_2,x_3) = M_j(\alpha_{j,0} \cdot T'_0(x_0)\oplus c_j)\oplus q_j}L\left(M_j(\alpha_{j,0} \cdot T'_0(x_0)\oplus c_j)\oplus q_j \right)\oplus c\\
& =L\circ M_j\circ \alpha_{j,0}(T'_0(x_0)) \oplus L(M_j(c_j))\oplus  L(q_j)\oplus c\\
& = L\circ M_j\circ \Lambda_{\alpha_{j,0}}\oplus L(M_j(c_j))\oplus  L(q_j)\oplus c\\
& =L\circ M_j\circ \Lambda_{\alpha_{j,0}}\oplus L(q_j \oplus M_j(c_j))\oplus c\\
\end{aligned}$$

$$\Downarrow$$

$$M_i \circ  \Lambda_{\alpha_{i,0}}\oplus M_i(c_i)\oplus q_i=L\circ M_j\circ \Lambda_{\alpha_{j,0}}\oplus L(q_j \oplus M_j(c_j))\oplus c$$

$$\Downarrow$$

$$L=M_i\circ \Lambda_{\alpha_{i,0}/\alpha_{j,0}} \circ M_j^{-1}$$

$$c= L(q_j \oplus M_j(c_j)) \oplus M_i(c_i) \oplus q_i$$

</font>

----

$$y_i(x_0,x_1,x_2,x_3)=Q_i(\alpha_{i,0} \cdot T'_0(x_0)\oplus \alpha_{i,1} \cdot T'_1(x_1)\oplus \alpha_{i,2} \cdot T'_2(x_2)\oplus \alpha_{i,3} \cdot T'_3(x_3))$$

$\alpha_{i,j}$: MC matrix elements

$\Lambda_{\alpha}$: the matrix over $GF(2)^8$ of the multiplication by $\alpha$

$Q_i=M_i(x)\oplus q_i$
$\bullet$ $M_i$: a linear bijection 
$\bullet$ $q_i$: an 8-bit string

$$\Downarrow$$

set $x_1,x_2,x_3$ all to $00$ $\Rightarrow$ $y_0(x_0,x_1,x_2,x_3)=y_0(x_0,00,00,00)$:

$$L_0\xlongequal{i=0,j=1}M_0\circ \Lambda_{\alpha_{0,0}/\alpha_{1,0}} \circ M_1^{-1}$$

set $x_0,x_2,x_3$ all to $00$ $\Rightarrow$ $y_0(x_0,x_1,x_2,x_3)=y_0(00,x_1,00,00)$:

$$L_1\xlongequal{i=0,j=1}M_0\circ \Lambda_{\alpha_{0,1}/\alpha_{1,1}} \circ M_1^{-1}$$

$$\Downarrow$$

<font color=red>

$$\begin{aligned}
L
&=L_0\circ L_1^{-1}\\
&\xlongequal{\beta=\alpha_{0,0}\alpha_{1,1}/\alpha_{0,1}\alpha_{1,0}}M_0\circ \Lambda_{\beta}\circ M_0^{-1}\\
\end{aligned}$$

</font>

$$\Downarrow$$

$\alpha \in \left\{01,02,03\right\}\Rightarrow \beta \text{ only 16 possible values}$

$$\beta\in B=\left\{02,d8,03,6f,04,bc,06,b7,05,25,4a,f8,7f,c8,64,5f\right\}$$

$$\Downarrow$$

<font color=red>

retrieve $\beta$ and $M_0$

$$\Downarrow$$

$\beta$ is chosen from $B$, computing the `characteristic polynomial` of $L$ reduces the number of possibilities for $\beta$ to at most $2$; actually, either $\beta$ is already determined, of $\beta\in \left\{b,b^2\right\}\subset B$.

$$\Downarrow$$

ease the exposition $\Rightarrow$ assume that $\beta$ is known, for instance by testing the two possibilities, and using `Proposition.2` to determine the correct one

</font>

----

$\textbf{Proposition 2.(original BGE)}$

Given an element $\beta$ of $GF(2^8)$ not in any subfields of $GF(2^8)$ and its corresponding matrix $L=M_0\circ \Lambda_{\beta}\circ M_0^{-1}$, we can compute with time complexity lower than $2^{16}$, a matrix $\widetilde{M}_0$ such that there exists a unique non-zero constant $\gamma$ in $GF(2^8)$, so that 

$$ \widetilde{M}_0=M_0\circ \Lambda_{\gamma}$$

***Proof***
<font color=blue>

$L=M_0\circ \Lambda_{\beta}\circ M_0^{-1} \Rightarrow L\circ M_0=M_0\circ \Lambda_{\beta}$

$$\Downarrow$$

seek for $\widetilde{M}_0$ such that 

$$L\circ \widetilde{M}_0=\widetilde{M}_0\circ \Lambda_{\beta}$$

$64$ unknown $\widetilde{M}_0$ $\Rightarrow$ $64$ equations
some non-trivial solution can be computed in time complexity $64^{\omega}<2^{16}$

$$\Downarrow$$

$$M\xlongequal{\text{def}}M_0^{-1}\circ \widetilde{M}_0$$

$$\Downarrow$$

$$L\circ (M_0\circ M)=(M_0\circ M)\circ \Lambda_{\beta} $$

$$(M_0\circ \Lambda_{\beta}\circ M_0^{-1} )\circ (M_0\circ M)=(M_0\circ M)\circ \Lambda_{\beta} $$

$$M_0\circ \Lambda_{\beta}\circ  M=M_0\circ M\circ \Lambda_{\beta} $$

$$M_0\circ (\Lambda_{\beta}\circ  M)=M_0\circ (M\circ \Lambda_{\beta} )$$

$$\Downarrow$$

<font color=darkorange>

$$\Lambda_{\beta}\circ M=M\circ \Lambda_{\beta}$$

the only $GF(2)$-affine mappings that commutes with the multiplication by $\beta$, are the multiplications by a $GF(2^8)$ element

</font>

$\Rightarrow$ write $M(x)= \sum_{i=0}^{7}\gamma_i\cdot x^{2^i}$

for all $x\in GF(2^8)$, $\Lambda_{\beta}\circ M=M\circ \Lambda_{\beta} \Rightarrow \sum_{i=0}^7\gamma_i\beta^{2^i}\cdot x^{2^i}=\sum_{i=0}^7\beta\gamma_i \cdot x^{2^i}$ 

$\beta$ is not contained in any subfield of $GF(2^8)$ $\Rightarrow$ implies $\gamma_i=00$ for all $i$ but $i\neq 0$

$$\Downarrow$$

$$M(x)=\gamma_0 x$$

$$\Downarrow$$

exists a unique $\gamma\in GF(2^8)$ such that $M=\Lambda_{\gamma}$

$$M=M_0^{-1}\circ \widetilde{M}_0$$

$$\Downarrow$$

$$\Lambda_{\gamma}=M_0^{-1}\circ \widetilde{M}_0$$

$$\Downarrow$$

<font color=darkorange>

$$\widetilde{M}_0=M_0 \circ \Lambda_{\gamma}$$

</font>

</font>

----
<center>
<font color=red>

recover $\gamma$ of `Proposition 2` 

$$\downarrow$$

fully determine $M_0$, the linear part of $Q_0$ $\Rightarrow$ $M_1,M_2,M_3$

$$\downarrow$$

recover $P_i$ up to the key, and $Q_0$

</font>
</center>

$$y_i(x_0,x_1,x_2,x_3) =Q_i(\alpha_{i,0} \cdot T'_0(x_0)\oplus \alpha_{i,1} \cdot T'_1(x_1)\oplus \alpha_{i,2} \cdot T'_2(x_2)\oplus \alpha_{i,3} \cdot T'_3(x_3))$$

$$\Downarrow$$

$$\begin{aligned}
y_0(x_0,x_1,x_2,x_3) &=Q_0(\alpha_{0,0} \cdot T'_0(x_0)\oplus \alpha_{0,1} \cdot T'_1(x_1)\oplus \alpha_{0,2} \cdot T'_2(x_2)\oplus \alpha_{0,3} \cdot T'_3(x_3))\\
&=Q_0\left(\bigoplus_{i=0}^{3}\alpha_{0,i}\cdot T'_i(x_i) \right)\\
&\xlongequal{T'_i=T_i\circ P_i}Q_0\left(\bigoplus_{i=0}^{3}\alpha_{0,i}\cdot T_i\circ P_i(x_i)\right)\\
\end{aligned}
$$

$$T_i(\mathcal{z})=S(\mathcal{z}\oplus k_i)$$

$$\Downarrow$$

$$x\mapsto S^{-1}\circ T_i\circ P_i(x)=P_i(x)\oplus k_i  \text{  is affine}$$

$$\widetilde{M}_0=M_0 \circ \Lambda_{\gamma}$$

----

$\textbf{Proposition 3.(original BGE)}$

There exists unique pairs $(\delta_i,c_i)_{(i=0,\cdots,3)}$ of elements in $GF(2^8)$, $\delta_i$ being non-zero, such that

$$\widetilde{P}_0:x\longmapsto (S^{-1}\circ \Lambda_{\delta_0}\circ \widetilde{M}_0^{-1})(y_0(x,00,00,00)\oplus c_0)$$

$$\widetilde{P}_1:x\longmapsto (S^{-1}\circ \Lambda_{\delta_1}\circ \widetilde{M}_0^{-1})(y_0(00,x,00,00)\oplus c_1)$$

$$\widetilde{P}_2:x\longmapsto (S^{-1}\circ \Lambda_{\delta_2}\circ \widetilde{M}_0^{-1})(y_0(00,00,x,00)\oplus c_2)$$

$$\widetilde{P}_3:x\longmapsto (S^{-1}\circ \Lambda_{\delta_3}\circ \widetilde{M}_0^{-1})(y_0(00,00,00,x)\oplus c_3)$$

are affine mappings. Any pairs $(\delta_i,c_i)$ can be computed with time complexity $2^{24}$. Moreover, those mappings are exactly $\widetilde{P}_i=P_i(x)\oplus k_i$

$$ \Downarrow$$

<center>

amounts to $x\rightarrow S^{-1}(\delta\cdot S(x)\oplus c)$

</center>

***Proof.***

<font color=blue>

$\bullet$ $S$: the AES-128 S-box

$\bullet$ $\delta$: non-zero

$\bullet$ $(\delta,c)=(01,00)$ $\Rightarrow$ the existence and uniqueness of $(\delta_i,c_i)$  (use an `exhaustive search`to verify)

$$\Downarrow$$

$c=00 \ \Rightarrow \ c_0=y_0(x,00,00,00)\oplus \alpha_{0,0}\cdot T_0(P_0(x))$


$\widetilde{A}_0=A_0\circ \Lambda_{1/\gamma}\ \Rightarrow \ \widetilde{P}_0(x)=S^{-1}\circ \Lambda_{\delta_0\cdot \gamma\cdot \alpha_{0,0}}\circ S(P_0(x)\oplus k_0)$

$$\Downarrow$$

$$\delta_0\cdot \gamma\cdot \alpha_{0,0}=01$$

$$\Downarrow$$

$$\widetilde{P}_0(x)=P_0(x)\oplus k_0$$



$$\begin{aligned}
y_0(x,00,00,00) &=Q_0(\alpha_{0,0} \cdot T'_0(x)\oplus \alpha_{0,1} \cdot T'_1(00)\oplus \alpha_{0,2} \cdot T'_2(00)\oplus \alpha_{0,3} \cdot T'_3(00))\\
& =Q_0(\alpha_{0,0} \cdot T'_0(x)\oplus c_0)\\
&\xlongequal{Q_0=M_0(x)\oplus q_0} M_0(\alpha_{0,0} \cdot T'_0(x)\oplus c_0)\oplus q_0\\
&\xlongequal{T'_i(\mathcal{z})=T_i\circ P_i(\mathcal{z})}M_0(\alpha_{0,0}\cdot T_0\circ P_0(x)\oplus c_0)\oplus q_0\\
&\xlongequal{T_i(\mathcal{z})=S(\mathcal{z}\oplus k_i)}M_0(\alpha_{0,0}\cdot S (P_0(x)\oplus k_0))\oplus c_0)\oplus q_0\\
&=M_0(\Lambda_{\alpha_{0,0}}( P_0(x)\oplus k_0)\oplus c_0)\oplus q_0\\
&=M_0 \circ  \Lambda_{\alpha_{0,0}}\oplus M_0(c_0)\oplus q_0\\
\end{aligned}$$

$$\Downarrow$$

$$\begin{aligned}
(S^{-1}\circ \Lambda_{\delta_0}\circ \widetilde{M}_0^{-1})(y_0(x,00,00,00)\oplus c_0)
&\xlongequal{y_0(x,00,00,00) =M_0(\Lambda_{\alpha_{0,0}}( P_0(x)\oplus k_0)\oplus c_0)\oplus q_0}(S^{-1}\circ \Lambda_{\delta_0}\circ \widetilde{M}_0^{-1})\left(M_0(\Lambda_{\alpha_{0,0}}( P_0(x)\oplus k_0)\oplus c_0)\oplus q_0\right)\oplus c_0\\
&\xlongequal{\widetilde{M}_0=M_0 \circ \Lambda_{1/\gamma}}(S^{-1}\circ \Lambda_{\delta_0}\circ \Lambda_{1/\gamma}^{-1}\circ M_0^{-1})\left(M_0(\Lambda_{\alpha_{0,0}}( P_0(x)\oplus k_0)\oplus c_0)\oplus q_0\right)\oplus c_0\\
&\xlongequal{\Lambda_{1/\gamma}^{-1}=\Lambda_{\gamma}}S^{-1}\circ \Lambda_{\delta_0}\circ \Lambda_{\gamma}\circ\left(\Lambda_{\alpha_{0,0}}( P_0(x)\oplus k_0)\oplus c_0\oplus q_0\right)\oplus c_0\\
&=S^{-1}\circ \Lambda_{\delta_0}\circ \Lambda_{\gamma}\circ \Lambda_{\alpha_{0,0}}( P_0(x)\oplus k_0)\oplus S^{-1}\circ \Lambda_{\delta_0}\circ \Lambda_{\gamma}\circ(c_0\oplus q_0 )\oplus c_0\\
&=S^{-1}\circ \Lambda_{\delta_0\cdot \gamma\cdot \alpha_{0,0}}\circ ( P_0(x)\oplus k_0)\\
\end{aligned}$$


$$\begin{aligned}
(S^{-1}\circ \Lambda_{\delta_0}\circ \widetilde{M}_0^{-1})(y_0(x,00,00,00)\oplus c_0)
&\xlongequal{c=00\ \Rightarrow \ c_0=y_0(x,00,00,00)\oplus \alpha_{0,0}\cdot T_0(P_0(x))}(S^{-1}\circ \Lambda_{\delta_0}\circ \widetilde{M}_0^{-1})\left(\alpha_{0,0}\cdot T_0(P_0(x))\right)\\
&\xlongequal{\widetilde{M}_0=M_0 \circ \Lambda_{1/\gamma}}(S^{-1}\circ \Lambda_{\delta_0}\circ \Lambda_{1/\gamma}^{-1}\circ M_0^{-1})\left(\alpha_{0,0}\cdot S (P_0(x)\oplus k_0)\right)\\
\end{aligned}$$


</font>

----

$$\downarrow$$

$2^{16}$ possible pairs $(\delta_i,c_i)$
test if the corresponding mapping is affine

$\bullet$ ***time complexity***

the lookup table has to be evaluated $2^8$ times
$8$ systems of $9$ unknowns over $GF(2)$, or equivalently $1$ system of $72$ unknowns which can be precomputed, has to be solved

the mapping evaluation through the lookup table dominates, the total time complexity is bounded by $2^{24}$

$$\downarrow$$

$$\delta_0\cdot \gamma\cdot \alpha_{0,0}=01 \ \Rightarrow \ \delta_i^{-1}=\gamma\cdot \alpha_{0,i}$$

$$\downarrow$$

$\alpha_{0,i}$: two $01$, one $02$, one $03$ $\Rightarrow$ two of the $\delta_i^{-1}$ are equal and share the common value $\gamma$ 

$$\Downarrow$$

know $\Lambda_{\gamma}$ and $\alpha_{0,i}$

$\Downarrow$ 

$M_0=\widetilde{M}_0\circ \Lambda_{\gamma}$  

$\bullet$ ***recover $q_0$*** at the same time

define 

$$\begin{aligned}
c_4=y_0(00,00,00,00)
&=Q_0\left(\bigoplus\limits_{i=0}^{3}\alpha_{0,i}\cdot T_i\circ P_i(00)\right)\\
&\xlongequal{Q_0=M_0(x)\oplus q_0} M_0\left(\bigoplus\limits_{i=0}^{3}\alpha_{0,i}\cdot T_i\circ P_i(00)\right)\oplus q_0\\
\end{aligned}$$

$$c_0=y_0(x,00,00,00)\oplus \alpha_{0,0}\cdot T_0(P_0(x))$$

$$c_1=y_0(00,x,00,00)\oplus \alpha_{0,1}\cdot T_1(P_1(x))$$

$$c_2=y_0(00,00,x,00)\oplus \alpha_{0,2}\cdot T_2(P_2(x))$$

$$c_3=y_0(00,00,00,x)\oplus \alpha_{0,3}\cdot T_3(P_3(x))$$

$$\Downarrow$$

$$q_0=c_0\oplus c_1\oplus c_2\oplus c_3\oplus c_4$$

$$\Downarrow$$

$$\text{fully recover the mapping } Q_0$$


$\textbf{Summarise}$

1. compute the non-linear part of any parasitic mapping $Q_i^{(r,j)} \ i=0,\dots,3$  and the non-linear part of its inverse parasitic mapping $P_i^{(r+1,j)}$—— up to some affine application $x\mapsto A_i^r(x)\oplus q_i^r$  $\Rightarrow$  $ 2^{24}$
2. recover $M_0 \rightarrow M_1,M_2,M_3$  $\Rightarrow$  $3\cdot 2^{16}$
3. recover the affine mapping $x\mapsto M_0^r(x)\oplus q_0^r$ for $r=2,\dots, 9$  $\Rightarrow$  $\text{lower than } 2^{16}$
4. retrieve the missing affine apart of $P_i^{(r,j)}$ up to the `key addition`  $\Rightarrow$  $3\cdot 2^{16}$
   
5. total time complexity: $4\cdot 4\cdot 2^{24}=2^{28}$



## Key Extraction

***the keys for two different rounds are related to each other***

determine $Q_i^{(r,j)}$

recover the parasitic mappings $Q_i^{(2,j)}$, $\widetilde{P}_i^{(3,j)}$, $Q_i^{(3,j)}$, $\widetilde{P}_i^{(4,j)}$ for $i=0,\dots,3$ and $j=0,\dots,3$

$P_i^{(r+1,j)}\circ Q_i^{r,j}$ must be the identity

$\Downarrow$

$k_i^{(3,j)}=\widetilde{P}_i^{(3,j)}\circ \overline{Q}_i^{(2,j)}$

$k_i^{(4,j)}=\widetilde{P}_i^{(4,j)}\circ \overline{Q}_i^{(3,j)}$

assume the round key were generated using the `key derivation algorithm` of AES-128 $\Rightarrow$ derive the whole set of round keys





----




the mappings $f$ that need to be tested for being affine are listed in `Proposition 3`

each $f$ is associated with a secret encoding $P_i^{(r,j)}(0\leq i,j\leq 3)$ of a round $r$

needs to be applied to ***two consecutive rounds***, this involves a total of $2\cdot 4 \cdot 4 $ mappings (which corresponds to the first three factors in $F$)

<font color=red>

$$f=S^{-1}\circ Q_{(c,d)}^{-1}\circ Q\circ S\circ \oplus_k \circ P \qquad (1)$$

$\bullet$ $S$: the AES S-box mapping (viewed as a permutation on $\mathbb{F}_2^8$)

$\bullet$ $k$: a key byte

$\bullet$ $P,Q$: bijective affine mappings on $\mathbb{F}_2^8$

$\bullet$ $Q_{(c,d)}^{-1}$: a bijective affine mapping on $\mathbb{F}_2^8$ for each pair $(c,d)\in \mathbb{F}_{256}^2$

$\bullet$ $Q_{(c,d)}^{-1}=Q^{-1}$ for one specific pair $(c,d)\in  \mathbb{F}_{256}^2$

</font>

if $f: \mathbb{F}_2^n\rightarrow \mathbb{F}_2^n$ is affine 
use the following algorithm to reduce the expected number of evaluations

$e_i(1\leq i \leq n)$: the $i$-th unit vector in $\mathbb{F}_2^n$

<font color=red>

$$ f(e_1 \oplus e_2)=f(0)\oplus f(e_1)\oplus f(e_2) \qquad  (2)$$

</font>

then the algorithm terminates with "$f$ is not affine"

requires 4 evaluations of $f$ in this case

$2^n$ evaluations of $f$ are required

<font color=blue>

$$ f(x)=A(x)\oplus b, A\in \mathbb{F}_2^{n\times n}, b\in \mathbb{F}_2^n$$

$$\Downarrow$$

$$f(0)\oplus f(e_1)\oplus f(e_2)=b\oplus A(e_1)\oplus b \oplus A(e_2)\oplus b= A(e_1 \oplus e_2)\oplus b= f(e_1 \oplus e_2)$$

</font>


# Reducing the Work Factor of the BGE Attack

1. an efficient method to ***retrieve the round key bytes*** of round $r+1$ after the round key bytes of round $r$ are extracted
2. an efficient method to ***determine the correct order of the round key*** bytes, given the round key bytes of two consecutive rounds

## Phases 1 and 2: Retrieve the Round Key Bytes $\overline{k}_i^{(r,j)}(0\leq i,j \leq 3)$ Associated with Round $r(2\leq r\leq 8)$



### Work factor of Phase 1

retrieve the round key bytes $\overline{k}_i^{(r,j)}(0\leq i,j \leq 3)$ with round $r$ for some $r$ with $2\leq r\leq 8$

four encodings for each of the four subrounds for each of the two consecutive rounds

retrieve one encoding up to an affine part using `Tolhuizen's method`

### Work factor of Phase 2

is measured in the number of evaluations of mappings on $\mathbb{F}_2^8$

the evalutaions are required to determine if a mapping on $\mathbb{F}_2^8$ is affine 

the mappings $f$ that need to be tested for being affine are listed in `Proposition 3`


each $f$ is associated with a secret encoding $P_i^{(i,j)}(0\leq i,j\leq 3)$ of a round $r$

needs to be applied to two consecutive rounds, this involves a total of $2\cdot 4 \cdot 4 $ mappings (which corresponds to the first three factors in $F$)

<font color=red>

$$f=S^{-1}\circ Q_{(c,d)}^{-1}\circ Q\circ S\circ \oplus_k \circ P \qquad (1)$$

$\bullet$ $S$: the AES S-box mapping (viewed as a permutation on $\mathbb{F}_2^8$)

$\bullet$ $k$: a key byte

$\bullet$ $P,Q$: bijective affine mappings on $\mathbb{F}_2^8$

$\bullet$ $Q_{(c,d)}^{-1}$: a bijective affine mapping on $\mathbb{F}_2^8$ for each pair $(c,d)\in \mathbb{F}_{256}^2$

$\bullet$ $Q_{(c,d)}^{-1}=Q^{-1}$ for one specific pair $(c,d)\in  \mathbb{F}_{256}^2$

</font>

if $f: \mathbb{F}_2^n\rightarrow \mathbb{F}_2^n$ is affine 
use the following algorithm to reduce the expected number of evaluations

$e_i(1\leq i \leq n)$: the $i$-th unit vector in $\mathbb{F}_2^n$

<font color=red>

$$ f(e_1 \oplus e_2)=f(0)\oplus f(e_1)\oplus f(e_2) \qquad  (2)$$

</font>

then the algorithm terminates with "$f$ is not affine"

requires 4 evaluations of $f$ in this case

$2^n$ evaluations of $f$ are required

<font color=blue>

$$ f(x)=A(x)\oplus b, A\in \mathbb{F}_2^{n\times n}, b\in \mathbb{F}_2^n$$

$$\Downarrow$$

$$f(0)\oplus f(e_1)\oplus f(e_2)=b\oplus A(e_1)\oplus b \oplus A(e_2)\oplus b= A(e_1 \oplus e_2)\oplus b= f(e_1 \oplus e_2)$$

</font>

-----
### Lemma 1

If $f$ is a random permutation on $\mathbb{F}_2^n$ and if $E(n)$ denotes the expected number of evaluations of $f$ required by the algorithm descired above, then $E(n)<5$

***Proof.*** 
$p(n)$: the probability that `Eq.(2)` holds true for a random permutation
$f$: a random permutation
$f(e_1\oplus e_2)$: a random element of this set
$$p(n)=1/(2^n-3)$$

$$E(n)=4(1-p)+2^np=4+(2^n-4)/(2^n-3)<5$$

----

## Phase 3: Retrieve the Round Key Bytes $\overline{k}_i^{(r+1,j)}(0\leq i,j\leq 3)$ Assiciated with Round $r+1$

obtain the round key bytes of round $r+1$ by applying `Phases 1` and `Phase 2` to round $r+1$ as well

### Step 1
apply `Phase 1`(using `Tolhuizen's improvement`) to round $r+1$ in order to retrieve the encodings $Q_i^{(r+1,j)}(0\leq i \leq 3)$ up to an affine part

### Step 2
1. removes the non-affine part of the output encodings as recovered in `Step 1` from the encoded AES subround
2. `Step 2` removes the input encodings $P_i^{(r+1,j)}(0\leq i \leq 3)$ form the encoded AES subround (observe that the inverses of these input encodings were obtained in `Phase 1` and `Phase 2`)
   $$f^{(r+1,j)}=(\hat{Q}_0^{(r+1,j)},\hat{Q}_1^{(r+1,j)},\hat{Q}_2^{(r+1,j)},\hat{Q}_3^{(r+1,j)})\circ \overline{AES}^{(r+1,j)}$$

   $\hat{Q}_i^{(r+1,j)}(0\leq i \leq 3)$: affine output encodings

### Step 3
retrieves the round key bytes $\overline{k}_i^{(r+1,j)}(0\leq i \leq 3)$
find a key byte $\overline{k}_0^{(r+1,j)}$, fix the other three input bytes to $f^{(r+1,j)}=0$
search over all possible $2^8$ values of the key byte $k$ and verify if 
$$g_k(x)=f^{(r+1,j)}(k\oplus S^{-1}(x),0,0,0)$$

is affine using the test described above
$g_k(x)$ is affine, then $\overline{k}_0^{(r+1,j)}=k$
repeat this for $\overline{k}_i^{(r+1,j)}(i=1,2,3)$

the correctness of `Step 3` uses the fact that the mapping $S(c\oplus S^{-1}(x))$ is non-affine for all non-zero values of $c$

### Work factor of Phase 3

$$4\cdot 4\cdot 2^7\cdot 5 \approx 2^{13}$$

$\bullet$ $4\cdot 4$: the number of round key bytes

$\bullet$ $2^7$: the expected number of key values for which the `affine-test` is performed

$\bullet$ $5$: the `expected number` of evaluations of the `affine-test` if $g_k$ is not affine

`Step 1` is $4\cdot 4\cdot (35\cdot 2^8)<2^{18}$
the first two factors: the number of output encodings involved in `Step 1`
the work factor of `Phase 3` is dominated by `Step 1` and is less than $2^{18}$

## Phase 4: Determine the Correct Order of the Round Key Bytes and Extract the Secret AES Key

the values of the round key bytes of two consecutive round $r$ and $r+1$ are known

`the order of the round key bytes of each subround` and `the order of the four subrounds` ate still unknown

how the correct order can be determined given the `"shuffled"` round key bytes of round $r$ and $r+1$

### Step 1 

1. retrieves $MC^{(r,j)}$ 
2. recall that the encodings $P_i^{(r,j)}$ and $Q_i^{(r,j)}(0\leq i,j \leq 3)$ were obtained in Phases 1 and 2
3. together with the knowledge of the round key bytes  $\overline{k}_i^{(r,j)}(0\leq i,j \leq 3)$ 
4. compute
   <font  color=blue>

   $$\overline{AES}_{enc}^{(r,j)}=(Q_0^{(r,j)},Q_1^{(r,j)},Q_2^{(r,j)},Q_3^{(r,j)})\circ MC^{(r,j)}\circ (S,S,S,S)\circ \oplus_{[\overline{k}_i^{(r,j)}]_{0\leq i \leq  3}}\circ (P_0^{(r,j)},P_1^{(r,j)},P_2^{(r,j)},P_3^{(r,j)})$$
   
   $$\Downarrow$$

   </font>

   $$MC^{(r,j)}=(Q_0^{(r,j)},Q_1^{(r,j)},Q_2^{(r,j)},Q_3^{(r,j)})^{-1}\circ \overline{AES}_{enc}^{(r,j)}\circ  (P_0^{(r,j)},P_1^{(r,j)},P_2^{(r,j)},P_3^{(r,j)})^{-1} \circ \oplus_{[\overline{k}_i^{(r,j)}]_{0\leq i \leq  3}}\circ (S,S,S,S)^{-1}$$
   for $j=0,1,2,3$

### Step 2

compute for each $MC^{(r,j)}(0\leq j \leq 3)$ the permutations $\Pi_1,\Pi_2:(\mathbb{F}_2^8)^4\rightarrow  (\mathbb{F}_2^8)^4$ such that 

$$MC^{(r,j)}=\Pi_2\circ MC \circ \Pi_1$$

$(\Pi^{(1)},\Pi^{(2)})$: the pairs of permutations for which $MC$ remains invariant, i.e.,

$$MC=\Pi^{(2)}\circ MC \circ \Pi^{(1)} \qquad (3)$$

there are exactly **four** such pairs

the four permutations $\Pi^{(1)}$ are ***the four different circular shifts*** on the indices of a 4-byte vector, and $\Pi^{(2)}=(\Pi^{(1)})^{-1}$ for each of these pairs
  
$$\downarrow$$

there are also exactly four different pairs of permutations satisfying `Eq.(3)`, given by 

$$(\Pi^{(1)}\circ \Pi_1,\Pi_2\circ \Pi^{(2)}) \qquad (4)$$

$$\downarrow$$

finding ***one pair of permutation matrices*** satisfying `Eq.(3)` suffices to find the remaining three as well

one of these four pairs of permutations equals the pair $(\Pi_1^{(r,j)},\Pi_2^{(r,j)})$ of the encoded subround; in other words, one of these pairs is the correct pair

$$\downarrow$$

the order of the round key bytes associated with each subround is known up to an uncertainty of four possibilities (circular shifts)

$$\downarrow$$

the order of the four subrounds is still unknown

### Step 3 

determine the correct order of the round key bytes 

$$\downarrow$$

obtain a candidate for the $(r+1)^{th}$ round key using the following two methods:
(i) the AES key scheduling algorithm
(ii) the data-flow of the white-box AES implementation between the encoded subrounds of rounds $r$ and $r+1$

once an order of the round $r+1$ can be determined using the corresponding pair of permutations of each of the subround of round $r$ and the data-flow of the white-box implementation

with overwhelming prabability, only one ordering of round key bytes of round $r$ results in the same $(r+1)^{th}$ round key

this ordering corresponds to the correct round key of round $r$

$$\downarrow$$

use the property of `the AES key scheduling algorithm` that the AES key can be computed if one of the round keys is known

### Work factor of Phase 4

$\bullet$ a naive approach
$(4!)^2 \approx 2^9$: for `Step 2` by searching over all possible pairs of permutations 

$\bullet$ `Step 2` reduces the number of possible orderings of the round key bytes from $2^{23}$ to $4^4 \cdot 4!<2^{13}$ which equals the work factor of `Step 3`
the first and second factor: the possible orderings of round key bytes within each subround and of the four subrounds, respectively

$$\downarrow$$

the overall work factor of `Phase 4` is dominated by the work factor of `Step 3` and hence is less than $2^{13}$



# Reference

<div id="refer-anchor-1"></div>

[1] Chow S, Eisen P, Johnson H, et al. White-box cryptography and an AES implementation[C]. International Workshop on Selected Areas in Cryptography. Springer, Berlin, Heidelberg, 2002: 250-270.

<div id="refer-anchor-2"></div>

[2] Billet O, Gilbert H, Ech-Chatbi C. Cryptanalysis of a white box AES implementation[C]. International Workshop on Selected Areas in Cryptography. Springer, Berlin, Heidelberg, 2004: 227-240.

<div id="refer-anchor-3"></div>

[3] Tolhuizen L. Improved cryptanalysis of an AES implementation[C]. Proceedings of the 33rd WIC Symposium on Information Theory in the Benelux. 2012: 68-71.

<div id="refer-anchor-4"></div>

[4] De Mulder Y, Roelse P, Preneel B. Revisiting the BGE attack on a white-box AES implementation[J]. IACR Cryptol. ePrint Arch., 2013, 2013: 450.

Lepoint T, Rivain M. Another Nail in the Coffin of White-Box AES Implementations[J]. IACR Cryptol. ePrint Arch., 2013, 2013: 455.

Lepoint T, Rivain M, De Mulder Y, et al. Two attacks on a white-box AES implementation[C]. International Conference on Selected Areas in Cryptography. Springer, Berlin, Heidelberg, 2013: 265-285.