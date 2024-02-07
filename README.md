# puzzle-chaos-theory

**DO NOT FORK THE REPOSITORY, AS IT WILL MAKE YOUR SOLUTION PUBLIC. INSTEAD, CLONE IT AND ADD A NEW REMOTE TO A PRIVATE REPOSITORY, OR SUBMIT A GIST**

Trying it out
=============

Use `cargo run --release` to see it in action

Submitting a solution
=====================

[Submit a solution](https://xng1lsio92y.typeform.com/to/UYMwUsgG)

[Submit a write-up](https://xng1lsio92y.typeform.com/to/NGwTHlVz)

Puzzle description
==================

    |___  /| | / / | | | |          | |
       / / | |/ /  | |_| | __ _  ___| | __
      / /  |    \  |  _  |/ _` |/ __| |/ /
    ./ /___| |\  \ | | | | (_| | (__|   <
    \_____/\_| \_/ \_| |_/\__,_|\___|_|\_\

Bob designed a new one time scheme, that's based on the tried and true method of encrypt + sign. He combined ElGamal encryption with BLS signatures in a clever way, such that you use pairings to verify the encrypted message was not tampered with. Alice, then, figured out a way to reveal the plaintexts...

# Writeup

https://hackmd.io/@liquan/H1srq-D5T

## ElGamal encryption in ECC

### Key generation
Alice:
- secret key: $x$
- public key: $H = x\cdot G$

### Encryption
Bob:
- $M = F(m)$, the $m$ is the message, and the $M$ is the element in $G$, $F$ is an invertible function
- ephemeral key: $y$
- shared secret: $S = H \cdot y = x \cdot y \cdot G$
- $C_1 = y \cdot G$
- $C_2 =S + M$
- public $C_1, C_2$

### Decryption
Alice:
- $S = C_1 \cdot x = x \cdot y \cdot G$
- $M = C_2 - S$
- $m = F^{-1}(M)$

## Solution
The Struct and method:
- Sender(Bob): $y$ and $C_1 = y \cdot G_1$
- Receiver(Alice): $H$
- Message: $M$
- ElGamal: $(C_1, C_2)$ in $G_1$, $Hash((C_1, C_2))$ in $G_2$ group
- Sender's `send()`: new  $C_1$, $C_2 = H \cdot y + M = S + M$
- Sender's `authenticate()`: $y \cdot H((C_1, C_2))$
- Auditor's `check_auth()`: $e(G_1, y\cdot Hash((C_1, C_2))) = e(C_1, Hash((C_1, C_2)))$

Now, we have blob, which is $C_1, (C_1, C_2), y\cdot Hash((C_1, C_2)), H$, and some messages, how to know which message is the pre-image of (C_1, C_2)?


$e(x \cdot y \cdot G_1, Hash((C_1, C_2))) = e(x \cdot G_1, y \cdot Hash((C_1, C_2)))$

$S = x \cdot y \cdot G_1 = C_2 - M$, so we just need to try different messages to get different S, and check if the pairs equal.
