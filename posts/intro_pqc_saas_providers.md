# An Introduction to Post-Quantum Cryptography for SaaS Providers

> **Last updated:** August 5, 2026

This post is an introduction to Post-Quantum Cryptography (PQC) for Software as a Service (SaaS) providers.
We will address the following questions:
- What is Post-Quantum Cryptography?
- Why should we care about Post-Quantum Cryptography?
- What does academic and industry literature say about Post-Quantum Cryptography?
- How are we SaaS providers affected by Post-Quantum Cryptography?
- What's next for Post-Quantum Cryptography in the SaaS industry?

## What is Post-Quantum Cryptography?

### A recap of classical cryptography

Before we get into PQC, it's good to reiterate the concepts of classical cryptography.

[Cryptography](https://en.wikipedia.org/wiki/Cryptography), or cryptology, is the practice and study of techniques for secure communication in the presence of adversarial behavior.

There are two main types of cryptography: [symmetric-key cryptography](https://en.wikipedia.org/wiki/Symmetric-key_algorithm) and [asymmetric-key cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography), the latter also known as public key cryptography.

In symmetric-key cryptography, the same key is used for both encryption and decryption, while in public-key cryptography, a pair of keys is used: a public key / private key pair for encryption and decryption operations.

Two standard asymmetric cryptographic algorithms are [Rivest–Shamir–Adleman (RSA)](https://en.wikipedia.org/wiki/RSA_cryptosystem) and [Elliptic Curve Cryptography (ECC)](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography).
RSA is based on the difficulty of factoring semiprimes `N = p * q` with `p` and `q` large primes. ECC relies on the hardness of elliptic curve discrete logarithm problem, which is based on the ability to compute point multiplication on elliptic curves efficiently and the apparent inability to reverse this operation for properly chosen curves and key sizes.

A standard symmetric cryptographic algorithm is the [Advanced Encryption Standard (AES)](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), a symmetric block cipher that encrypts and decrypts data in blocks of 128 bits using keys of 128, 192, or 256 bits.

A different type of cryptography is [lattice-based cryptography](https://en.wikipedia.org/wiki/Lattice-based_cryptography), which is a form of cryptography that involves lattices, which are integer linear combinations of a basis of a vector space.
This type of cryptography is used in the creation of the first 3 post-quantum cryptographic standards (more on this later), and has a lot of interesting properties. One of these properties is its security relying on worst-case hardness of certain lattice problems. Another is the ability to use lattice-based cryptography within homomorphic encryption schemes, which allows computations to be performed on ciphertext data without needing to decrypt it first.

### What is Quantum Computing?

We'll rely on other sources to explain what quantum computing is.

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

A second reason why PQC is important is that improved cryptanalysis is being published continuously (more on this later), meaning cryptographic algorithms deemed safe today may not be in the future.
This applies both to classical and post-quantum cryptography, but the latter is still in its infancy, so it is important to keep an eye on the latest developments in the field.
Ensuring the cryptographic algorithms and their associated keys are quantum-safe could require a full-on migration of these components, so we'll have to plan for this migration in advance.

Lastly, compliance and regulatory requirements may leave us with little choice but to adopt PQC.
In [executive order 14412](https://www.whitehouse.gov/presidential-actions/2026/06/securing-the-nation-against-advanced-cryptographic-attacks/), the White House has mandated that all high impact systems use PQC for key establishment by December 31, 2030.
In similar fashion, France's cybersecurity agency ANSSI announced it would stop certifying security products that lack quantum-resistant encryption, a move that will force government bodies and critical operators to shift away from older systems (see references at the end).

## What does academic and industry literature say about Post-Quantum Cryptography?

### Industry leaders and standards

Without providing an in-depth literature review, we will just point out that Google and Cloudflare are two major players that have already started engaging with PQC.

Google’s [white paper](https://research.google/blog/safeguarding-cryptocurrency-by-disclosing-quantum-vulnerabilities-responsibly/) published recently on March 31, 2026, shows massive improvements in quantum cryptanalysis, with 20x reduction in the number of physical qubits required to solve ECDLP-256 (i.e. 256-bit elliptic curve discrete logarithm problem).

Cloudflare has excellent technical documentation for industry professionals on PQC, and is running active experiments like one on [the latest post-quantum signature standardization candidates](https://blog.cloudflare.com/another-look-at-pq-signatures/).
Additionally, they have been a frontrunner on adopting these new standards, reporting about 50% of their TLS traffic is now using post-quantum cryptography.
This is done through enabling `X25519MLKEM768` over TLS 1.3 for their edge gateways, the details of which we'll explain later in this post.

One of the takeaways from Cloudflare post-quantum standardization support is that the availability of these encryption algorithms is **already here**.
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

We therefore present the following result: **Symmetric encryption algorithms are not heavily affected by quantum computing and are not a migration priority**.

### How is Asymmetric-key cryptography affected by Post-Quantum Cryptography?

Unlike symmetric encryption algorithms, asymmetric encryption algorithms are highly affected by quantum computing, as we saw with Shor's algorithm.
Shor's algorithm will break RSA and ECC. This means that wherever these encryption algorithms are used, whether it is in key exchanges or digital signatures, they will need to be migrated to post-quantum safe alternatives.
As highlighted earlier, NIST provides such alternatives in the form of ML-KEM, ML-DSA, and SLH-DSA.

We demo a quantum-safe key exchange for [https://cloudflare.com](https://cloudflare.com) from the terminal.

```shell
➜  ~ openssl s_client -connect cloudflare.com:443 -servername cloudflare.com -tls1_3 </dev/null 2>/dev/null
CONNECTED(00000005)
---
Certificate chain
 0 s:CN=cloudflare.com
   i:C=US, O=Google Trust Services, CN=WE1
   a:PKEY: EC, (prime256v1); sigalg: ecdsa-with-SHA256
   v:NotBefore: Jul  8 21:47:39 2026 GMT; NotAfter: Oct  6 22:47:27 2026 GMT
 1 s:C=US, O=Google Trust Services, CN=WE1
   i:C=US, O=Google Trust Services LLC, CN=GTS Root R4
   a:PKEY: EC, (prime256v1); sigalg: ecdsa-with-SHA384
   v:NotBefore: Dec 13 09:00:00 2023 GMT; NotAfter: Feb 20 14:00:00 2029 GMT
 2 s:C=US, O=Google Trust Services LLC, CN=GTS Root R4
   i:C=BE, O=GlobalSign nv-sa, OU=Root CA, CN=GlobalSign Root CA
   a:PKEY: EC, (secp384r1); sigalg: sha256WithRSAEncryption
   v:NotBefore: Nov 15 03:43:21 2023 GMT; NotAfter: Jan 28 00:00:42 2028 GMT
---
Server certificate
-----BEGIN CERTIFICATE-----
<snippet>
-----END CERTIFICATE-----
subject=CN=cloudflare.com
issuer=C=US, O=Google Trust Services, CN=WE1
---
No client certificate CA names sent
Peer signing digest: SHA256
Peer signature type: ecdsa_secp256r1_sha256
Negotiated TLS1.3 group: X25519MLKEM768
---
SSL handshake has read 3956 bytes and written 1552 bytes
Verification: OK
---
New, TLSv1.3, Cipher is TLS_AES_256_GCM_SHA384
Protocol: TLSv1.3
Server public key is 256 bit
This TLS version forbids renegotiation.
Compression: NONE
Expansion: NONE
No ALPN negotiated
Early data was not sent
Verify return code: 0 (ok)
---
```

As mentioned earlier, and as we can see from the `openssl` output, Cloudflare uses `X25519MLKEM768` over TLS 1.3.
The `AES-256-GCM` is the familiar bulk encryption cipher, and the `X25519MLKEM768` is a post-quantum key exchange algorithm that is based on the ML-KEM standard.

In fact, this last algorithm is what is known as a hybrid key exchange.
In simple terms, such exchange involves using two different key agreement algorithms simultaneously to establish a secure connection: one tried-and-true classical algorithm (in this example ECDH [X25519](https://en.wikipedia.org/wiki/Curve25519)) and the new PQC algorithm.

As explained very well in this [article](https://live.paloaltonetworks.com/t5/quantum-security-articles/the-quantum-countdown-how-hybrid-encryption-is-quietly/ta-p/1230276), the beauty of this hybrid approach is its resilience.
The resulting connection remains secure as long as at least one of the component algorithms remains unbroken. If an unforeseen flaw is found in ML-KEM, the classical algorithm X25519 still provides robust protection. Conversely, when quantum computers eventually break X22159, the PQC component will ensure your data remains secure. This method allows for the early adoption of quantum-resistant security while retaining the proven guarantees of classical cryptography.

### How is Web PKI affected by Post-Quantum Cryptography?

In the previous subsections we discussed how PQC affects symmetric and asymmetric cryptography, but we didn't discuss how it affects the Web PKI (Public Key Infrastructure).

This domain could arguably be the most affected by PQC, as web PKI is one of the trickiest places to deploy post-quantum signatures.
The reason is handshake size.
As Let's Encrypt explains in their vision on post-quantum future in [this post](https://letsencrypt.org/2026/06/03/pq-certs), ML-DSA-44, one of the smaller NIST standardized post-quantum signature schemes, has a signature roughly 2,420 bytes long.
The algorithms used in the Web PKI today are much smaller.
Public keys are bigger as well: 1,312 bytes for ML-DSA-44, 256 bytes for RSA-2048, and 64 bytes for ECDSA-P256.
A typical Web PKI handshake today carries five signatures and two public keys.
Replacing those with ML-DSA equivalents would push a single TLS handshake well past 10 kilobytes.
Cloudflare’s research has shown that, at that scale, a meaningful share of TLS connections fail on real-world networks, and the rest get slower.

Let's Encrypt will move to a new design known as ["Merkle Tree Certificates" (MTCs)](https://blog.cloudflare.com/bootstrap-mtc/).
Instead of issuing certificates one at a time and signing each one individually, an MTC certificate authority issues certificates in batches, with a single signature covering the entire batch. Browsers stay up to date on those batch signatures (called “landmarks”) separately from the TLS handshake.
In the common case, the entire authentication path in an MTC handshake is one signature, one public key, and one inclusion proof. That’s smaller than today’s Web PKI handshake, even though MTCs use post-quantum algorithms. The other case is the “standalone” form. It uses slightly larger handshakes as a fallback when a client’s landmark is out of date.

To conclude: this is a massive design change in the way certificates are issued and verified, and it is a necessary change to accommodate the larger sizes of post-quantum signatures.
Let's Encrypt is targeting late 2026 for a staging environment that issues MTCs, and 2027 for a production-ready environment.

Interestingly enough, other certificate authorities like ssl(.)com have made no such commitments as of time of writing.

## How are we SaaS providers affected by Post-Quantum Cryptography?

We can share the following conclusions for SaaS providers:
- Symmetric encryption algorithms are not heavily affected by quantum computing, so we can continue using them as before.
- Asymmetric encryption algorithms are highly affected by quantum computing, so we will need to migrate to post-quantum safe alternatives for key exchanges and digital signatures.
- For cloud-based SaaS providers, the cloud service provider used to host the SaaS product will be the biggest dependency for PQC adoption, as they will be the ones to provide the post-quantum safe alternatives for most key exchanges and digital signatures. The main cloud providers (Azure, AWS, Google) have already committed to timelines for post-quantum safety, with Microsoft [committing](https://www.microsoft.com/en-us/security/blog/2026/06/30/microsoft-advances-quantum-safe-security-as-the-risk-timeline-shifts/) to transition products and services to PQC by 2029.
- Microsoft provides a customer strategy for cryptographic posture management in [this blog](https://www.microsoft.com/en-us/security/blog/2026/04/16/building-your-cryptographic-inventory-a-customer-strategy-for-cryptographic-posture-management/). They introduce the concept of a "cryptographic inventory", which is a living catalog of all the cryptographic assets and mechanisms in use across your organization. This includes certificates and keys, protocols and cipher suites, cryptographic libraries and more. SaaS providers should start an initial inventory to get insights into critical gaps. Certificate authorities like ssl(.)com not having committed to a PQC timeline would be an example of such a gap.

## What's next for Post-Quantum Cryptography in the SaaS industry?

We're going to see continued rollout of `X25519MLKEM768` key exchange to edge services, trial deployments of Merkle Tree Certificates, and continued adoption of post-quantum safe encryption algorithms by service providers.

For us security professionals and service providers it is incumbent to keep an eye on the latest developments in the field, and to start planning for migration to post-quantum safe alternatives for the broken cryptographic algorithms.

There are many changes ahead making it hard to predict what the next, say, 5-10 years will look like. But we can point to clear standards set by NIST: ML-DSA and ML-KEM are the post-quantum safe alternatives for digital signatures and key exchanges, respectively.

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
21. [https://en.wikipedia.org/wiki/Curve25519](https://en.wikipedia.org/wiki/Curve25519)
22. [https://live.paloaltonetworks.com/t5/quantum-security-articles/the-quantum-countdown-how-hybrid-encryption-is-quietly/ta-p/1230276](https://live.paloaltonetworks.com/t5/quantum-security-articles/the-quantum-countdown-how-hybrid-encryption-is-quietly/ta-p/1230276)
23. [https://letsencrypt.org/2026/06/03/pq-certs](https://letsencrypt.org/2026/06/03/pq-certs)
24. [https://blog.cloudflare.com/bootstrap-mtc/](https://blog.cloudflare.com/bootstrap-mtc/)
25. [https://www.microsoft.com/en-us/security/blog/2026/06/30/microsoft-advances-quantum-safe-security-as-the-risk-timeline-shifts/](https://www.microsoft.com/en-us/security/blog/2026/06/30/microsoft-advances-quantum-safe-security-as-the-risk-timeline-shifts/)
26. [https://www.microsoft.com/en-us/security/blog/2026/04/16/building-your-cryptographic-inventory-a-customer-strategy-for-cryptographic-posture-management/](https://www.microsoft.com/en-us/security/blog/2026/04/16/building-your-cryptographic-inventory-a-customer-strategy-for-cryptographic-posture-management/)
