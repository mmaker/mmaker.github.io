---
layout: page
title: Code
permalink: /code.html
---

>  Someone has to write software to defend privacy, and since we can't get privacy unless we all do,
> we're going to write it.

<cite>-- A cypherpunk manifesto</cite>

A more complete list of contributions and software is available on [Github](https://github.com/mmaker).
Project listed here are for the most part proofs of concepts and paired with a research paper:
they are not maintained and are **not** meant for deployment.

### Anonymous Tokens

Anonymous Tokens are lightweight, single-use anonymous credentials.
They were introduced by Davidson et al. ([PETS 2018](https://www.petsymposium.org/2018/files/papers/issue3/popets-2018-0026.pdf)).
The library contains both Privacy Pass as well as the extensions proposed in
[[KLOR20]](https://eprint.iacr.org/2020/072) and
[[FLOR20]](https://eprint.iacr.org/2022/004).

### Mangiafuoco

 A post-quantum designated-verifier lattice-based zk-SNARKS, used for the benchmarks reported in [[GMNO18]](https://eprint.iacr.org/2018/275).

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

### Oblivious transfer

A pure-Rust implementation of oblivious transfer and oblivious transfer extensions.

### Homomorphic Secret Sharing

A a blazingly-fast implementation of the group-based Homomorphic Secret Sharing Scheme of Boyle et al. [[BCIG17]](https://eprint.iacr.org/2018/419).

### ocaml-letsencrypt

An implementation of the ACME protocol (mostly client
side) purely in OCaml based on [RFC 8555](https://tools.ietf.org/html/rfc8555).
The HTTP, DNS, and [ALPN](https://tools.ietf.org/html/draft-ietf-acme-tls-alpn-07)
challenges are implemented.
