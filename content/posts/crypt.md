---
title: "A very, very light introduction to security"
date: 2020-05-02
draft: false
tags: ["technology"]
---
[Continued in this post]({{< ref "crypt_cont.md" >}})
# Introduction
[They say the best way to learn something is to explain it to a 5-year-old (or some other beginner).](https://getpocket.com/explore/item/the-feynman-technique-the-best-way-to-learn-anything) So let's explain security!
# Desired outcomes
In security, we want to create 3 outcomes:

1. Confidentiality

Only the sender(s) and the receiver(s) can understand the message. For example, if you sent your coworker an invite to lunch, neither your boss nor the government could see the invite.

2. Authenticity

The receiver(s) can verify the identity of the sender(s). For example, if your coworker sent back an invite to dinner instead, you could be sure it was your coworker, and not your boss nor the government disguised as your coworker.

3. Integrity

The receiver(s) can verify the message was not altered from its original content. For example, if you sent your coworker an address to meet for dinner, your coworker could be sure the address was not changed (by your boss or the government) from the original address you typed out.
# A high-level overview
In order to accomplish these outcomes, we can use encryption and decryption (and some other things, in later posts).

We start with the plaintext. The **plaintext** is the original content of a message; it could be an English sentence, a string of emojis, a file, really anything that can be represented in a computer. 

Next, we have a **key**. A key is a value given to encryption or decryption that is not the plaintext nor the ciphertext (defined next).

The sender **encrypts** the plaintext with a key. This creates the **ciphertext**, which is sent to the receiver. Ideally, no one except the receiver should be able to make sense of the ciphertext.

The receiver **decrypts** the ciphertext with a key; this recreates the plaintext.
# The dirty details (math)
In this post, we'll only cover confidentiality. In later posts, we can cover authenticity and integrity.

In order to create confidentiality, the receiver needs a pair of keys: a public key and a private key. If you're interested in the cool and complicated math behind this, you should search "Diffie Hellman" or shake the nearest security expert. In quick summary, the public and private key have 2 extremely useful characteristics:
1. If something is encrypted with the public key, it can only be decrypted with the corresponding private key.
2. Given the public key, it is [theoretically impractical](https://xkcd.com/538/) to figure out the private key.

So, the full process looks something like:
1. The receiver generates a pair of a public key and a private key.
2. The receiver publishes their public key.
3. The sender retrieves the receiver's public key.
4. The sender encrypts the plaintext with the receiver's public key.
5. The sender sends the resulting ciphertext to the receiver.
6. The receiver decrypts the ciphertext with their private key to get the original plaintext.

Even though the public key is published, because it is theoretically impractical to figure out the private key given the public key, only the receiver will have the private key necessary to decrypt the ciphertext. Thus, we have made it so that only the sender and receiver can understand the message; we have accomplished confidentiality!
# Conclusion
In future posts, we'll cover authenticity and integrity. I'll get to it when I get to it.
