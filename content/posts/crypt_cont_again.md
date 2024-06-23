---
title: "A very, very light introduction to security continued again"
date: 2020-05-30
draft: false
tags: ["technology"]
---
[A continuation of this post]({{< ref "crypt_cont.md" >}})

[Continued in this post]({{< ref "crypt_final.md" >}})
# Introduction
In [the previous post]({{< ref "crypt_cont.md" >}}) we described the basic process of how to achieve the 3 desired outcomes of security:
1. Confidentiality: only the sender(s) and the receiver(s) can understand the message.

2. Authenticity: the receiver(s) can verify the identity of the sender(s).

3. Integrity: the receiver(s) can verify the message was not altered from its original content.

Now let's add efficiency considerations.
# Primer: security trade-offs
A good rule of thumb is increasing security decreases usability. For example, putting your favorite pants into a locked safe with an unknown code makes them very secure against theft, and very hard to wear.

You could imagine potential security-usability trade-offs as a frontier. Perhaps you could choose either 1.00 security and 0.00 usability; 0.00 security and 1.00 usability; or 0.50 security and 0.50 usability. This frontier expands through security breakthroughs, such as new technologies and clever math (e.g. public-private keys via Diffie and Hellman).
# One-way hash functions
A hash function takes input of arbitrary size and produces output, called a "hash value", of fixed size. For instance, no matter if the input is a thousand bits or a million bits, our hash function `example_hash(input) = 0xe3` (`0x` denotes hexadecimal notation) produces 8 bits. Obviously, hash functions used in practice are much more complicated.

For our use case (described later), we want a one-way hash function. A good one-way hash function, `one_way(x) = y`, has the following properties:
1. (Pre-image resistance) Given `y`, it should be hard to find the original `x` such that `one_way(x) = y`, hence "one-way."
2. (Collision resistance) Given `x`, it should be hard to find `z` (not equal to `x`) such that `one_way(x) = one_way(z)`.[^1] Informally, changes in the input lead to unpredictable changes in the output.
[^1]: When two different inputs have the same hash value, this is known as a hash collision.
# Symmetric encryption
In the previous post, we used public-private key pairs to perform **asymmetric encryption.** In asymmetric encryption, the key used to encrypt does not equal the key used to decrypt, hence "asymmetric."

In **symmetric encryption**, the same key is used to encrypt and decrypt. In general, as you would intuit, both asymmetric and symmetric encryption take longer when the input gets longer. However, symmetric encryption tends to be noticeably faster than asymmetric.
# Revised revised process
With these two concepts, let's revisit the process from the previous post, adding in some efficiency:
1. The sender and receiver generate their own public-private key pairs.
2. The sender and receiver publish their own public keys.
3. The sender retrieves the receiver's public key.
4. **The sender generates a symmetric encryption key, then hashes it.**
5. The sender encrypts **the hash value** with their own private key to create a signature.
6. **The sender combines the symmetric encryption key and the signature in a format known to both parties to form the plaintext.**
7. The sender encrypts **the combined plaintext** with the receiver's public key.[^2]
8. The sender sends **the resulting ciphertext** to the receiver.
9. The receiver retrieves the sender's public key.
10. **The receiver decrypts the ciphertext with their own private key, then extracts the symmetric encryption key and signature.** 
11. The receiver decrypts the signature with the sender's public key, and makes sure the decrypted signature equals **the hashed symmetric key**.
12. **From then on, the symmetric encryption key is used to encrypt and decrypt all subsequent messages.**
[^2]: You might notice in the previous post we encrypted then signed. In this post, we sign then encrypt. See [this Stack Exchange post](https://crypto.stackexchange.com/questions/5458/should-we-sign-then-encrypt-or-encrypt-then-sign).

The gains in efficiency are as follows:
1. Instead of encrypting an entire message to create a signature, we only need to encrypt the much shorter hash value.
2. All subsequent messages use symmetric, rather than asymmetric, encryption.

The second gain is much more impactful in magnitude of time saved. Because we used a one-way hash function with collision resistance, an attacker still cannot modify messages without being noticed, so we have maintained integrity. The use of public-private key pairs maintains confidentiality and authenticity.
# Conclusion
Once again, if the concept doesn't come to you immediately, take some time (when you can) to think about it. Next time, I'll take about certificates.
