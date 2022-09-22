---
layout: post
title:  "Writing code with code: Substitution ciphers in R"
date:   2022-09-14
author: Adam Simpson
description: A deep dive into advanced substitution ciphers and creating them in R
image: /assets/images/cipher.jpeg
---

# Writing code with code: Substitution ciphers in R

## A "deep dive" into advanced substitution ciphers and creating them in R

### A brief history of substition ciphers

Substitution ciphers marked the beginning of cryptography. They were used anciently, most notably by Caesar's Rome (ever heard of the "Caesar Shift"?). These ciphers were largely unsolvable for millenium, until formal techniques such as "frequency analysis" were introduced. Even still, some substitution ciphers were so advanced that these techniques were rendered useless; in fact, attempts to break one notorious cipher, the German "enigma" from WWII, led to the invention of what many call the first computer. Ciphers nowadays involve advanced mathematics in areas such as prime number theory, finite fields, and elliptic curves.

Despite the fact that modern cryptography has shifted far beyond substituion ciphers, messages that are shorter than 100 characters, especially when spaces are removed, are relatively unsolvable to someone without a computer when encrypted with a substitution cipher. This can be a lot of fun for friends who want to communicate without being discovered by their peers.

Substition ciphers are quite easy to create in programs like python and R. We will now explore one such cipher, the "Affine Cipher", and how it can be created in R.

### First, the theory.

The Affine cipher is a one-to-one substituion, meaning every letter of the alphabet maps to one other letter only; for instance, if the 'h' in the message "helloworld" mapped to 'y', it would only map to 'y'. This mapping is accomplished with a function of the form:
```
L(x) = ax + b (mod 26)
```
Every letter is assigned a value, with 'a' = 0, 'b' = 1, ..., and 'z' = 25. This number, when plugged into the above function, produces another letter. It is important to note that this function only creates a one-to-one mapping when 'a' and 26 are coprime (this means that 'a' must be odd and NOT 13).

Let's say we pick 'a' to be 11 and 'b' to be 8. Thus we have:
```
L(x) = 11x + 6 (mod 26)
```
With this funciton, the message "helloworld" maps to "fyxxeoelxn". Want to make sure? Plug in 'h' = 7 and confirm that 11(7) + 6 (mod 26) = 5. Try each letter yourself!

### Second, the code.


### Finally, the application.