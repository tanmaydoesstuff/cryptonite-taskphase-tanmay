## C3
**Flag: `picoCTF{adlibs}`**
**Difficulty:** `medium`

In this challenge, we are given a program, that we need to reverse the logic for to obtain the solution. This can be figured out since the encoder is given to us.

Here is the reverse program code,
```
lookup1 = "\n \"#()*+/1:=[]abcdefghijklmnopqrstuvwxyz"
lookup2 = "ABCDEFGHIJKLMNOPQRSTabcdefghijklmnopqrst"

ciphertext = "DLSeGAGDgBNJDQJDCFSFnRBIDjgHoDFCFtHDgJpiHtGDmMAQFnRBJKkBAsTMrsPSDDnEFCFtIbEDtDCIbFCFtHTJDKerFldbFObFCFtLBFkBAAAPFnRBJGEkerFlcPgKkImHnIlATJDKbTbFOkdNnsgbnJRMFnRBNAFkBAAAbrcbTKAkOgFpOgFpOpkBAAAAAAAiClFGIPFnRBaKliCgClFGtIBAAAAAAAOgGEkImHnIl"

out = ""
prev = 0

for char in ciphertext:
    cur = lookup2.index(char)
    original_index = (cur + prev) % 40 
    out += lookup1[original_index]
    prev = original_index

print(out)
```

On running the reverse program above, we get this text output, which we will call `c3_2.py`, 
```
#asciiorder
#fortychars
#selfinput
#pythontwo

chars = ""
from fileinput import input
for line in input():
    chars += line
b = 1 / 1

for i in range(len(chars)):
    if i == b * b * b:
        print chars[i] #prints
        b += 1 / 1
```

Now we convert `c3_2.py` into Python3 from Python2. Self input gives us a hint that we need to input the first program into the second.

```
with open('c3_2.py') as f:
    ciphertext = f.read()
asciichars = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmn"
b = 1
for i in range(len(ciphertext)):
    if i == b*b*b:
        print(ciphertext[i])
        b += 1
```

On running this, we finally get the flag `picoCTF{adlibs}`.

**Learnt:** 
1. reversing python programs

**Challenges/Wrong Approaches:**
1. Got stuck when I received the output from the first program, until I figured out the significance of the self input tag.

---
## Custom Encryption
**Flag: `picoCTF{custom_d2cr0pt6d_e4530597}`**
**Difficulty:** `medium`

In this challenge, we are given a custom encryption which we need to reverse the logic for. We are also given a, b and the cipher. On researching a bit, we can easily find out that this challenge entails the Diffie Hellman key exchange.

On running the program, we get `}7950354e_d6tp0rc2d_motsuc{FTCocip`. On reversing this, we finally get the flag `picoCTF{custom_d2cr0pt6d_e4530597}`.

**Learnt:** 
1. diffie hellman

**References:**
https://www.youtube.com/watch?v=Yjrfm_oRO0w
https://www.youtube.com/watch?v=NmM9HA2MQGI
https://www.geeksforgeeks.org/implementation-diffie-hellman-algorithm/

---

## miniRSA
**Flag: `picoCTF{n33d_a_lArg3r_e_ccaa7776}`**
**Difficulty:** `hard`

How RSA works is that `N` is a really large number, which is a product of two prime numbers (both very large and with a large difference). `e`, the public exponent is also typically large, but in this channel, it has been given as `3`, which we can exploit as a vulnerability.

In RSA, the ciphertext C is calculated as: `C=M^e mod  N`. Decrypting the message involves finding `M=C^d mod  N` (where `d` is the private key). The private key `d` satisfies `d⋅e≡1modϕ(N)`, where `ϕ(N)=(p−1)(q−1)` is the totient function of N.

Since `e=3`, `C=M^3 mod N`.
So, `M^3=k⋅N+C` where `k` is a small integer. So we need to loop through different small values of k, which when put in the equation give us the value for `M^3` that has a perfect cube root. That cube root would be the plaintext.

In this particular question, `k=0`, we basically need to compute the exact cube root of `M^3` or `C`. I did this using an online calculator and got M as `13016382529449106065894479374027604750406953699090365388203708028670029596145277`. 
Converting this to plaintext, we obtain the flag `picoCTF{n33d_a_lArg3r_e_ccaa7776}`.

**Learnt:** 
1. rsa and it's vulnerabilities
2. small e attack

**Challenges/Wrong Approaches:**
1. Initially I was trying to use some different vulnerability, that did not give the answer. 
2. I faced problems in obtaining the answer, after getting cube root of `M^3` to get `M` (the plaintext)

**References:**
https://www.youtube.com/watch?v=-ShwJqAalOk
https://infosecwriteups.com/breaking-rsa-algorithm-fermats-surprise-554768a707ea