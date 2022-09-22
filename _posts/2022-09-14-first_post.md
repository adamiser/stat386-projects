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


### Second, the code.


### Finally, the application.