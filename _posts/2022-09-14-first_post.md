---
layout: post
title:  "MYYPQTU NGHG WGJY: Cryptography in R"
date:   2022-09-14
author: Adam Simpson
description: A deep dive into advanced substitution ciphers and creating them in R
image: /assets/images/Lock.jpeg
---
I'm quite sure you've seen cipher text similar to the title of this post at some point in your life. Isn't it thrilling?! You know a message lurks somehwere within the jumbled letters. As data scientists, we understand the importance of "safe data". Cryptography, with its beautiful math and essential role in today's data-driven age, ought to demand from us at least some level of respect and interest.

> As data scientists, we understand the importance of "safe data".

Substitution ciphers marked the beginning of cryptography. They were used anciently, most notably by Caesar's Empire (ever heard of the "Caesar Shift"?). These ciphers were largely unsolvable for millenium, until formal techniques such as "frequency analysis" were introduced. Despite this, some substitution ciphers were so advanced that these techniques were rendered useless. Attempts to break one notorious cipher, the German "enigma" from WWII, led to the invention of what many call the first computer.

> Substition ciphers led to the invention of what many call the first computer.

Modern cryptography has moved beyond substitution ciphers into [higher-level public key systems](https://www.infoworld.com/article/3641388/a-quick-guide-to-modern-cryptography.html), RSA being the most famous. However, messages that are shorter than 100 characters, especially when spaces are removed, are relatively unsolvable to someone without a computer when encrypted with certain substitution ciphers. These types of ciphers offer a wonderful introduction into the idea of data encryption.

> These ciphers are virtually unsolvable when shorter than 100 characters!

Plus, substition ciphers are quite simple to create! They employ various principles of number theory that are easily employed by computers. We will now explore one such cipher, the "Affine Cipher", and how it can be created in R.



# Theory

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

> **At this point you should be thinking, "I wonder if the title of this post was encrypted in this way...?"**



# Code

Our first step is to split our message into a vector of characters. R does this nicely with the 'strsplit' function. We must also initialize our key values, as well as a vector for our cipher text.

```
message <- strsplit("helloworld", "")[[1]]

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

*Notice 3 things:*
1. We used the function 'str_to_upper', a function found in the _tidyverse_ library.
2. The 'match' function in R will return the index where the character 'message[i]' matches a character in the vector 'LETTERS'.
3. We add the '- 1' in our declaration of 'value' because R indices start at 1, and remember that our cipher implies that we index starting at 0.

We now have a numeric value for our letter. We can then insert that number into our Affine function. This is quite easy. Note that I extend our previous for loop:

```
for (i in 1:length(message)) {
    message[i] <- str_to_upper(message[i])
    value <- match(message[i], LETTERS) - 1

    cipher_value <- ((a*value + b) %% 26) + 1
    cipher[i] <- LETTERS[ciph_value]
}
```

*Notice that in the 'cipher_value' declaration, we include '+ 1' in order to align with R indexing.*

We now have a vector, 'cipher', that contains each mapped-to letter from our original message using our Affine function. We can piece it back together using the 'paste' function:

```
ciphertext <- paste(cipher, sep = "", collapse = "")
```

We've done it! Using "helloworld" as our message, 'ciphertext' now contains the text 'FYXXEOELXN'.



# Decryption

*Decrpytion is simple: reverse the above process!*

I leave this as a challenge to you - how would you reverse the process? I'll start you with a hint. Inverting this process involves calculating the "multiplicative modular inverse". There are many ways to do this, from [Euclid's Algorithm](https://www.extendedeuclideanalgorithm.com/multiplicative_inverse.php) to [other naive methods](https://www.khanacademy.org/computing/computer-science/cryptography/modarithmetic/a/modular-inverses). I found it easiest to implement the method found in [this video](https://www.youtube.com/watch?v=03Gv0YAMWmo&t=131s). I've coded it as follows:

```
modulus <- 26
for (i in 1:modulus) {
    value <- ((i*modulus)+1)/a
    if (value%%1 == 0) {
      inverse <- value
      break
    }
  }
  return(inverse)
```

# Closing

Substition ciphers, though relatively easy to break today, are quite fun to create and use. The math behind the scenes is interesting and the code is an enjoyable puzzle. We have only scratched the surface - go check out the [Hill Cipher](https://crypto.interactive-maths.com/hill-cipher.html), the [Vigenère Cipher](https://www.geeksforgeeks.org/vigenere-cipher/), or the [Playfair Cipher](http://practicalcryptography.com/ciphers/playfair-cipher/). Each of these ciphers have been used to encrypt incredibly important messages throughout the history of the world. All of them can be replicated on computers!

**Now, check out [this Affine Cipher application](https://adamiser.shinyapps.io/affinecipher/) I made in R Shiny.** 

And yes, I used it to encrpyt the title!
