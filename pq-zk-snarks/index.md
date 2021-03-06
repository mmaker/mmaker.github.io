---
layout: page
title: Post-quantum zk-SNARKs
permalink: /pq-zk-snarks/
---

This is the homepage for _Mangiafuoco_, the implementation of a
designated-verifier, _LWE-based zk-SNARK_ from Square Span Programs [[GMNO18]](https://eprint.iacr.org/2018/275).

A zk-SNARK is a non-interactive, succinct, zero-knowledge argument of knowledge.
It allows to prove knowledge of a witness for any $\mathsf{NP}$ statement, without
revealing any information about the witness itself. The proof size is
constant in the security parameter.
Square Span Programs are an efficient characterization of the complexity class
$\mathsf{NP}$ for boolean circuits. They have been introduced by Danezis et al.
[[DFGK14]](https://pdfs.semanticscholar.org/b0f0/a5bd5fa074d1720fb23c47d67b539e4c4591.pdf).
are currently implemented in libraries such as `libsnark`.

SNARKs are nowadays used for delegating computation, electronic
cryptocurrencies, and anonymous credentials.
Yet, all current implementations are based on so-called pre-quantum assumptions
and, for this reason, are not expected to withstand cryptanalitic efforts over
the next few decades.
Here we implement an LWE scheme and benchmark the computations of prover and
verifier.

The [source code](download/mangiafuoco-0.2.tar.gz)
([.sig](download/mangiafuoco-0.2.tar.gz.asc)) is now available for download.
The package is not copyrighted, and released to the public domain.
For bug reports and comments please use my email address.

<br />
<br />
<br />
<br />
##### Acknowledgments

<font size="1">
 Rosario Gennaro was supported by NSF Award no. 1565403. Michele Minelli was
supported by European Union’s Horizon 2020 research and innovation programme under grant
agreement no. H2020-MSCA-ITN-2014-643161 ECRYPT-NET. Anca Nitulescu was supported by
the European Research Council under the European Community’s Seventh Framework Programme
(FP7/2007-2013 Grant Agreement no. 339563 – CryptoCloud). Michele Orrù was supported by
ERC grant 639554 (project aSCEND).
</font>
