---
layout: post
title:  "Inbetween Obfuscation and the Random Oracle"
date:   2016-10-10 12:00:00 +0100
categories: cryptography obfuscation
---

There's lots of similarities in the articles that talk about the impossibility
of instantiating the random oracle and obfuscating programs.
The biggest takeaway of these stories is that there is a substantial difference between giving
a program as input and simply giving oracle access (duh...).

The topic of obfuscation has been a long-standing quest in crypto, because of its
appealing implications, like:
- any symmetric key cryptosystem can become homomorphic.
  (Just obfuscate decryption composed with group operation, then make it public)
- software watermarking would become trivial
  (Just change the program behavior so that it uniquely identifies the
  user).
- asymmetric encryption can be reduced to symmetric encryption.
  (On could public the obfuscation of the symmetric key algorithm with embedded
  the secret key, and use it as a public key).
  Interestingly enough, before Diffie-Hellman this was the reason asymmetric
  encryption was believed to exist.


We start by formally defining what is an obfuscator, respectively for Turing Machines
and circuits.

<div class="definition" text="TM Obfuscator" markdown="1">
A probabilistic algorithm $O$ is a TM obfuscator for a family $F$ of
Turing Machines if:
- (_functionality_) $\forall M \in F: [O(M)] = [M]$ (read: they compute the
same function);
- (_polynomial slowdown_) $\forall M \in F: O(M) \in \mathsf{poly}(\lambda)$
- (_virtual black-box property_) $\forall$ $\adv$ $\ppt$ distinguisher:
  $
  \exists S \in \ppt:
  \forall M \in F: \abs{\Pr[\adv(O(M)) = 1] - \Pr[S^{\gen{M}}(1^{\size{M}})]}
  \leq \negl(\size{M}).
  $
</div>

In the above, $\gen{M}$ is the machine that emulates $M$ only for the first $t$
reductions, i.e.:

$$
 \gen{M}: 1^k \to \bin^* \to \bin^*: (1^t, x) \mapsto
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
- (_functionality_) as before, $C$ and $O(C)$ compute the same function;
- (_polynomial slowdown_) as above;
- (_virtual black-box property_) $\forall \adv ~~\ppt$ distinguisher
$
\exists S \in \ppt:
\forall M \in F \abs{\Pr[\adv(O(C)) = 1] - \Pr[S^{C}(1^{\size{C}}) = 1]}
\leq \negl(\size{M}).
$
</div>
It is easy to convince ourselves that if a Turing Machine obfuscators exist,
then circuit obfuscator exists (prop 2.3). This little remark will be useful
later in the proof.
When the family of Turing Machines $F$ is omitted it is assumed to be the
collection of all Turing Machines (circuits, respectively).

The above definition cover the smallest possible requirements one could give to
obfuscation. In fact, one could give space to imagination and attempt to reach
something like the following:
1. _Computational Indistinguishability_: The output stream of any $\ppt$
machine given access to $O(P)$ is computationally indistinguishable from the
one when given just oracle access.
2. _Satisfying a Relation_: the adversary produces an output that satisfies some
relation $R$ together with the input program (possibly $\poly$-computable
relations).
The same stream can be produced (with roughly the same probability) when given
$O(P)$ as input as when given only oracle access to $P$.
2. _Computing a Function_: as above, but the relation is a function
3. _Satisfying a Predicate_: as above, but the function has image in $\bin$.

<div markdown="1" class="proposition"> It is impossible to satisfy 2.
</div>
<div class="proof">
Consider the relation $(P, O(P))$. It is always satisfied when given
to an obfuscated program, but difficult when given only random oracle access.
</div>


<div markdown="1" class="definition" text="2-TM/2-Circuit Obfuscator">
Everything is still the same as in the above except
- _virtual black-box property_:
$
\forall \adv ~~\ppt$ distinguisher $\exists S \in \ppt: \forall C, D \in F
\abs{\Pr[A(O(C), O(D)) = 1] - \Pr[S^{\gen{C, D}}(1^{\size{C} + \size{D}})]} \leq
\negl(\min(C, D))$
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
\Pr[\adv(O(C_{\alpha, \beta}), O(D_{\alpha, \beta}))] &\defeq \Pr[\adv_C] = 1
\tag{i} \\
\abs{
  \Pr[S^{C_{\alpha, \beta}~~ D_{\alpha, \beta}}(\secparam) =1] -
  \Pr[S^{Z_\secpar ~~ D_{\alpha, \beta}}(\secparam) = 1]} &\defeq
\abs{ \Pr[S_C] - \Pr[S_Z]} \leq
2^{-\Omega(\secpar)}
\tag{ii} \\
\Pr[\adv(O(Z_\secpar), O(D_{\alpha, \beta}) = 1] &\defeq \Pr[\adv_Z] =
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
In all this, we did not prove that $\adv(O(C_\alpha, \beta), O(D_{\alpha,
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

i.$\Pr[\adv(O(F_{\alpha, \beta}) = 1] = 1$

ii. $
\abs{
\Pr[S^{F_{\alpha, \beta}}(\secparam)=1] -
\Pr[S^{G_{\alpha,\beta}}(\secparam) = 1]} \leq
2^{-\Omega(\secpar)
}$

iii. $\Pr[\adv(O(G_{\alpha, \beta}) = 1] = 2^{-k}$

Just as before, this leads to a contraddiction.
</div>

In the circuit case, we can't bring the same argument; $\adv$ on input
$O(F_{\alpha, \beta}) = O(C_{\alpha, \beta} \concat D_{\alpha, \beta}) = F_0
\concat F_1$:
- requires $\size{F_1} > \size{F_0}$ as one takes as input the other, but we
have no guarantee for this after polynomial slowdown by the obfuscator.
- requires $\size{F_0}= \size{F_1}$ since $F_0 = O(F_{\alpha, \beta})(0, x)$ and
$F_1 = O(F_{\alpha, \beta})(1, x)$.

So proving it is a bit of a mess.


Before closing up, the authors of XXX show a weaer defition of obfuscator, to
prove that indeed there exists iO.

<div class="definition" text="iO" markdown="1">
An _indistinguishability obfuscator_ is defined aas a circuit obfuscator except
that the virtual black-box property is reduced to:
- (_indistinguishability_) $\forall \adv \in \ppt \quad \exists \alpha$
negligible function such that $\forall C_0, C_1$ circuits computing the same
function such that $\size{C_0} = \size{C_1} = \secpar$:
$$
\abs{\Pr[\adv(O(C_0)) = 1] - \Pr[\adv(O(C_1)) = 1]} \leq \negl(\secpar).
$$
</div>


<div class="proposition">
Indistinguishability Obfuscation exists.
</div>
<div class="proof">
Let O(C) be the lexicographically first circuit of size $\size{C}$ that computes
the same function as $C$.
</div>
We now go though the random oracle rabbit hole.

We show the existence of (contrived) protocols that are secure in the Random
Oracle model but for which there exist no secure implementation.

<div class="definition">
A length function is a map $\ell: \NN \to \NN$ super-logarithmic and
polynomially bounded: $\ell(\secpar) \in \Omega(\secpar) \cap O(\secpar)$.
</div>

<div class="definition" text="Function Ensemble">
Given $\ell: \NN \to \NN$ a length function, an $\ell$-ensemble is a sequence $F
= \set{F_\secpar}_\secpar$ of families of maps $F_\secpar = {f_s: \bin^* \to
\bin^{\ell(\secpar)}}_{s \in \bin^\secpar}$ so that $\exists \eval \in
\poly(\secpar)$ such that $\forall s, x \in \bin^* . [\eval(s, x)] = f_s(x).
$s$ is called description or seed.
</div>


We attempt to define relations that are hard to satisfy in the Random Oracle
model:

<div class="definition">
A relation $R$ is said to be evasive w.r.t. some length function $\ell: \NN \to
\NN$ if $\forall M ~~\ppt$ machine with oracle access:
$$
\Pr_O[x \gets M^O(\secparam) ~\land~ (x, O(x)) \in R] \leq \negl(\secpar)
$$
</div>

So for instance the relation $(x, 0 \concat y^{\secpar - 1}) \quad\forall x, y$ is
not evasive, while $(x, 0^\secpar)$ is so.

Note: informally, a random oracle is a machine that upon receiving $x \in
\bin^*$:
- if it was already queried, reply with the same response;
- if it wasn't, select a uniformly random element from $\bin^{\ell(\secpar)}$ and
output it as $O(x)$.

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
\Pr_O[x \gets M^O(\secparam), (x, O(x)) \in R] &\leq
\poly(\secpar) \sup \left( \Pr_{x \in \bin^*}[(x, O(x)) \in R] \right) \\ &\leq
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
R^F \\
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


With the above knowledge, the authors prove the existence of a signature scheme
that is existentially unforgeable under chosen-message attack in the random
oracle model but for any possible choice of $F$ the scheme is insecure.
