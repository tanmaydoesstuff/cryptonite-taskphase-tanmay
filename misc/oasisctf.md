[Event GitHub Repo](https://github.com/Cryptonite-MIT/oasisCTF-2024)
Team: Finders of Flags
Challenges Solved: 23

## 4/Ready Player One
 A simple challenge, I recognised the file is trying to convey something in morse code and then used an [online decoder](https://morsecode.world/international/decoder/audio-decoder-adaptive.html) at `morsecode.world` to obtain the flag.
 
**Flag:** `OASIS{M0R5E_M4N1PU7470R}`

**Prerequisite Knowledge:** morse code and it's online decoder

## 5/Arte_Miss?
My first step on obtaining any image file was to use the online tool to check for metadata and other steganographic encoding within the image. For this, I always use the [online tool](https://aperisolve.fr)
`aperisolve.fr`. The flag was obtained within the metadata of the image.

**Flag:** `OASIS{m4ta_af_04515}`

**Prerequisite Knowledge:** image metadata & steg

## 6/String Me To Sleep?
The flag was simply present in plaintext in the file given and opening it using a text editor, then searching for it using Ctrl+F would give you the flag.

**Flag:** `OASIS{y0u_fou7d_m3}`

## 7/This or that?
The giveaway word `exclusively` from the challenge text led me to simply try to decode the given text with the `XOR` cipher using the [online tool](https://cryptii.com) `cryptii.com`. On decoding it with the provided key `ARTEMIS_WHISPER`, we get the decoded text which we need to put into the flag format of `OASIS{}`.

**Flag:** `OASIS{B17W153_MY573RY}`

**Prerequisite Knowledge:** XOR

## 8/Jekylle and Hide
On seeing a custom challenge website, I first explored around the sources, which led me to the first part of the flag `OASIS{th15_15_part1_`  in the `book.js` file. Then on entering this value as the required username, it asks us to enter a PIN. We find this PIN value to be `7928` from a comment in the `styles.css` file. On entering the PIN we obtain the second part of the flag `@nd_h3r3_go35_1h3_n3xt!!}`

**Flag:** `OASIS{th15_15_part1_@nd_h3r3_go35_1h3_n3xt!!}`

**Prerequisite Knowledge:** Exploring around the website source files using Inspect Element

## 9/Keynough is enough
The giveaway word here was `vinegar` pointing us toward the vignere cipher. We decrypt `KHAAH{W1C3J7_C1O3F3G3}` using the key `WHISPER` to obtain the flag.

**Flag:** `OASIS{S1L3N7_V1G3N3R3}`

**Prerequisite Knowledge:** Vignere and other ciphers

## 10/BasiKEllY
The `=` signs at the end of the encoded text `J5AVGSKTPNWDA53LGN4V65BQGBPXGMDGORPXG4BQNMZW4XZTNB6Q====` point us to either base-32 or base-64 encoding. We use an [online tool](https://www.dcode.fr/cipher-identifier) to identify the type of the cipher as base-32 and decrypt it to obtain the flag.

**Flag:** `OASIS{l0wk3y_t00_s0ft_sp0k3n_3h}`

**Prerequisite Knowledge:** Base 32 and other ciphers

## 11/Key-key do you love me?
Simply pretending that you knew the flag already led the bot to reveal what secret it was hiding and obtaining the flag.

**Flag:** `OASIS{thE_kEyPeR_oF_sECReTS_iS_kInDa_wEiRD_nGL}`

## 12/Knock Knock
Seeing an obfuscated QR, I first tried to upload it to a online QR code cleaner but realised that wouldn't work. 
Then I tried manipulating color saturation in the image using `aperisolve.fr` but that did not work either. I had the backup plan of recreating the entire QR code using `https://merri.cx/qrazybox/`, but ditched that approach since it would be time intensive. 
Then after trying to manipulate with the image contrast, I was able to scan the QR code, obtaining the flag. 

**Flag:** `OASIS{qu3u3r_r3c0v3r3r}`

**New Learnings:** 
- Recreation of QR code to bruteforce from scratch on [this website](https://merri.cx/qrazybox)
- How a QR code works and [when it's scannable or not](http://datagenetics.com/blog/november12013/index.html)

## 13/Microsoft StrongEdge
Obtaining the .pptx file, I looked around for hidden text. Then I converted the .pptx file to images and tried to look for hidden steganographic data using `aperisolve.fr`. I was able to find an encoded text, which I recognised as `ROT-13` eventually, and then decoding it, I was able to obtain the flag.

**Flag:** `OASIS{r0t4t0r_0f_pp75}`

**Prerequisite Knowledge:** steganographic data, ROT-13

## 14/Maze Runner
Scanning the image using Google Lens, we obtained the name of the place and it's location to be `Fontanellato`, and reading up on it's Wikipedia, we learn that it was ideated by someone with the surname `Borges`.

Flag: `OASIS{Borges_Fontanellato}`

## 15/Not Noice
On reading `hidden within the noise` in the challenge text, my first thought with this image was spectrogram analysis using Audacity, having done a similar challenge before. This gave us the flag in plaintext written in audio.

**Flag:** `OASIS{5P3CT0GR4M_15_TH3_C00L35T}`

**Prerequisite Knowledge:** Audacity, Spectrogram analysis

## 16/Nom-nom
A very simple cookie manipulation challenge, which was evidently clear by the challenge name `nom-nom`. We simply had to change the cookie value of admin to true and obtain the flag.

**Flag:** `OASIS{p@cman_l!k3d_y0ur_c00k!3_<3}`

**Prerequisite Knowledge:** Cookie manipulation

## 17/Quench Your Thirst
Using the cipher identifier on `dcode.fr` was inconclusive, but then I tried to use `boxentriq.com` which was able to identify it as monoalphabetic substitution. We were able to decrypt the instructions but the link at the end could not completely be decoded because of the lack of subsititution letters for 4 alphabets used in the link.

We obtained the link as `https://katb.in/y_n_o_q_`. Now we had to permutate the remaining 4 letters in the spaces to obtain the link as [https://katb.in/yxnzorqj](https://katb.in/YXNZORQJ). The link help us obtain the flag.

**Flag:** `OASIS{fr3qu3nt_j4!l_br34k1ng_m4k3s_!t_t00_3asy_fr}`

**New Learnings:** 
- Permutations to bruteforce stuff

## 18/Git gud
For this challenge, you make the .7z file into a .zip then extract it. This gave us a git repo. Reading the git log of the file to know more about the commits gives us the flag in reverse.

**Flag:** `OASIS{g3t_gu663r_4t_g!t!!}`

## 19/Stairway to Heaven
Was stuck at the challenge for hours, but I obtained the flag when my teammate ran the program on his machine running on Kali. Surprisingly there was a blob of text at the end of the colorful pattern, copying which helped me obtain the flag. Call it bruteforcing, or just pure luck (as the admins did!).

Later I learnt the actual solution, which was to decode the background text, and after decrypting that, we were able to obtain the flag written in ASCII.

**Flag:** `OASIS{v34y_f4ncy_AN5I}`

## 20/Firewall Jail
After exploring the sources page, I simply tried to manipulate the userid to `6` to see how the page would respond, which gave us the flag.

**Flag:** `OASIS{T00_m4ny_h0urs_4nd_wh3r3_d1d_1t_l34d}`

**Prerequisite Knowledge:** URL Parameters

## 21/Heads up, tails down
Solving this took me an incredibly long time, but the challenge was incredibly simple. As the title suggested, we had to edit the `header` of the corrupted file to the standard header of a `.png` file. On googling I figured out the `header` portion to be standard as `89 50 4E 47 0D 0A 1A 0A`.

But this didn't uncorrupt the file, as I was forgetting to add the `IHDR` parameter, which I added later. In the meanwhile, I downloaded and was using a hexeditor locally called `Bless`. Somehow, it wasn't able to output the right file, as it was overwriting some of the original file instead of appending to fix the header. 
Using an online hexeditor at [this website](https://hexed.it) worked (after also making the mistake of adding an extra byte in the header, I had to redo it again) and I was able to obtain the flag by uncorrupting the png file.

**Flag:** `OASIS{h34d_c4rr135}`

**New Learnings:** 
- PNG files, and editing their structure
- Standard File Headers

## 22/All that for a drop of blud?
Initially seeing the `agent` hint, I tried to manipulate the `UserAgent` to `OASISPlayer` as specified in the challenge by using Inspect Element (Ctrl+Shift+I). But since that didn't help. I installed `curl` and executed commands on the terminal. 
First we POST request on `/game`, which asked to add the name of the Student Project organising the CTF in the `name` parameter using URL manipulation.
Then you send a POST request to `/game?name=cryptonite`, which asks to add a `rank` parameter with the CTFTime rank of cryptonite, in the `rank` parameter. It also specifies to POST request on `/givemetheFlag`. Hence POST on `/givemetheFlag?name=cryptonite&rank=4` would give us the flag.

**Flag:** `OASIS{G37_d035_n07_4lw4y5_G37_th1ng5}`

**Prerequisite Knowledge:** URL parameters, User Agents

**New Learnings:**
- curl, POST requests

## 23/A Rocky Start
Initially I was trying to use Ghidra or some tool to decompile the game file, but couldn't understand much there. Then I tried to learn a bit more about overflow attacks, and it mentioned about overflowing the input to spill into the output. In this case, `name` would have been the only possible input (to flow into the `points` parameter), so I tried to overflow it with different long texts of length greater than 20 chars, and was able to obtain the flag after 2-3 tries.

**Flag:** `OASIS{D0DG3_4LL_TH3_R0CK5_0R_N07}`

**New Learnings:**
- Basic working of Overflow Attacks
- Using decompiler tools like [dogbolt.io](https://dogbolt.io) and Ghidra


