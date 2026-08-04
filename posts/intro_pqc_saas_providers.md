# An Introduction to Post-Quantum Cryptography for SaaS Providers

> **Last updated:** August 4, 2026

This post is an introduction to Post-Quantum Cryptography (PQC) for Software as a Service (SaaS) providers.
We will address the following questions
- What is Post-Quantum Cryptography?
- Why should we care about Post-Quantum Cryptography?
- What does the academic and industry say about Post-Quantum Cryptography?
- How are we SaaS providers affected by Post-Quantum Cryptography?
- What's next for Post-Quantum Cryptography in the SaaS industry?

## What is Post-Quantum Cryptography?

### A recap of classical cryptography

Before we get into PQC, it's good to iterate the concepts of classical cryptography.

[Cryptography](https://en.wikipedia.org/wiki/Cryptography), or cryptology, is the practice and study of techniques for secure communication in the presence of adversarial behavior.

There are two main types of cryptography: [symmetric-key cryptography](https://en.wikipedia.org/wiki/Symmetric-key_algorithm) and [asymmetric-key cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography), the latter also known as public key cryptography.

In symmetric-key cryptography, the same key is used for both encryption and decryption, while in public-key cryptography, a pair of keys is used: a public key / private key pair for encryption and decryption operations.

Two standard asymmetric cryptographic algorithms are [Rivest–Shamir–Adleman (RSA)](https://en.wikipedia.org/wiki/RSA_cryptosystem) and [Elliptic Curve Cryptography (ECC)](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography).
RSA is based on the difficulty of factoring semiprimes `N = p * q` with `p` and `q` large primes. ECC relies on the hardness of elliptic curve discrete logarithm problem, which is based on the ability to compute point multiplication on elliptic curves efficiently and the apparent inability to reverse this operation for properly chosen curves and key sizes.

A standard symmetric cryptographic algorithm is the [Advanced Encryption Standard (AES)](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), a symmetric block cipher that encrypts and decrypts data in blocks of 128 bits using keys of 128, 192, or 256 bits.

A different type of cryptography is [lattice-based cryptography](https://en.wikipedia.org/wiki/Lattice-based_cryptography), which is a form of cryptography that involve lattices, which are integer linear combinations of a basis of a vector space.
This type of cryptography is used in the creation of the first 3 post-quantum cryptographic standards (more on this later), and has a lot of interesting properties. One of these properties is it's security relying on worst-case hardness of certain lattice problems. Another is the ability to use lattice-based cryptography within homomorphic encryption schemes, which allows computations to be performed on ciphertext data without needing to decrypt it first.

### What is Quantum Computing?

I am not a physicist, so I'll rely on other sources to explain what quantum computing is.

[Quantum computing](https://en.wikipedia.org/wiki/Quantum_computing) is the use of quantum-mechanical phenomena such as superposition and entanglement to perform computation.
The basic unit of information in quantum computing, the qubit (quantum bit), serves a similar function as the bit in ordinary or "classical" computing. Unlike a classical bit, which can be in one of two states (a binary), a qubit can exist in a linear combination of states known as a quantum superposition.

[Quantum algorithms](https://en.wikipedia.org/wiki/Quantum_algorithm) are algorithms that run on a realistic model of quantum computation, the most widely used being the quantum circuit model in which a computation is a sequence of quantum gates, which are reversible transformations on a quantum mechanical analog of an n-bit register.

There are two quantum algorithms that are highly relevant to cryptography: [Shor's algorithm](https://en.wikipedia.org/wiki/Shor%27s_algorithm) and [Grover's algorithm](https://en.wikipedia.org/wiki/Grover%27s_algorithm).
- Shor's algorithm is a quantum algorithm for finding the prime factors of an integer, running in polynomial time of `log(N)`, where `N` is the integer to be factored. This algorithm can be used to break RSA and ECC.
- Grover's algorithm is a quantum algorithm for unstructured search that finds with high probability the unique input to a black box function that produces a particular output value, using just `O(√N)` evaluations of the function, where `N` is the size of the function's domain.

Now, wrapping up. [Post-quantum cryptography (PQC)](https://en.wikipedia.org/wiki/Post-quantum_cryptography) is the development of cryptographic algorithms that are thought to be secure against a cryptanalytic attack by a quantum computer.

## Why should we care about Post-Quantum Cryptography?

We provide multiple reasons why SaaS providers should care about PQC.

The primary reason is that PQC risks are not a risk of the future, they are a risk of **today**.
Attackers can perform [Harvest Now, Decrypt Later (HNDL)](https://en.wikipedia.org/wiki/Harvest_now,_decrypt_later) attacks, where they can capture encrypted data today and store it until a quantum computer is available to decrypt it. If the data in question is sensitive and has a long shelf life, it is important to consider the potential risks of quantum computing and PQC.

A second reason why PQC is important is that improved cryptanalysis are being published continuously (more on this later), meaning cryptographic algorithms deemed safe today may not be in the future.
This applies both to classical and post-quantum cryptography, but the latter is still in its infancy, so it is important to keep an eye on the latest developments in the field.
Ensuring the cryptographic algorithms and their associated keys are quantum-safe could require a full-on migration.

Lastly, compliance and regulatory requirements may leave us with little choice but to adopt PQC.
In [executive order 14412](https://www.whitehouse.gov/presidential-actions/2026/06/securing-the-nation-against-advanced-cryptographic-attacks/), the White House has mandated that all high impact systems use PQC for key establishment by December 31, 2030.
In similar fashion, France's cybersecurity agency ANSSI said on Tuesday it would stop certifying security products that lack quantum-resistant encryption, a move that will force government bodies and critical operators to shift away from older systems (see references at the end).

## What does the academic and industry say about Post-Quantum Cryptography?

Without providing an in-depth literature review, we will just point out that Google and Cloudflare are two major players in that have already starting engaging with PQC.

Google’s [white paper](https://research.google/blog/safeguarding-cryptocurrency-by-disclosing-quantum-vulnerabilities-responsibly/) published recently on March 31, 2026, shows massive improvements in quantum cryptanalysis, with 20x reduction in the number of physical qubits required to solve ECDLP-256 (i.e. 256-bit elliptic curve discrete logarithm problem).

Cloudflare has excellent technical documentation for industry professionals on PQC, and are running active experiments like one on [the latest post-quantum signature standardization candidates](https://blog.cloudflare.com/another-look-at-pq-signatures/).
Additionally, they have been a frontrunner on adopting these new standards, reporting about 50% of their TLS traffic is now using post-quantum cryptography.
This is done through enabling `X25519MLKEM768` over TLS 1.3 `AES-128-GCM` for their edge gateways, the details of which i'll explain later in this post.

One of the takeaways from Cloudflare post-quantum standardization support is that the availability of these encryption algorithms is <u>already here</u>.
In August 13, 2024, NIST [announced](https://www.nist.gov/news-events/news/2024/08/nist-releases-first-3-finalized-post-quantum-encryption-standards) the first 3 post-quantum cryptography standards, which are:
- FIPS 203, intended as the primary standard for general encryption. Among its advantages are comparatively small encryption keys that two parties can exchange easily, as well as its speed of operation. The standard is based on the CRYSTALS-Kyber algorithm, which has been renamed ML-KEM, short for Module-Lattice-Based Key-Encapsulation Mechanism.
- FIPS 204, intended as the primary standard for protecting digital signatures. The standard uses the CRYSTALS-Dilithium algorithm, which has been renamed ML-DSA, short for Module-Lattice-Based Digital Signature Algorithm.
- FIPS 205, also designed for digital signatures. The standard employs the Sphincs+ algorithm, which has been renamed SLH-DSA, short for Stateless Hash-Based Digital Signature Algorithm. The standard is based on a different math approach than ML-DSA, and it is intended as a backup method in case ML-DSA proves vulnerable.

As you saw above, ML-KEM is already used in TLS 1.3 key exchanges for Cloudflare edge gateways, and other software is catching up also, like go 1.27, which has added [support](https://go.dev/doc/go1.27#crypto_mldsa) for ML-KEM and ML-DSA in its standard library.

### How is Symmetric-key cryptography affected by Post-Quantum Cryptography?

As mentioned earlier, Grover's algorithm can be used to perform unstructured search in `O(√N)` evaluations of the function, where `N` is the size of the function's domain.
Now for breaking a symmetric-key cryptography algorithm, the function's domain is the key space, which is `2^k` for a `k`-bit key.
Running the algorithm gives us a time complexity of `O(√(2^k)) = O(2^(k/2))`, which is a quadratic speedup over classical brute-force attacks.
This cryptanalysis improvement can be overcome by doubling the key size, which is a simple and effective mitigation strategy.

In fact, Cloudflare researchers reported that even though doubling the symmetric key size will mitigate the theoretical risk completely, there are no [practical benefits](https://blog.cloudflare.com/pq-2025/#already-post-quantum-secure-symmetric-cryptography) to doing so. They argue that asymmetric cryptographic algorithms are the ones that are most at risk from quantum computing, and that doubling the key size of symmetric algorithms is not the most productive use of resources.

We therefore present the following result: **Symmetric encryption algorithms are not affected by quantum computing**.

### How is Asymmetric-key cryptography affected by Post-Quantum Cryptography?

## How are we SaaS providers affected by Post-Quantum Cryptography?

## What's next for Post-Quantum Cryptography in the SaaS industry?

## References

1. [https://en.wikipedia.org/wiki/Cryptography](https://en.wikipedia.org/wiki/Cryptography)
2. [https://en.wikipedia.org/wiki/Symmetric-key_algorithm](https://en.wikipedia.org/wiki/Symmetric-key_algorithm)
3. [https://en.wikipedia.org/wiki/Public-key_cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography)
4. [https://en.wikipedia.org/wiki/RSA_cryptosystem](https://en.wikipedia.org/wiki/RSA_cryptosystem)
5. [https://en.wikipedia.org/wiki/Elliptic-curve_cryptography](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography)
6. [https://en.wikipedia.org/wiki/Advanced_Encryption_Standard](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
7. [https://en.wikipedia.org/wiki/Lattice-based_cryptography](https://en.wikipedia.org/wiki/Lattice-based_cryptography)
8. [https://en.wikipedia.org/wiki/Quantum_computing](https://en.wikipedia.org/wiki/Quantum_computing)
9. [https://en.wikipedia.org/wiki/Quantum_algorithm](https://en.wikipedia.org/wiki/Quantum_algorithm)
10. [https://en.wikipedia.org/wiki/Shor%27s_algorithm](https://en.wikipedia.org/wiki/Shor%27s_algorithm)
11. [https://en.wikipedia.org/wiki/Grover%27s_algorithm](https://en.wikipedia.org/wiki/Grover%27s_algorithm)
12. [https://en.wikipedia.org/wiki/Post-quantum_cryptography](https://en.wikipedia.org/wiki/Post-quantum_cryptography)
13. [https://en.wikipedia.org/wiki/Harvest_now,_decrypt_later](https://en.wikipedia.org/wiki/Harvest_now,_decrypt_later)
14. [https://www.whitehouse.gov/presidential-actions/2026/06/securing-the-nation-against-advanced-cryptographic-attacks/](https://www.whitehouse.gov/presidential-actions/2026/06/securing-the-nation-against-advanced-cryptographic-attacks/)
15. [https://www.reuters.com/legal/litigation/france-stop-certifying-products-without-quantum-safe-encryption-2026-06-16/](https://www.reuters.com/legal/litigation/france-stop-certifying-products-without-quantum-safe-encryption-2026-06-16/)
16. [https://research.google/blog/safeguarding-cryptocurrency-by-disclosing-quantum-vulnerabilities-responsibly/](https://research.google/blog/safeguarding-cryptocurrency-by-disclosing-quantum-vulnerabilities-responsibly/)
17. [https://blog.cloudflare.com/another-look-at-pq-signatures/](https://blog.cloudflare.com/another-look-at-pq-signatures/)
18. [https://www.nist.gov/news-events/news/2024/08/nist-releases-first-3-finalized-post-quantum-encryption-standards](https://www.nist.gov/news-events/news/2024/08/nist-releases-first-3-finalized-post-quantum-encryption-standards)
19. [https://go.dev/doc/go1.27#crypto_mldsa](https://go.dev/doc/go1.27#crypto_mldsa)
20. [https://blog.cloudflare.com/pq-2025/#already-post-quantum-secure-symmetric-cryptography](https://blog.cloudflare.com/pq-2025/#already-post-quantum-secure-symmetric-cryptography)
