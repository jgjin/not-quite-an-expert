---
title: "A very, very light introduction to security continued"
date: 2020-05-23
draft: false
tags: ["programming", "technology"]
---
[A continuation of this post]({{< ref "crypt.md" >}})

[Continued in this post]({{< ref "crypt_cont_again.md" >}})
# Introduction
In [a previous post]({{< ref "crypt.md" >}}) we defined 3 desired outcomes of security:
1. Confidentiality: only the sender(s) and the receiver(s) can understand the message.

2. Authenticity: the receiver(s) can verify the identity of the sender(s).

3. Integrity: the receiver(s) can verify the message was not altered from its original content.

That post covered how to achieve confidentiality. In this post, we'll cover authenticity and integrity.
# Motivation
Suppose you get a message from your friend:
> Hey bud, can you send 20 bucks to @yagirl2912 on Venmo for Sam's birthday present? Thanks in advance!

How can you be sure the person who sent it is your friend, and not a scammer (authenticity)? Even if your friend sent it, how can you be sure a scammer didn't swap out the Venmo username for their own along the way (integrity)?
# Public-private key pairs
Recall from [the previous post]({{< ref "crypt.md" >}}) that public-private key pairs have 2 extremely useful characteristics:
1. If something is encrypted with the public key, it can only be decrypted with the corresponding private key.
2. Given the public key, it is theoretically impractical to figure out the private key.

Public-private key pairs also have a third extremely useful characteristic, basically a reversal of the first characteristic:

3. If something is encrypted with the private key, it can be decrypted with the corresponding public key.

We will take advantage of this third characteristic to achieve authenticity and integrity.
# Revised process
To accomplish confidentiality, our process looked like:
1. The receiver generates a pair of a public key and a private key.
2. The receiver publishes their public key.
3. The sender retrieves the receiver's public key.
4. The sender encrypts the plaintext with the receiver's public key.
5. The sender sends the resulting ciphertext to the receiver.
6. The receiver decrypts the ciphertext with their private key to get the original plaintext.

With only a few modifications (bolded and italicized), we can achieve authenticity and integrity:

1. _**The sender and receiver generate their own public-private key pairs.**_
2. _**The sender and receiver publish their own public keys.**_
3. The sender retrieves the receiver's public key.
4. The sender encrypts the plaintext with the receiver's public key.
5. _**The sender encrypts the resulting ciphertext from the previous step with their own private key to create a signature.**_
6. The sender sends the _**ciphertext and signature**_ to the receiver.
7. _**The receiver retrieves the sender's public key.**_
8. _**The receiver decrypts the signature with the sender's public key, and makes sure the decrypted signature equals the ciphertext.**_ 
9. The receiver decrypts the ciphertext with their private key to get the original plaintext.

Recall it is theoretically impractical to figure out the private key given the public key. Therefore, only the sender will have the private key necessary to create a signature that when decrypted with the sender's public key will equal the ciphertext. If the receiver decrypts the signature and the result is not equal to the ciphertext, then either:
1. The sender did have the appropriate private key, so is likely not who they claim to be.
2. Or the message was altered along the way.

In either case, the receiver can and should reject the message. 

Going back to the motivating example, step 8 of the revised process allows us to detect if a scammer sent or altered the message. We have accomplished authenticity and integrity!
# Conclusion
If the concept doesn't come to you immediately the first time, sit down and think about it for a while. It took me a while to even re-learn the concept while writing this post. In the next post of this series, I'll talk about certificates or efficiency.
