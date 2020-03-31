---
layout: post
title: "Cash Systems"
date: 2018-11-29 00:00:00 +0100
categories: cryptography
---

<div class="announcement">
This post is still a work in progress. But, at least, now I have a CSS all hacked-up to for displaying game-based notions
as in <a href="https://ctan.org/pkg/cryptocode">cryptocode.sty</a>.
</div>


Formalizing cryptocurrencies is a gigantic SNAFU: there's a lot of parties interacting, and a lof of complexity (community-wise, network-wise, and cryptography-wise).
Mimblewimble is no exception, but (at least cryptographically speaking) it's based on a very elegant idea that relies solely on
simulation-extractable proofs of knowledge, and homomorphic commitment schemes.

<script>
    macros['\\R'] = '\\mathsf{R}';
    macros['\\cash'] = '\\mathsf{Cash}';
    macros['\\ledger'] = '\\Lambda';
    macros['\\vmax'] = 'v_{\\text{max}}';
    macros['\\coin'] = '\\mathsf{C}';
    macros['\\agg'] = '\\mathsf{A}';
    macros['\\tx'] = '\\mathsf{tx}';
    macros['\\trans'] = '\\mathsf{T}';
    macros['\\setup'] = '\\mathsf{S}';
    macros['\\itx'] = '\\textsf{in}';
    macros['\\otx'] = '\\textsf{out}';
    macros['\\mw'] = '\\textsf{MW}';
    macros['\\cp'] = '\\textsf{cp}';
    macros['\\grgen'] = '\\textsf{GrGen}';
    macros['\\com'] = '\\textsf{Com}';
    macros['\\rp'] = '\\mathsf{\\Pi}';
    macros['\\ep'] = '\\mathsf{\\Sigma}';
    macros['\\commit'] = '\\textsf{C}';
    macros['\\prover'] = '\\textsf{P}';
    macros['\\verifier'] = '\\textsf{V}';
    macros['\\crs'] = '\\textsf{crs}';
</script>


<div class="definition">
An aggregate cash system $\cash$ is composed of algorithms of the following algorithms:
- $\pp, \ledger \gets \cash.\setup(\secparam, \vmax)$, a setup algorithm that, given as input the security parameter in unary form and the maximal value for a coin $\vmax$, outputs some public parameters $\pp$ and an empty ledger $\ledger$;
- $C, k \gets \cash.\coin(\pp, v)$, a minting algorithm that, given as input some value $v$, creates a oin with that value (along with the key associated to it);
- $\tx \gets \cash.\trans(\pp, [(C_{i}, v_i, k_i)], [(C_j\', v_j\', k_j\')])$, a transaction algorithm, that given as input the input coins (along with the respective values and keys) and the output coins (along with the respective values and keys), outputs a new transaction $\tx$.

Additionally, we have the aggregation procedures:
- $\bottom$ or $\tx \gets \cash.\agg(\pp, \tx_0, \tx_1)$, a transaction aggregation algorithm, that merges together two transactions;
 - $\bottom$ or $\ledger\' \gets \cash.\agg(\pp, \tx, \ledger)$, which merges a transaction with the ledger.

Finally, we have the verification procedures:
 - $\bool \gets \cash.\verify(\pp, C, v, k)$, a coin verification function;
 - $\bool \gets \cash.\verify(\pp, \tx)$, a transaction verification function;
 - $\bool \gets \cash.\verify(\pp, \ledger)$, a ledger verification function.
</div>

There are a couple things that we can already note: first, there are no accounts. Rather, to each coin there is an associated "secret key".
Whoever owns the secret key has the ability to spend that coin.
Secondly, the system must be aware of the maximal value that a coin (or a transaction) can spend. This will be necessary _for proving security_ as at some
point -- intuitivey, to say that it's not possible to wrap around the modular representation in polynomial time (i.e., $p / \vmax > \poly(\secpar)$).



Pragmatically, to send money, the user would make a new coins $\vec C^*$ for the amount to be sent. Then, take a
set of coins already available in the ledger that add up _at least_ for the value to be sent.
Then, make a set of new coins for the change, and create a transaction
$\tx$ with input the coins in the ledger (available for spending),
with output the payment coins, plus the change coins.
Send $\tx$, along with $v, \key$ and the openings for the $\vec C^\*$.
On the other side, the user, to receieve coins would make a new transactions where the coins $\vec C^\*$
appear as inputs, and as outputs some new coins $\vec C''$ that add up for the exchanged value.
Then, it would aggregare the two transactions.



Defining correctness for this scheme is really annoying. Intuitively, we just want to enforce that the procedures of $\cash$ behave "as expected", but there's a lot of functions to test. Let's see a bunch of them:
- **correctness of setup**. Informally, the setup should produce a ledger that is valid; in other words, for any $\secpar \in \NN$ and any $\vmax \in \NN$:

$$
\Pr \left[
(\pp, \ledger) \gets \cash.\setup(\secparam, \vmax) : \cash.\verify(\pp, \ledger)
\right] = 1- \negl(\secpar).
$$

- **correctness of aggregation with ledger**. Informally, a transaction should be aggregated to the ledger only if it is valid and spends some coins in the ledger's list.

$$
\Pr \left[
\begin{array}{l}
\pp, \ledger \gets \cash.\setup(\secparam, \vmax)  \\
\ledger, \tx \gets \adv_{\text{Agg}}(\pp, \ledger) \\
\ledger' \gets \cash.\agg(\pp, \ledger, \tx)
\end{array}
:
\begin{array}{c}
\cash.\verify(\pp, \ledger) ~\land~
\cash.\verify(\pp, \tx) ~\land~
\tx.\itx \subset \ledger.\otx ~\land~
\tx.\otx \cap \ledger.\otx = [~]
 \implies \\
 \cash.\verify(\pp, \ledger') ~\land~
 \ledger'.\otx
\end{array}
\right] = 1 - \negl(\secpar)
$$

you got the idea.

Defining security is a bit more fun, and Yannick, Georg, and myself we proved that there are three basic
properties that Mimblewimble satisfies:

<div class="definition"> A cash system $\cash$ is transaction indistinguishable if, for all $\adv \in \ppt$:

$$
\advantage{ind-tx}{\cash, \adv} \defeq \abs{
\prob{\specgame{0}{IND-TX}{\cash, \adv}{\secpar, \vmax} =1}-
\prob{\specgame{1}{IND-TX}{\cash, \adv}{\secpar, \vmax} = 1}
} = \negl(\secpar),
$$

where the game $\specgame{b}{IND-TX}{\cash, \adv}{\secpar, \vmax}}
</div>

So here's Mimblewimble!
We need two proof systems: one is a range proof that a commitment $C$ is to some value $0 \leq v < \vmax$:

$$
\R(\rp, \vmax) \defeq \set{(C, (v, r)): C = \com.\commit(v, r)
\text{ and } 0 \leq v < \vmax},
$$

and one that is a proof of knowledge of the opening of a commitment to $0$:

$$
\R(\ep) \defeq \set{(C, r): C = \com.\commit(0, r)}.
$$




<div class="split">
<div class="minipage">
{:.pseudocode}
    Procedure $\mw.\setup(\secparam, \vmax)$:

    $\Gamma \gets \grgen(\secparam)$
    $\pp \defeq (\mw.\setup(\Gamma), \com.\setup(\Gamma), \Pi.\setup(\Gamma), \Sigma.\setup(\Gamma))$
    $\ledger \defeq ([], [], 0, [])$
    return $(\pp, \ledger)$
</div>
<div class="minipage">
{:.pseudocode}
    Procedure $\mw.\coin(\pp, v)$:

    $(C, k)\gets \com.\commit(cp, v)$
    return $(C, k)$
</div>
<div class="minipage">
{:.pseudocode}
    Procedure $\mw.\trans(\pp, [k, C, \pi], [k', C' \pi'])$:

    $s \defeq \sum_i v' - \sum_j v$
    return $\bottom$ if $s < 0$
    return $\bottom$ if any $v_i, v_i' \not\in [0, \vmax]$
    $E \defeq \sum_i C_i' - \sum_j C_j - \com.\commit(s, 0)$
    $\sigma \gets \ep.\prover(\crs, E, \sum_j k' - \sum_i k )$
    $\vec \pi \gets \rp.\prover(\crs, \vec C', \vec v', \vec k')$
    return $(s, [\vec C], [\vec C'], [\vec \pi, E, \sigma]])$
</div>
</div>
<div class="split">
<div class="minipage">
{:.pseudocode}
    Procedure $\mw.\agg(\pp, \tx_0, \tx_1)$:

    check for cut-trough conditions
    $\bar C, \bar C' \defeq $ be the cut-trough of $\tx_0, \tx_1$
    $\bar \pi' \defeq $ the proofs from $C'$
    $K \defeq [\pi', E_0  \concat E_1, \sigma_0 + \sigma_1] $
    return $(s, [\bar C], [\bar C'], k)$
</div>
<div class="minipage">
{:.pseudocode}
    Procedure $\mw.\verify(\pp, C)$:

    return $(C = \com.\commit(\pp, v; k))$
</div>
<div class="minipage">
{:.pseudocode}
    Procedure $\mw.\verify(\pp, \tx)$:

    verify all rangeproofs $\vec \pi$
    verify $\sigma$ with excesses $E_i$'s
    check that no inputs are outputs
    check no repetition
    ceck $s \geq 0$
</div>
