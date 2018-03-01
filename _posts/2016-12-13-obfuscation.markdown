---
layout: post
title:  "In Between Obfuscation and the Random Oracle"
date:   2017-12-13 14:00:00 +0100
categories: cryptography obfuscation
---

There's lots of similarities in the articles that talk about the impossibility
of instantiating the random oracle [[CanGolHal98]] and obfuscating programs [[BGIRSV01]].
The biggest takeaway of these stories is that for a long time we (_we_ as in _the
crypto community_) have been thinking about using oracles to abstract away
details and make proofs of security easier to deal with.
Then, in the real life, one would just have to replace those procedure with an actual
implementation of the primitive (like, `sha1` or `Dual_EC_DRBG`).
This is totally wrong: there is a substantial difference between giving
a program as input and just providing oracle access to it.
On the other hand, experience has shown that this idea, although formally wrong,
can be a pretty good heuristic.
<br/>
<br/>

#### Obfuscation

The topic of obfuscation has been a long-standing quest in crypto, because of its
appealing implications, like:
- asymmetric encryption could be reduced to symmetric encryption.
  (On could public the obfuscation of the symmetric key algorithm with embedded
  the secret key, and use it as a public key).
  Interestingly enough, before Diffie-Hellman this was the reason asymmetric
  encryption was believed to exist.
- any symmetric key cryptosystem can become homomorphic.
  (Just obfuscate decryption composed with group operation, then make it public)
- software watermarking would become trivial
  (Just change the program behavior so that it uniquely identifies the
  user).


We start by formally defining what is an obfuscator, respectively for Turing Machines
and circuits.

<div class="definition" text="TM Obfuscator" markdown="1">
A probabilistic algorithm $\oracle$ is a TM obfuscator for a family $F$ of
Turing Machines if:
- (_functionality_) $\forall M \in F: [\oracle(M)] = [M]$ (read: they compute the
same function);
- (_polynomial slowdown_) $\forall M \in F: \oracle(M)$ runs in $\mathsf{poly}(\size{M})$
- (_virtual black-box property_) $\forall$ $\adv$ $\ppt$ distinguisher:
  $
  \exists S \in \ppt:
  \forall M \in F: \abs{\Pr[\adv(\oracle(M)) = 1] - \Pr[S^{\gen{M}}(1^{\size{M}})]}
  \leq \negl(\size{M}).
  $
</div>

In the above, $\gen{M}$ is the machine that emulates $M$ only for the first $t$
reductions, i.e.:

$$
 \gen{M}: 1^* \to \bin^* \to \bin^*: (1^t, x) \mapsto
 \begin{cases}
 y = M(x) &\text{if } M(x) \text{ halts within } t \text{ steps,} \\
 \bottom &\text{otherwise}.
 \end{cases}.
$$

On the other hand
$\negl$ is the family of functions $\NN \to \RR$ that go to zero faster than
the inverse of any polynomial, i.e.
$\forall c > 0 . \exists k_c: (\negl(k_c) \leq k^{-c} ~~\forall k > k_c)$.


<div markdown="1" class="definition" text="Circuit Obfuscator">
A probabilistic algorithm $O$ is a circuit
obfuscator for the following family $F$ of circuits if it satisfies:
- (_functionality_) as before, $C$ and $\oracle(C)$ compute the same function;
- (_polynomial slowdown_) as above;
- (_virtual black-box property_) $\forall \adv ~~\ppt$ distinguisher
$
\exists S \in \ppt:
\forall M \in F \abs{\Pr[\adv(\oracle(C)) = 1] - \Pr[S^{C}(1^{\size{C}}) = 1]}
\leq \negl(\size{M}).
$
</div>
When the family of Turing Machines $F$ is omitted it is assumed to be the
collection of all Turing Machines (circuits, respectively).

It is easy to convince ourselves that if a Turing Machine obfuscators exist,
then circuit obfuscator exists ([[BGIRSV01]], Prop 2.3). In some sense, this
means that
proving impossibility for Turing Machines is easier than for circuits.

One could also wonder why the authors used this _virtual black box property_ instead of
something else.
In order of aspiration, one could attempt to reach out for:
1. _Computational Indistinguishability_: The output stream of any $\ppt$
machine given access to $\oracle(P)$ is computationally indistinguishable from the
one when given just oracle access.
2. _Satisfying a Relation_: the adversary produces an output that satisfies some
relation $R$ together with the input program (possibly $\poly$-computable
relations).
The same stream can be produced (with roughly the same probability) when given
$\oracle(P)$ as input as when given only oracle access to $P$.
2. _Computing a Function_: as above, but the relation is a function
3. _Satisfying a Predicate_: as above, but the function has image in $\bin$.

Turns out this is the smallest possible requirements one could
give to obfuscation, and the instatisfiability for all the above definitions
is pretty straightfoward. For instance:
<div markdown="1" class="proposition"> It is impossible to satisfy 2.
</div>
<div class="proof">
Consider the relation $\set{(P, \oracle(P))}_{P \in \ppt}$. It is always satisfied when given
as input an obfuscated program, but difficult when given only oracle access.
</div>


<div markdown="1" class="definition" text="2-TM/2-Circuit Obfuscator">
Everything is still the same as in the above except:
- _virtual black-box property_:
$
\forall \adv ~~\ppt$ distinguisher $\exists S \in \ppt: \forall C, D \in F
\abs{\Pr[A(\oracle(C), \oracle(D)) = 1] - \Pr[S^{\gen{C, D}}(1^{\size{C} + \size{D}})]} \leq
\negl(\min(\size{C}, \size{D}))$
</div>

<div class="proposition"> Neither 2-TM obfuscator not 2-circuit obfuscator
exist.
</div>
<div markdown="1" class="proof">
We start defining some functions that are difficult to learn with oracle queries:
given $\alpha, \beta \in \bin^\secpar$ define:

$$
\begin{aligned}
C_{\alpha, \beta}(x) &\defeq
\begin{cases}
\beta & \text{if } x = \alpha \\
0^\secpar & \text{otherwise}.
\end{cases}
\\
D_{\alpha, \beta}(C) &\defeq
\begin{cases}
1 & \text{if } C(x) = \beta \\
0 & \text{otherwise.}
\end{cases}
\\
Z_\secpar(x) &\defeq 0^\secpar
\end{aligned}
$$

Note: $D_{\alpha, \beta}$ can be uncomputable, but we can execute $C$ for
$\poly$ steps and output $0$ is none was given from the execution of $C$.
Now consider the adversary $\adv \in \ppt : \adv(C, D) = D(C)$.
Note: also in this case we should let $\adv$ run only for $\poly(\min(\size{C},
\size{D})) \subset \poly(\secpar)$.
Then, the following holds:<br/>

$$
\begin{aligned}
\Pr[\adv(\oracle(C_{\alpha, \beta}), \oracle(D_{\alpha, \beta}))] &\defeq \Pr[\adv_C] = 1
\tag{i} \\
\abs{
  \Pr[S^{C_{\alpha, \beta}~~ D_{\alpha, \beta}}(\secparam) =1] -
  \Pr[S^{Z_\secpar ~~ D_{\alpha, \beta}}(\secparam) = 1]} &\defeq
\abs{ \Pr[S_C] - \Pr[S_Z]} \leq
2^{-\Omega(\secpar)}
\tag{ii} \\
\Pr[\adv(\oracle(Z_\secpar), \oracle(D_{\alpha, \beta}) = 1] &\defeq \Pr[\adv_Z] =
2^{-\secpar}.
\tag{iii}
\end{aligned}
$$

By contradiction, if a 2-TM obfuscator existed, we would have that
i.
$
\abs{\Pr[\adv_C] - \Pr[S_C]} \leq \negl(\secpar)
$
(for any adversary $\adv$ there exists $S$);
ii.
$
\abs{\Pr[\adv_Z] - \Pr[S_Z]} \leq \negl(\secpar)
$
(for any adversary $\adv$ there exists $S$).
Therefore:

$$
\begin{aligned}
\negl(\secpar) &\geq \abs{\Pr[\adv_C] - \Pr[S_C]} + \abs{\Pr[\adv_Z] - \Pr[S_Z]} \\
      &= \abs{\Pr[\adv_C] - \Pr[S_C]} + \abs{\Pr[S_Z] - \Pr[\adv_Z]} \\
      &\geq \abs{\Pr[\adv_C] - \Pr[S_C] + \Pr[S_Z] - \Pr[\adv_Z]} \\
      &= \abs{1 - 2^\secpar + (\Pr[S_Z] - \Pr[\adv_Z])} \\
      &\geq \abs{1 - \negl(\secpar)},
\end{aligned}
$$

which is a contradiction.
In all this, we did not prove that $\adv(\oracle(C_\alpha, \beta), \oracle(D_{\alpha,
\beta})) \in \poly(\secpar)$, however this follows immediately from the polynomial
slowdown property of obfuscators.
</div>

The proof for the circuit case is very similar, we just need to make
$D_{\alpha, \beta}$ big enough to contain a description of $C$ as input. This
This can be solved letting $D_{\alpha, \beta}$ have input length $\poly$ and the
proof would work as before.


<div class="theorem"> TM obfuscators do not exist.</div>

<div markdown="1" class="proof">
Given $f_0, f_1: X \to Y$ define $f_0 \concat f_1: \bin \times X \to Y:
(b, x) \mapsto f_b(x)$ the concatenation of two functions (or Turing Machines,
or circuits).

Consider $\adv$ to be the adversary that behaves like in the previous
proposition but after splitting hte concatenated programs:

$$
\adv(C) = \adv(C_0 \concat C_1) = C_1(C_0).
$$
Then define $F_{\alpha, \beta} \defeq C_{\alpha, \beta} \concat D_{\alpha,
\beta}$ and
$G_{\alpha, \beta} \defeq Z_\secpar \concat D_{\alpha, \beta}$. We have that:

$$
\begin{aligned}
\Pr[\adv(\oracle(F_{\alpha, \beta}) = 1] &= 1
\tag{i} \\
\abs{
\Pr[S^{F_{\alpha, \beta}}(\secparam)=1] -
\Pr[S^{G_{\alpha,\beta}}(\secparam) = 1]} &\leq
2^{-\Omega(\secpar)
}
\tag{ii} \\
\Pr[\adv(\oracle(G_{\alpha, \beta}) = 1] &= 2^{-k}.
\tag{iii}
\end{aligned}
$$

Just as before, this leads to a contraddiction.
</div>

In the circuit case, we can't bring the same argument; $\adv$ on input
$\oracle(F_{\alpha, \beta}) = \oracle(C_{\alpha, \beta} \concat D_{\alpha, \beta}) = F_0
\concat F_1$:
- requires $\size{F_1} > \size{F_0}$ as one takes as input the other, but we
have no guarantee for this after polynomial slowdown by the obfuscator.
- requires $\size{F_0}= \size{F_1}$ since $F_0 = \oracle(F_{\alpha, \beta})(0, x)$ and
$F_1 = \oracle(F_{\alpha, \beta})(1, x)$.

So proving it is a bit of a mess. I have a good summary of the steps that need
to be taken, but I still need to put them down here.

Before closing up, the authors attempt to patch up the defition of obfuscator.
They define something called _indistinguishability-obfuscation_ (iO for
friends), which is one of the nicest unicorns in crypto these days.

<div class="definition" text="iO" markdown="1">
An _indistinguishability obfuscator_ is defined as a circuit obfuscator except
that the virtual black-box property is reduced to:
- (_indistinguishability_) $\forall \adv \in \ppt \quad \exists \alpha \in \negl(\secpar)$ such that $\forall C_0, C_1$ circuits computing the same
function and having the same size $\size{C_0} = \size{C_1} = \secpar$:

$$
\abs{\Pr[\adv(\oracle(C_0)) = 1] - \Pr[\adv(\oracle(C_1)) = 1]} \leq \alpha.
$$

</div>


<div class="proposition">
Indistinguishability Obfuscation exists.
</div>
<div class="proof">
Let $\oracle(C)$ be the lexicographically first circuit of size $\size{C}$ that computes
the same function as $C$.
</div>

<br/>
<br/>

#### The Random Oracle Methodology

The random oracle rabbit hole is pretty much of the same type. In here, however,
we show that there are _relations_ that one can trivially satisfy when playing
with actual implementations and never with oracle access.

In addition to that, the _coup de grace_ of [[CanGolHal98]] consists in showing the existence of
(contrived) protocols that are secure in the Random Oracle model but for which
there exist no secure implementation.

Informally, a random oracle $\oracle$ is a machine that upon receiving $x \in
\bin^*$:
- if it was already queried, reply with the same response;
- if it wasn't, select a uniformly random element from $\bin^{\ell(\secpar)}$ and
output it as $\oracle(x)$.

<div class="definition">
A length function is a map $\ell: \NN \to \NN$ super-logarithmic and
polynomially bounded.
</div>

<div class="definition" text="Function Ensemble">
Given $\ell: \NN \to \NN$ a length function, an $\ell$-ensemble is a sequence $F
= \set{F_\secpar}_{\secpar \in \NN}$ of families of maps $F_\secpar = \set{f_s: \bin^* \to
\bin^{\ell(\secpar)}}_{s \in \bin^\secpar}$ so that $\exists \eval \in
\poly(\secpar)$ such that $\forall s, x \in \bin^* . [\eval(s, x)] = f_s(x).$
The string $s$ is called description or seed.
</div>


We attempt to define relations that are hard to satisfy in the Random Oracle
model:

<div class="definition">
A relation $R$ is said to be evasive w.r.t. some length function $\ell: \NN \to
\NN$ if $\forall M ~~\ppt$ machine with oracle access:
$$
\Pr_\oracle[x \gets M^\oracle(\secparam) ~\land~ (x, \oracle(x)) \in R] \leq \negl(\secpar)
$$
</div>

So for instance the relation $(x, 0 \concat y^{\secpar - 1}) \quad\forall x, y$ is
not evasive, while $(x, 0^\secpar)$ is so.

<div class="proposition">
If $R$ is a binary relation satisfying:
$$
\sup_{x \in \bin^*} \left(
\Pr_{y \in \bin^{\ell(\secpar)}}[(x, y) \in R]
\right)
\leq \negl(\secpar)
$$
then $R$ is evasive.
</div>

<div class="proof">
$$
\begin{aligned}
\Pr_O[x \gets M^\oracle(\secparam), (x, \oracle(x)) \in R] &\leq
\poly(\secpar) \sup \left( \Pr_{x \in \bin^*}[(x, \oracle(x)) \in R] \right) \\ &\leq
\poly(\secpar) \negl(\secpar) = \negl(\secpar).
\end{aligned}
$$
</div>

<div class="definition" text="Correlation Intractability">
Let $F$ be a $\ell$-ensemble. We say that $F$ is correlation intractable
w.r.t. $R$ if $R$ is fixed.
</div>

Note: we are considering all binary relation between bitstrings here; a weaker
notion could require $R$ to be decided in $\poly(\secpar)$. However, this will
not change the impossibility results.


<div class="theorem">
Correlation intractable function ensemble do not exist.
</div>
<div class="proof">
Let $F$ be an $\ell$-ensemble. Consider the following binary relation:
$$
R^F \defeq \bigcup_{\secpar \in \NN} \set{(s, f_s(s)) | s \in \bin^\secpar}.
$$
Clearly, this relation is poly-time decidable since $f_s \in \poly(\secpar)$ and
evasive, since:
$$
\begin{aligned}
& \forall x \in \bin^\secpar \quad \exists!y \in \bin^{\ell(\secpar)} . (x, y) \in
R^F
\tag{by definition of function}\\
\implies & \Pr_y[(x, y) \in R^F] \leq 2^{-\ell(\secpar)} \leq \negl(\secpar).
\end{aligned}
$$

However, if we consider the machine $\1(x) = x \quad \forall x \in \bin^*$:
$$
\begin{aligned}
& \Pr_{s \in \bin^{\secpar}}[x \gets \1(s), (x, f_s(x)) \in R^F] \\
=& \Pr_{s \in \bin^{\secpar}}[(s, f_s(s)) \in R^F] = 1,
\end{aligned}
$$
which means that $F$ is not correlation intractable.
</div>


The above theorem should be already enough to be convinced of the
uninstantiability of the random oracle.
In the origial paper [[BGIRSV01]] the authors prove the existence of a signature
scheme that is existentially unforgeable under chosen-message attack in the
random oracle model but for any possible choice of $F$ the scheme is insecure.


[BGIRSV01]: <https://www.iacr.org/archive/crypto2001/21390001.pdf>
[CanGolHal98]: <https://eprint.iacr.org/1998/011.pdf>
