# An Introduction to Post-Quantum Cryptography for SaaS Providers

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

The primary reason is that PQC risks are not a risk of the future, they are a risk of <u>today</u>.
Attackers can perform [Harvest Now, Decrypt Later (HNDL)](https://en.wikipedia.org/wiki/Harvest_now,_decrypt_later) attacks, where they can capture encrypted data today and store it until a quantum computer is available to decrypt it. If the data in question is sensitive and has a long shelf life, it is important to consider the potential risks of quantum computing and PQC.

A second reason why PQC is important is that improved cryptanalysis are being published continuously (more on this later), meaning cryptographic algorithms deemed safe today may not be in the future.
This applies both to classical and post-quantum cryptography, but the latter is still in its infancy, so it is important to keep an eye on the latest developments in the field.
Ensuring the cryptographic algorithms and their associated keys are quantum-safe could require a full-on migration.

Lastly, compliance and regulatory requirements may leave us with little choice but to adopt PQC.
In [executive order 14412](https://www.whitehouse.gov/presidential-actions/2026/06/securing-the-nation-against-advanced-cryptographic-attacks/), the White House has mandated that all high impact systems use PQC for key establishment by December 31, 2030.
In similar fashion, France's cybersecurity agency ANSSI said on Tuesday it would stop certifying security products that lack quantum-resistant encryption, a move that will force government bodies and critical operators to shift away from older systems (see references at the end).

## What does the academic and industry say about Post-Quantum Cryptography?

## How are we SaaS providers affected by Post-Quantum Cryptography?

## What's next for Post-Quantum Cryptography in the SaaS industry?

## References

- [https://en.wikipedia.org/wiki/Cryptography](https://en.wikipedia.org/wiki/Cryptography)
- [https://en.wikipedia.org/wiki/Symmetric-key_algorithm](https://en.wikipedia.org/wiki/Symmetric-key_algorithm)
- [https://en.wikipedia.org/wiki/Public-key_cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography)
- [https://en.wikipedia.org/wiki/RSA_cryptosystem](https://en.wikipedia.org/wiki/RSA_cryptosystem)
- [https://en.wikipedia.org/wiki/Elliptic-curve_cryptography](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography)
- [https://en.wikipedia.org/wiki/Advanced_Encryption_Standard](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [https://en.wikipedia.org/wiki/Lattice-based_cryptography](https://en.wikipedia.org/wiki/Lattice-based_cryptography)
- [https://en.wikipedia.org/wiki/Quantum_computing](https://en.wikipedia.org/wiki/Quantum_computing)
- [https://en.wikipedia.org/wiki/Quantum_algorithm](https://en.wikipedia.org/wiki/Quantum_algorithm)
- [https://en.wikipedia.org/wiki/Shor%27s_algorithm](https://en.wikipedia.org/wiki/Shor%27s_algorithm)
- [https://en.wikipedia.org/wiki/Grover%27s_algorithm](https://en.wikipedia.org/wiki/Grover%27s_algorithm)
- [https://en.wikipedia.org/wiki/Post-quantum_cryptography](https://en.wikipedia.org/wiki/Post-quantum_cryptography)
- [https://en.wikipedia.org/wiki/Harvest_now,_decrypt_later](https://en.wikipedia.org/wiki/Harvest_now,_decrypt_later)
- [https://www.whitehouse.gov/presidential-actions/2026/06/securing-the-nation-against-advanced-cryptographic-attacks/](https://www.whitehouse.gov/presidential-actions/2026/06/securing-the-nation-against-advanced-cryptographic-attacks/)
- [https://www.reuters.com/legal/litigation/france-stop-certifying-products-without-quantum-safe-encryption-2026-06-16/](https://www.reuters.com/legal/litigation/france-stop-certifying-products-without-quantum-safe-encryption-2026-06-16/)
