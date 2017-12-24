---
layout: page
title: Homomorphic Secret Sharing
permalink: /hss/
---

This is the homepage for the implementation of _homomorphic secret sharing_.

A (2-party) HSS scheme splits an input $$x$$ into shares $$(x_0, x_1)$$
such  that each share  computationally hides  $$x$$, and there
exists an efficient homomorphic evaluation algorithm $$\mathsf{Eval}$$ such that
for any function (or "program") $$P$$ from a given class it holds that
$\mathsf{Eval}(x_0,P)+\mathsf{Eval}(x_1,P)=P(x)$.

HSS schemes were introduced by Boyle et al. ([Crypto 2016](https://eprint.iacr.org/2016/585),
[Eurocrypt 2017](https://eprint.iacr.org/2017/150),
[CCS 2017](https://acmccs.github.io/papers/p2105-boyleA.pdf)).
Here we implement both the naïve algorithms as well as the optimized version proposed in CCS'17.
[Extended benchmarks](benchmarks/) as well as
[performance graphs](benchmarks/Intel.Core.i7-3537U/summary.html) are available.

Looking for the implementation of a particular component?
* Optimized group operations for pseudo-mersenne primes are in
[`src/group.c`](https://git.tumbolandia.net/maker/hss/src/master/src/group.c).
* The so-called _share conversion_ or _distributed discrete-log_ is available at
[`src/ddlog.c:160`](https://git.tumbolandia.net/maker/hss/src/master/src/ddlog.c#L160).
The optimized version of (Eurocrypt'17, §6.3) is defined in
[`src/ddlog.c:172`](https://git.tumbolandia.net/maker/hss/src/master/src/ddlog.c#L172).
(Note: the window has been increased to 64-bit.)
The improvements described in (CCS'17, §4.1 and §6.1) are available in the same
file; see
[`src/ddlog.c:55`](https://git.tumbolandia.net/maker/hss/src/master/src/ddlog.c#L55).
* The _RMS program multiplication_ is implemented in [`src/rms.c`](https://git.tumbolandia.net/maker/hss/src/master/src/rms.c).
* The _homomorphic secret sharing scheme_  for RMS programs (CCS'17, §3.3) is implemented in
[`src/hss.c`](https://git.tumbolandia.net/maker/hss/src/master/src/hss.c)

The latest release is available for [download](download/hss-0.1.tar.xz)
([.sig](download/hss-0.1.tar.xz.asc)).
The package is not copyrighted, and released into the public domain.

For bug reports and comments concerning the specific implementation, please use my email
address; for any other question or comment please refer to the paper authors as a whole.



<br />
<br />
<br />
<br />
##### Aknowledgements

<font size="1">
Elette Boyle was supported by ISF grant 1861/16, AFOSR Award FA9550-17-1-0069, and ERC grant 307952.
Geoffroy Couteau was supported by ERC grant 339563 (project CryptoCloud).
Yuval Ishai was supported by ISF grant 1638/15, a grant by the BGU Cyber Center, and by the European Union's Horizon 2020 ICT program (Mikelangelo project).
Niv Gilboa was supported by a DARPA/ARL SAFEWARE award, DARPA Brandeis program under Contract N66001-15-C-4065, NSF Frontier Award 1413955, NSF grants 1619348, 1228984, 1136174, and 1065276, ERC grant 742754, NSF-BSF grant 2015782, ISF grant 1709/14, BSF grant 2012378, a Xerox Faculty Research Award, a Google Faculty Research Award, an equipment grant from Intel, and an Okawa Foundation Research Grant.
This material is based upon work supported by the Defense Advanced Research Projects Agency through the ARL under Contract W911NF-15-C-0205.
Michele Orrù was supported by ERC grant 639554 (project aSCEND).
</font>
