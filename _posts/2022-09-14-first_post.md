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

### The history.

Substitution ciphers marked the beginning of cryptography. They were used anciently, most notably by Caesar's Rome (ever heard of the "Caesar Shift"?). These ciphers were largely unsolvable for millenium, until formal techniques such as "frequency analysis" were introduced. Even still, some substitution ciphers were so advanced that these techniques were rendered useless; in fact, attempts to break one notorious cipher, the German "enigma" from WWII, led to the invention of what many call the first computer. Ciphers nowadays involve advanced mathematics in areas such as prime number theory, finite fields, and elliptic curves.

> Substition ciphers led to the invention of what many call the first computer.

Despite the fact that modern cryptography has shifted far beyond substituion ciphers, messages that are shorter than 100 characters, especially when spaces are removed, are relatively unsolvable to someone without a computer when encrypted with a substitution cipher. This can be a lot of fun for friends who want to communicate without being discovered by their peers.

Substition ciphers are quite easy to create in programs like python and R. We will now explore one such cipher, the "Affine Cipher", and how it can be created in R.

### The theory.

The Affine cipher is a one-to-one substituion, meaning every letter of the alphabet maps to one other letter only; for instance, if the 'h' in the message "helloworld" mapped to 'y', it would only map to 'y'. This mapping is accomplished with a function of the form:
```
L(x) = ax + b (mod 26)
```
Every letter is assigned a value, with 'a' = 0, 'b' = 1, ..., and 'z' = 25. This number, when plugged into the above function, produces another letter. It is important to note that this function only creates a one-to-one mapping when 'a' and 26 are coprime (this means that 'a' must be odd and NOT 13).

Let's say we pick 'a' to be 11 and 'b' to be 6. Thus we have:
```
L(x) = 11x + 6 (mod 26)
```
With this funciton, the message "helloworld" maps to "fyxxeoelxn". Want to make sure? Plug in 'h' = 7 and confirm that 11(7) + 6 (mod 26) = 5. Try each letter yourself!

### The code.

First step is to split your message into a vector of characters. R does this nicely with the 'strsplit' function:

```
message <- strsplit("helloworld", "")[[1]]
```

We must also initialize our key values, as well as a vector for our cipher text. I prefer to do this as follows:

```
a <- 11
b <- 6
cipher <- vector()
```

Another nice quality of R is the default 'LETTERS' character vector. 'LETTERS' contains each letter A-Z. Each of these letters is, by deafult, uppercase. This means you either want to make them lowercase or make your message uppercase. In my example, I will change my message to uppercase.

You can use this 'LETTERS' vector to create the numeric value for each letter in your message. A for loop works nicely for this:

```
for (i in 1:length(message)) {
    message[i] <- str_to_upper(message[i])
    value <- match(message[i], LETTERS) - 1
}
```

Notice 2 things:
* 1. The 'match' function in R will return the index where the character 'message[i]' matches a character in the vector 'LETTERS'. *
* 2. We add the '- 1' in our declaration of 'value' because R indexes starting at 1, and remember that our cipher implies that we index starting at 0. *

We now have a numeric value for our letter. We can then insert that number into our Affine function. This is quite easy. Note that I continue our previous for loop:

```
for (i in 1:length(message)) {
    message[i] <- str_to_upper(message[i])
    value <- match(message[i], LETTERS) - 1

    cipher_value <- ((a*value + b) %% 26) + 1
    cipher[i] <- LETTERS[ciph_value]
}
```

Notice that in the 'cipher_value' declaration, we include '+ 1' in order to align with R indexing.

We now have a vector, 'cipher', that contains each mapped-to letter from our original message using our Affine function. We can piece it back together using the 'paste' function:

```
ciphertext <- paste(cipher, sep = "", collapse = "")
```

We've done it! Using "helloworld" as our message, 'ciphertext' now contains the text 'FYXXEOELXN'.

### The decryption.





### An application.
https://adamiser.shinyapps.io/affinecipher/