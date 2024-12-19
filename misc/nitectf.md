[Event CTFTime](https://ctftime.org/event/2461)
[Event GitHub Repo](https://github.com/Cryptonite-MIT/niteCTF-2024)
Team: [`OrangeMangoLemon`](https://ctftime.org/team/366126) 
Challenges Solved: 1

### 1/WebExp/Ooguth
**Solved: `No`**
**Approach:** With this challenge, my initial thought was to try to login into Oogle. For that, I found out that the password field on the login page was not being validated (i.e, you could set the username to whatever you wanted to, with any password, then going back to the home page would log you in). My approach here was to try to find a "superuser" or "admin" account, and then try to find endpoints on the Oogle website, which might give the flag. Also noted that the session cookie could be manipulated to act as any user on Oogle.

**Actual Solution:** "Find `/.well-known/openid-configuration` on Oogle server, look at the scopes provided, and enable `payment_details` scope. Then login to YankDeal and go to `/payment_details` endpoint."

### 2/Bin/Chaterine
**Solved: `No`**
**Approach:** I tried to obtain the memory address of the buffer but couldn't understand how to. I later learnt of format strings while solving the assigned TP challenge, but didn't end up trying it during the CTF. A remarkable flunk.

**Actual Solution:** "A tcache poison is possible to get a pointer in the stack We can leak a stack address using a "%p" payload with a format string in the first input.

Calculate the address of buffer
We need to calculate the offset of the required buffer from the leaked address. For this step you need to run a debugger on the binary in the docker. You can then see that the offset is 0x2130. Let the buffer address be stack_addr.

Creating 2 chunks
Use the new message option to create 2 chunks using malloc.

Free the chunks to push them into the tcache
Use the delete message option to free the chunks.

Leaking heap address
Use the format string exploit in the Write message function to leak the heap address of the latest freed chunk. One of the usable payloads can be "%p %p %p". This will leak the address of the chunk you are writing onto in the third spot.

Poison the chunk in tcache
Now we have a heap address and the address of the buffer. We can poison the tcache using the write message option and the payload p64(stack_addr ^ (heap_leak>>12)).

Create 2 new chunks
We create 2 new chunks again with the new message option.The first chunk will be allocated in the heap but the second allocation will have a pointer to the buffer address.

Write spiderdrive onto the buffer
Use the write option onto the buffer to write spiderdrive.

Clear the strcmp check
With the buffer now being written we can then try getting the admin acces which will be easily given to us and now we have shell access.
```cat flag.txt```
"

### 3/Forensics/Buckspeak
**Solved: `No`**
**Approach:** Initially I tried to play the .wav file in audacity, and try manipulating it a bit. Then I tried to search the web for encoding techniques that could be related to the challenge description and found the bucking cipher. I wasn't able to proceed further into the challenge.

**Actual Solution:** "The WAV file contains a deepsound password that is encoded using the bucking cipher. The notes have to extracted from the wav file and according to the cipher mapping, they need to be mapped to the corresponding letters of the alphabet. Use the first part of the [solvescript](https://github.com/Cryptonite-MIT/niteCTF-2024/blob/main/forensics/buckspeak/solution/solve.py) for this. This audio file and the password from the previous step need to be put into deepsound to extract an mkv file. Using `./mkvextract-helper.sh -f screech.mkv -tavsc` we can extract the subtitles and the custom font used. Then using a script similar to the one given in the solvescript rendered using the custom font which will give the flag. Use the second part of the solvescript for this"

### 4/Crypto/RSAbc
**Solved: `No`**
**Approach:** Tried to understand the given RSA script and understood that I had to map the greek letters back to the english counterparts using `language` dictionary given. I was able to do this. Then I proceeded to try to reverse the `alphabet` and `googly` transformations, but was unable to do so.

**Actual Solution:** "Take the Greek characters, map them to the corresponding English equivalents as given in the encryption, and then flip the bit of the `ciphertext` at the position `length - ASCII value of the corresponding English character`.
After flipping the bit, perform RSA to obtain one character, and loop through the process to perform RSA on all the Greek characters. For non-Greek characters, simply use the same reverse alphabet function."



NOTE: All actual solutions credits go to the organisers respective [challenge writeup authors](https://github.com/Cryptonite-MIT).