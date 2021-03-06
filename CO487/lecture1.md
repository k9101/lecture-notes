# Lecture 1 - January 3, 2018

Alice and Bob want to send a message, but Eve wants to listen in

# Fundemental Goal of Crypto

- **Confidentiality**: Keeping data secret from all but those authorized to see it

### Authentication

Ensure that parties are who they say they are. Confidence in the actual messages.

- **Data Integrity**: Ensuring data has not been altered by unauthorized means (detect alterations)
- **Data origin auth**: Can determine the source of the message
- **Non-repudiation**: Prevent an entity from denying that they sent a message
  - i.e. Alice denys that she sent a message to Bob, but Bob can prove that she did.

### Services

Some fields only need some of these guarantees. For example, you may just care about authentication.

### Assume that the Adversary is Always Malicious

If you assume everyone will behave, you don't need cryptography

## Secure Web Transactions

**Secure Socket Layer (SSL)**: Encrypt web traffic
- Example of **symmetric-key cryptography**
  - A public key can encrypt the data (from Gmail), Some secret key ![latex-bb9c3603-c209-45fa-84e4-2f5ae78b0ed8](data/lecture1/latex-bb9c3603-c209-45fa-84e4-2f5ae78b0ed8.png) (determined by Alice) can decrpyt the message.
  - Alice must *securely* share ![latex-48236a55-8894-4a2d-815e-c389b6813e5a](data/lecture1/latex-48236a55-8894-4a2d-815e-c389b6813e5a.png) with Gmail, how?
    - Answer is **Public Key Cryptography**,
    - RSA signature scheme to validate Gmail's public key. Completed by some trusted **cretifying Authority**

## The SSL Protocol

A secure technology as the doesn't have to do anything to use it.

1. Client visits a web page, server sends it's certificate to the client
  - Alice's browser can verify the RSa public key
2.
3. Client select's a random session key $k$, encrypt it using the public key
4. Server can decrypt the session key
5.

### Potential Weak Points

1. The crypto itself is weak, if it is broken then SSL is broken.
2. Quantum attacks on the underlying cryptography, *if* a quantum computer can be built then RSA is broken
3. Weak random number generation, devices are really bad at picking random numbers.
4. Issuance of fradulent certificates
  - Verisign did so in 2001, released 2 Microsoft certificates.
  - Human error / social engineering.
5. Software bugs (both inadvertent and malicious)
  - How can you gain assurance that software is trustworthy?
6. Phishing attacks
  - Most users don't verify the security information by clicking on the padlock
7. SSL only protects data during transit, no protection once it's on the server.
  - The server / database where your data is stored could be compromised or badly secured etc.
  - NSA

## Information Security

- Computer Security: Keep physical mechines secure
- Network security: DDOS, firewalls
- Software Security:

## Crypto != Security

Crypto provides a mathematical toolset that can assist with information security. Crypto is just part of a chain, typically a stronger link.
It takes one flaw in the chain to break in.
