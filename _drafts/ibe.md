---
layout: post
title: "Identity Based Encryption"
date: 2018-05-01 00:00:00 +0100
categories: cryptography
---

This is a quick walkthrough identity-based encryption, over finite groups [[BF03]] and over lattices [[ABB11]].
<div class="definition" text="IBE">
An Identity-Based Encryption scheme $\ibe$ is composed of the following $\ppt$ algorithms:
* $(\msk, \pp) \gets \ibe.\S(\secparam)$, a setup algorithm that generates the master secret key $\msk$ along with some public information $\pp$;
* $\ct \gets \ibe.\E(\pp, \id, m)$, an encryption algorithm that takes as input the identity of the recipient, and a message. It outputs a cipertext;
* $\sk_\id  \gets \ibe.\X(\pp, \msk)$, an extraction algorithm that generates the secret key for a particular identity $\id$;
* $m^* \gets \ibe.\D(\pp, \ct, \sk_\id)$, the decryption algorithm.
</div>

A Hierarchical IBE, is an IBE scheme that additionally contains a "derive" algorithm:
* $\sk_{\id \concat \id_\ell} \gets \ibe.\mathsf{R}(\pp, (\id \concat \id_\ell), \sk_\id)$,
an algorithm that allows to derive a leaf identity from a parent identity.

An $(\mathsf{H})\ibe$ scheme must be _correct_; the security notions we can give are pretty similar to the standard security notions of encryption schemes. Here we go for real-or-random ($\implies$ semantic security).

<div class="split">
<div class="minipage">
{:.pseudocode}
	$\text{Game }\textsf{IND-sID-CPA}(\secpar)$
	
	$(\id^*) \gets \adv(\secparam)$
	$(\msk, \pp) \gets \ibe.\S(\secparam)$
	$m \gets \adv^{ \textsf{Extract} }(\pp, \id^*)$
	$b \sample \bin$
	$\sk_{id^{*}} \gets \ibe.\X(\msk, \id^*)$
	if $b = 0$: $\ct \sample \mathcal{C}_{\ibe}$
	if $b = 1$: $\ct \gets \ibe.\E(\sk_{\id^{*}}, m)$
	$b' \gets \adv^{\textsf{Extract}}(\pp, \id^*, m, \ct)$
	return $b = b'$
</div>
<br>
<div class="minipage">
{:.pseudocode}
	$\text{Oracle } \textsf{Extract}(\id)$
	
	if $\id =\id^*$:
	  return $\bottom$
	else: return $\ibe.\X(\msk, \id)$
</div>
</div>

_Nota bene_: in the case of HIBE, we replace equality in the oracle $
\textsf{Extract}$ with a check for prefix.
We present here the 


[ABB11]: <http://crypto.stanford.edu/~dabo/papers/latticebb-proc.pdf>
[BF03]: <https://crypto.stanford.edu/~dabo/papers/bfibe.pdf>
