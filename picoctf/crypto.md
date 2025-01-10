## C3
**Flag: `picoCTF{adlibs}`**
**Perceived Difficulty:** `medium`

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
**Perceived Difficulty:** `medium`

In this challenge, we are given a custom encryption which we need to reverse the logic for. We are also given a, b and the cipher. On researching a bit, we can easily find out that this challenge entails the Diffie Hellman key exchange.

```
from random import randint  
import sys  
  
  
def generator(g, x, p):  
return pow(g, x) % p  
  
  
def encrypt(plaintext, key):  
cipher = []  
for char in plaintext:  
cipher.append(((ord(char) * key*311)))  
return cipher  
  
  
def is_prime(p):  
v = 0  
for i in range(2, p + 1):  
if p % i == 0:  
v = v + 1  
if v > 1:  
return False  
else:  
return True  
  
  
def dynamic_xor_encrypt(plaintext, text_key):  
cipher_text = ""  
key_length = len(text_key)  
for i, char in enumerate(plaintext[::-1]):  
key_char = text_key[i % key_length]  
encrypted_char = chr(ord(char) ^ ord(key_char))  
cipher_text += encrypted_char  
return cipher_text  
  
  
def test(plain_text, text_key):  
p = 97  
g = 31  
if not is_prime(p) and not is_prime(g):  
print("Enter prime numbers")  
return  
a = randint(p-10, p)  
b = randint(g-10, g)  
print(f"a = {a}")  
print(f"b = {b}")  
u = generator(g, a, p)  
v = generator(g, b, p)  
key = generator(v, a, p)  
b_key = generator(u, b, p)  
shared_key = None  
if key == b_key:  
shared_key = key  
else:  
print("Invalid key")  
return  
semi_cipher = dynamic_xor_encrypt(plain_text, text_key)  
cipher = encrypt(semi_cipher, shared_key)  
print(f'cipher is: {cipher}')  
  
  
if __name__ == "__main__":  
message = sys.argv[1]  
test(message, "trudeau")
```

The working of Deffie Helman, shared parameters are `g` (generator) = `31` and `p` (prime modulus) = `97` which both the parties know. Private keys are `a = 97` (for one party) &  `b = 22` (for the other party), which are unknown to the other party.

Then public values are calculated using the formulas `u = g^a % p` and `v = g^b % p`.

Now when the public values are exchanged openly between the two parties, each party can use the public key obtained with their own private key to get a shared secret key (`key = v^a % p` and `key2 = u^b % p`). Both `key` and `key2` come out to be equal thus making a shared secret key.

The encryption logic follows the order of key generation, then XOR encryption, then plaintext encryption. So, the decryption logic would follow key generation, then reverse plaintext encryption, then reverse XOR decryption.

The encryption function is,
```
def encrypt(plaintext, key):
    cipher = []
    for char in plaintext:
        cipher.append(((ord(char) * key * 311)))
    return cipher
```

We will reverse it to get the decryption function.

The final program to get the reversed flag was, 
```
def generator(g, x, p):
    return pow(g, x, p)

def is_prime(p):
    if p < 2:
        return False
    for i in range(2, int(p**0.5) + 1):
        if p % i == 0:
            return False
    return True

def dynamic_xor_decrypt(ciphertext, text_key):
    plaintext = []
    key_length = len(text_key)
    for i, char in enumerate(ciphertext):
        key_char = text_key[i % key_length]
        decrypted_char = chr(ord(char) ^ ord(key_char))
        plaintext.append(decrypted_char)
    return ''.join(plaintext)

def decrypt(cipher, key, text_key):
    decrypted_chars = []
    for encrypted_char in cipher:
        decrypted_char = encrypted_char // (key * 311)
        decrypted_chars.append(chr(decrypted_char))
    semi_plaintext = ''.join(decrypted_chars)
    decrypted_message = dynamic_xor_decrypt(semi_plaintext, text_key)
    return decrypted_message

def test():
    p = 97
    g = 31

    a = 97
    b = 22

    cipher = [151146, 1158786, 1276344, 1360314, 1427490, 1377108, 1074816, 1074816, 386262, 705348, 0, 1393902, 352674, 83970, 1141992, 0, 369468, 1444284, 16794, 1041228, 403056, 453438, 100764, 100764, 285498, 100764, 436644, 856494, 537408, 822906, 436644, 117558, 201528, 285498]

    if not is_prime(p):
        print("p must be a prime number")
        return

    u = generator(g, a, p)
    v = generator(g, b, p)
    key = generator(v, a, p)
    b_key = generator(u, b, p)

    if key != b_key:
        print("Key mismatch")
        return

    decrypted_message = decrypt(cipher, key, "trudeau")
    print(f'Decrypted message: {decrypted_message}')
    print(f'Key: {key}')

test()
```
On running the program, we get `}7950354e_d6tp0rc2d_motsuc{FTCocip`. On reversing this, we finally get the flag `picoCTF{custom_d2cr0pt6d_e4530597}`.

**Learnt:** 
1. diffie hellman key exchange

**References:**
https://www.youtube.com/watch?v=Yjrfm_oRO0w
https://www.youtube.com/watch?v=NmM9HA2MQGI
https://www.geeksforgeeks.org/implementation-diffie-hellman-algorithm/

---

## miniRSA
**Flag: `picoCTF{n33d_a_lArg3r_e_ccaa7776}`**
**Perceived Difficulty:** `hard`

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
