---
title: "A very, very light introduction to security continued for a final time"
date: 2020-06-06
draft: false
tags: ["programming", "technology"]
---
[A continuation of this post]({{< ref "crypt_cont_again.md" >}})
# Introduction
In [the previous post]({{< ref "crypt_cont_again.md" >}}) we described how to make the process of establishing confidentiality, authenticity, and integrity more efficient. Now let's put in some finishing touches.
# Primer: "you gotta' trust someone"
In any system, you have to trust someone. When paying with a credit card, you trust the payment provider. When sending mail, you trust the postal service. When cooking food, you trust the grocer. The list goes on.

Ideally, the circle of people you need to trust is as small and accountable as possible. For online security (HTTPS), besides the engineers writing the browser, you trust the certificate authority.
# Why we need a certificate authority
Let's look at the process again (focus on the bolded sections):
1. The sender and receiver generate their own public-private key pairs.
2. The sender and receiver publish their own public keys.
3. **The sender retrieves the receiver's public key.**
4. The sender generates a symmetric encryption key, then hashes it.
5. The sender encrypts the hash value with their own private key to create a signature.
6. The sender combines the symmetric encryption key and the signature in a format known to both parties to form the plaintext.
7. The sender encrypts the combined plaintext with the receiver's public key.
8. The sender sends the resulting ciphertext to the receiver.
9. **The receiver retrieves the sender's public key.**
10. The receiver decrypts the ciphertext with their own private key, then extracts the symmetric encryption key and signature.
11. The receiver decrypts the signature with the sender's public key, and makes sure the decrypted signature equals the hashed symmetric key.
12. From then on, the symmetric encryption key is used to encrypt and decrypt all subsequent messages.

Suppose a malicious party intercepts steps 3 or 9, during which the public keys are retrieved. If the malicious party swaps out the public keys with the public key from their own pair, then the entire process breaks down. 

Because the public keys' being correct is so vital to the process, we have certificate infrastructure. Certificates contain the public keys of their owners. Certificate authorities are then responsible for verifying certificates, essentially assuring "this is the right public key of the sender/receiver." 
# Retrieving public keys through certificates
To accomplish this, certificate infrastructure adds another layer of asymmetric encryption via public-private key pairs. The certificate authority has their own public and private keys. Web browsers keep a list of the public keys of well-known certificate authorities, so for Party A to retrieve the public key of Party B:
1. Ahead of time, Party B provides the certificate authority (CA) with Party B's public key. 
2. The CA issues a certificate for Party B, encrypts the certificate with the CA's private key, and sends the result to Party B.
3. Party A asks Party B for Party B's certificate.
4. Party B sends Party A the encrypted certificate from the CA, identifying which CA issued the certificate.
5. Assuming Party A's browser already stored that CA's public key, Party A decrypts Party B's certificate with the CA's public key.
6. Party A now has the right public key of Party B on the honor of the CA ("you gotta' trust someone").
# Session keys
So we just corrected the retrieval of public keys with certificate infrastructure. Another correction we should make is the generation of the symmetric encryption key. It is better to have both the sender and receiver generate the symmetric encryption key, as well as generate multiple symmetric encryption keys, called "session keys," that expire. That way, if problems do occur during the process, they are obvious and shorter-lived.

In (pseudo)random value generation, such as session keys generation, we can use "seeds." In a (pseudo)random generator algorithm, starting from the same seed makes sure the same "random" values are generated. That is, as long as the sender and receiver both start from the same seeds, they will independently "randomly" generate the same session keys values. The process is securely random if the seeds were securely randomly generated.
# Final revised process
Let's revise our process one final time\![^1] Note instead of "sender," we will use the term "client," and instead of "receiver," we will use the term "server." This is the more commonly used terminology in describing web security.
[^1]: Okay, I'll leave out a few details for simplicity and understandability. There's always going to be some level of detail left out for clarity.
1. **The server** generates their own public-private key pair.
2. **The server provides the public key to the CA.**
3. **The CA issues a certificate for the server, encrypts the certificate with the CA's own private key, and sends the result to the server.**
4. **The client sends a "client hello" message to the server, containing the protocols (e.g. for later symmetric encryption) supported by the client and the "client random," a value randomly generated by the client.**
5. **The server replies with a "server hello" message to the client, containing the protocol selected, the server's certificate (identifying which CA issued the certificate), and the "server random," a value randomly generated by the server.**
6. **The client decrypts the server's certificate with the CA's public key, getting the server's public key from the certificate.**
7. The client generates **another random value, called the "premaster secret."** 
8. The client encrypts **the premaster secret** with the server's public key.
9. The client sends the resulting ciphertext to the server.
10. The server decrypts the ciphertext with their own private key, **getting the premaster secret.**
11. **Using the client random, server random, and premaster secret, both the client and server generate the session keys. If neither side has had issues so far, the resulting session keys should be the same on both sides.**
12. **The client and server send "finished" messages to each other to signal they are both ready for symmetric encryption.**
13. From then on, the session keys are used to symmetrically encrypt and decrypt all subsequent messages. **Though not used so far, hashing and signatures are useful from this step onward. We can use hashing similar to how we did before to create signatures for subsequent messages.**
# Conclusion
After revising the process multiple times, we've devised a clever, efficient process for confidentiality, authenticity, and integrity with few necessary points of trust (certificate authorities). This process is known as TLS, and in you are interested in diving deeper, you should search it up.
