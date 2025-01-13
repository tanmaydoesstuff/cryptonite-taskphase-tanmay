# forensics
## blast from the past
**Flag: `picoCTF{71m3_7r4v311ng_p1c7ur3_3e336564}`**
**Perceived Difficulty:** `medium`

In this challenge, we're asked to edit the image creation timestamp to a given timestamp (which notably is as close to the start of Unix timestamps as possible).

Here's the terminal snippet:
```
tnmyhere@luna-mint:~/Desktop/pico/blastfrompast$ exiftool original.jpg
ExifTool Version Number         : 12.76
File Name                       : original.jpg
Directory                       : .
File Size                       : 2.9 MB
File Modification Date/Time     : 2025:01:12 11:42:32+05:30
File Access Date/Time           : 2025:01:12 11:48:21+05:30
File Inode Change Date/Time     : 2025:01:12 11:48:27+05:30
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
Exif Byte Order                 : Little-endian (Intel, II)
Image Description               : 
Make                            : samsung
Camera Model Name               : SM-A326U
Orientation                     : Rotate 90 CW
X Resolution                    : 72
Y Resolution                    : 72
Resolution Unit                 : inches
Software                        : MediaTek Camera Application
Modify Date                     : 2023:11:20 15:46:23
Y Cb Cr Positioning             : Co-sited
Exposure Time                   : 1/24
F Number                        : 1.8
Exposure Program                : Program AE
ISO                             : 500
Sensitivity Type                : Unknown
Recommended Exposure Index      : 0
Exif Version                    : 0220
Date/Time Original              : 2023:11:20 15:46:23
Create Date                     : 2023:11:20 15:46:23
Components Configuration        : Y, Cb, Cr, -
Shutter Speed Value             : 1/24
Aperture Value                  : 1.9
Brightness Value                : 3
Exposure Compensation           : 0
Max Aperture Value              : 1.8
Metering Mode                   : Center-weighted average
Light Source                    : Other
Flash                           : On, Fired
Focal Length                    : 4.6 mm
Sub Sec Time                    : 703
Sub Sec Time Original           : 703
Sub Sec Time Digitized          : 703
Flashpix Version                : 0100
Color Space                     : sRGB
Exif Image Width                : 4000
Exif Image Height               : 3000
Interoperability Index          : R98 - DCF basic file (sRGB)
Interoperability Version        : 0100
Exposure Mode                   : Auto
White Balance                   : Auto
Digital Zoom Ratio              : 1
Focal Length In 35mm Format     : 25 mm
Scene Capture Type              : Standard
Compression                     : JPEG (old-style)
Thumbnail Offset                : 1408
Thumbnail Length                : 64000
Image Width                     : 4000
Image Height                    : 3000
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Time Stamp                      : 2023:11:21 02:16:21.420+05:30
MCC Data                        : United States / Guam (310)
Aperture                        : 1.8
Image Size                      : 4000x3000
Megapixels                      : 12.0
Scale Factor To 35 mm Equivalent: 5.4
Shutter Speed                   : 1/24
Create Date                     : 2023:11:20 15:46:23.703
Date/Time Original              : 2023:11:20 15:46:23.703
Modify Date                     : 2023:11:20 15:46:23.703
Thumbnail Image                 : (Binary data 64000 bytes, use -b option to extract)
Circle Of Confusion             : 0.006 mm
Field Of View                   : 71.5 deg
Focal Length                    : 4.6 mm (35 mm equivalent: 25.0 mm)
Hyperfocal Distance             : 2.13 m
Light Value                     : 4.0
```

Now we try to update dates using the command I found by [looking up online](https://stackoverflow.com/questions/78566217/modify-timestamp-with-exiftool-with-milliseconds) and then submitting the image,
```
tnmyhere@luna-mint:~/Desktop/pico/blastfrompast$ exiftool -SubSecCreateDate='1970:01:01 00:00:00.001' -SubSecDateTimeOriginal='1970:01:01 00:00:00.001' -SubSecModifyDate='1970:01:01 00:00:00.001' original.jpg
    1 image files updated
tnmyhere@luna-mint:~/Desktop/pico/blastfrompast$ nc -w 2 mimas.picoctf.net 64394 < original.jpg
tnmyhere@luna-mint:~/Desktop/pico/blastfrompast$ nc mimas.picoctf.net 56479
MD5 of your picture:
eb5ae92ce9f801b9d1aa8e4c800e9705  test.out

Checking tag 1/7
Looking at IFD0: ModifyDate
Looking for '1970:01:01 00:00:00'
Found: 1970:01:01 00:00:00
Great job, you got that one!

Checking tag 2/7
Looking at ExifIFD: DateTimeOriginal
Looking for '1970:01:01 00:00:00'
Found: 1970:01:01 00:00:00
Great job, you got that one!

Checking tag 3/7
Looking at ExifIFD: CreateDate
Looking for '1970:01:01 00:00:00'
Found: 1970:01:01 00:00:00
Great job, you got that one!

Checking tag 4/7
Looking at Composite: SubSecCreateDate
Looking for '1970:01:01 00:00:00.001'
Found: 1970:01:01 00:00:00.001
Great job, you got that one!

Checking tag 5/7
Looking at Composite: SubSecDateTimeOriginal
Looking for '1970:01:01 00:00:00.001'
Found: 1970:01:01 00:00:00.001
Great job, you got that one!

Checking tag 6/7
Looking at Composite: SubSecModifyDate
Looking for '1970:01:01 00:00:00.001'
Found: 1970:01:01 00:00:00.001
Great job, you got that one!

Checking tag 7/7
Timezones do not have to match, as long as it's the equivalent time.
Looking at Samsung: TimeStamp
Looking for '1970:01:01 00:00:00.001+00:00'
Found: 2023:11:20 20:46:21.420+00:00
Oops! That tag isn't right. Please try again.
```

This doesn't work however. There is still a tag we need to update to prove the file is originally from the given date. On [reading up](https://stackoverflow.com/questions/78185037/how-to-edit-the-samsung-trailer-tag-timestamp) a bit more, we find that samsung trailing tag `TimeStamp` can be modified by directly editing the hex data.

We use the trusty [hexed.it]() for this. We find that in the trailing end of the file, there is something indicating `UTC_Data`, and it is indeed the timestamp we need to edit.

We edit it to the required unix timestamp of `00001` and obtain pass the checks to obtain the flag.
```
tnmyhere@luna-mint:~/Desktop/pico/blastfrompast$ nc -w 2 mimas.picoctf.net 61378 < final.jpg
tnmyhere@luna-mint:~/Desktop/pico/blastfrompast$ nc mimas.picoctf.net 56337
MD5 of your picture:
4b34e5904b55a0ff20029af28204a9a5  test.out

Checking tag 1/7
Looking at IFD0: ModifyDate
Looking for '1970:01:01 00:00:00'
Found: 1970:01:01 00:00:00
Great job, you got that one!

Checking tag 2/7
Looking at ExifIFD: DateTimeOriginal
Looking for '1970:01:01 00:00:00'
Found: 1970:01:01 00:00:00
Great job, you got that one!

Checking tag 3/7
Looking at ExifIFD: CreateDate
Looking for '1970:01:01 00:00:00'
Found: 1970:01:01 00:00:00
Great job, you got that one!

Checking tag 4/7
Looking at Composite: SubSecCreateDate
Looking for '1970:01:01 00:00:00.001'
Found: 1970:01:01 00:00:00.001
Great job, you got that one!

Checking tag 5/7
Looking at Composite: SubSecDateTimeOriginal
Looking for '1970:01:01 00:00:00.001'
Found: 1970:01:01 00:00:00.001
Great job, you got that one!

Checking tag 6/7
Looking at Composite: SubSecModifyDate
Looking for '1970:01:01 00:00:00.001'
Found: 1970:01:01 00:00:00.001
Great job, you got that one!

Checking tag 7/7
Timezones do not have to match, as long as it's the equivalent time.
Looking at Samsung: TimeStamp
Looking for '1970:01:01 00:00:00.001+00:00'
Found: 1970:01:01 00:00:00.001+00:00
Great job, you got that one!

You did it!
picoCTF{71m3_7r4v311ng_p1c7ur3_3e336564}
```

---
## mob psycho
**Flag: `picoCTF{ax8mC0RU6ve_NX85l4ax8mCl_b112ae57}`**
**Perceived Difficulty:** `medium`

In this challenge, we are given an `.apk` file. On reading up about it, we find out that these files can be unzipped, so we proceed with that.

We find a lot of directories and files, and try to look up for anything relevant within these files. We find an interesting file at `/mobpsycho/res/color/flag.txt`.

This file contains `7069636f4354467b6178386d433052553676655f4e5838356c346178386d436c5f62313132616535377d`. Converting this from hex to text gives us the flag `picoCTF{ax8mC0RU6ve_NX85l4ax8mCl_b112ae57}`

---
## St3g0
**Flag: `picoCTF{7h3r3_15_n0_5p00n_a9a181eb}`
Perceived Difficulty:** `easy`

In this challenge, we are given a transparent image file. We obtain the flag on trying to use `zsteg` with lsb encoding using `aperisolve.fr`.

Edit: Later on reading up about this while revising, it turned out the challenge title was pointing to lsb encoding.

---
## MSB
**Flag: `picoCTF{15_y0ur_que57_qu1x071c_0r_h3r01c_572ad5fe}`
Perceived Difficulty:** `medium`

We see an image where a certain section of it has clearly flipped in color, meaning some corruption in data. The challenge name pointed us to `msb` or `most significant bit`. My initial check was to use `zsteg`, but that wasn't able to help me obtain the flag. 

On searching for MSB steganography decoders online, I find [this](https://georgeom.net/StegOnline/extract). Initially after trying out many options including strings, etc. I hit the jackpot while using the `extract files` option. I checked with MSB bit order decoding, and checking `7` as most significant bit, I was able to obtain some text.

![](/media/forensics_msb_1.png)

We download the extracted data to obtain the flag `picoCTF{15_y0ur_que57_qu1x071c_0r_h3r01c_572ad5fe}`.

---
## PcapPoisoning
**Flag: `picoCTF{P64P_4N4L7S1S_SU55355FUL_0f2d7dc9}`**
**Perceived Difficulty:** `easy`

A very lucky solve, simply trying to search for the flag in plaintext using `$ strings trace.pcap | grep picoCTF{` gave the flag.

---
## hide me
**Flag: `picoCTF{Hiddinng_An_imag3_within_@n_ima9e_82101824}`**

Obtained the flag using `steghide`, where I found a folder `secret` embedded which had an image that had the flag.

---
## c0rrupt
**Flag: `picoCTF{c0rrupt10n_1847995}`**
**Perceived Difficulty:** `medium`

In this challenge, we are given a `mystery` file. We use `$ file` but that doesn't lead us anywhere. 

We look at the hint given, and it says to "fix the header". On inspecting the file header, one might realise it looks something like the PNG file header. So we try changing it to the PNG header format, by comparing it to an existing PNG header. Based on my existing knowledge of png headers, I edit the IHDR chunk name, IDAT chunk name and the initial header to declare `PNG`.

![](/media/forensics_c0rrupt_1.png)

This still doesn't work and the file is unopenable. On running the `pngcheck` program we find out that `mystery.png` has the following error:
```
$ pngcheck mystery.png
zlib warning:  different version (expected 1.2.13, using 1.3)

mystery.png  CRC error in chunk pHYs (computed 38d82c82, expected 495224f0)
ERROR: mystery.png
```

On closer inspection, I had missed updating some bytes, which was declaring a very large size for the IDAT chunk (the byte before the chunk name declares it's size). We change it to `00 00`.

Now the file opens and we obtain the flag.

---
## WhitePages
**Flag: `picoCTF{not_all_spaces_are_created_equal_7100860b0fa779a5bd8ce29f24f586dc}`**
**Perceived Difficulty:** `medium`

In this challenge, when we open the file we are given, we see that it is all blank, but on running `$ file` we learn that it indeed does have some data. 
So naturally, we try reading the file in some other format, like using hexedit.

![](/media/forensics_whitepages_1.png)

On carefully seeing the file, we find out that the text is a repeating sequence of two sequences `E2 80 83` and `20`, interspaced with spaces and newline characters. The fact that it is two sequences gives us a hint that it might be binary data. So we convert it into such, keeping the first sequence type as `0` and second as `1`. 

We get the following data,
```
00001010000010010000100101110000011010010110001101101111010000110101010001000110000010100000101000001001000010010101001101000101010001010010000001010000010101010100001001001100010010010100001100100000010100100100010101000011010011110101001001000100010100110010000000100110001000000100001001000001010000110100101101000111010100100100111101010101010011100100010000100000010100100100010101010000010011110101001001010100000010100000100100001001001101010011000000110000001100000010000001000110011011110111001001100010011001010111001100100000010000010111011001100101001011000010000001010000011010010111010001110100011100110110001001110101011100100110011101101000001011000010000001010000010000010010000000110001001101010011001000110001001100110000101000001001000010010111000001101001011000110110111101000011010101000100011001111011011011100110111101110100010111110110000101101100011011000101111101110011011100000110000101100011011001010111001101011111011000010111001001100101010111110110001101110010011001010110000101110100011001010110010001011111011001010111000101110101011000010110110001011111001101110011000100110000001100000011100000110110001100000110001000110000011001100110000100110111001101110011100101100001001101010110001001100100001110000110001101100101001100100011100101100110001100100011010001100110001101010011100000110110011001000110001101111101000010100000100100001001 
```

We convert this to text, and there hence obtain the flag.

---
## File types
**Flag: `picoCTF{f1len@m3_m@n1pul@t10n_f0r_0b2cur17y_79b01c26}`**
**Perceived Difficulty:** `medium`

In this challenge, we are given a pdf file. My initial guess was to open this in a 
Then I remembered that I had read that PDF files are actually executable files. So given that, I tried renaming it to `.sh` and running it but that did not work. However when I ran `$ file` on it, it showed it as `shell archive text`. So I tried reading it in a text editor to find some instructions in comments of the file.

```# This is a shell archive (produced by GNU sharutils 4.15.2).
# To extract the files from this archive, save it to some FILE, remove
# everything before the '#!/bin/sh' line above, then type 'sh FILE'.
#
lock_dir=_sh00046
# Made on 2023-03-16 01:40 UTC by <root@e023b7dee37c>.
# Source directory was '/app'.
#
# Existing files will *not* be overwritten, unless '-c' is specified.
#
# This shar contains:
# length mode       name
# ------ ---------- ------------------------------------------
#   1092 -rw-r--r-- flag
#
```

So I saved it as a `.sh` file and ran it on terminal.
```
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ ./flag.sh
x - created lock directory _sh00046.
x - extracting flag (text)
./flag.sh: 119: uudecode: not found
restore of flag failed
flag: MD5 check failed
x - removed lock directory _sh00046.
```

So to install `uudecode` required for running it, I installed `sharutils`. And well, what followed was something special I've never witnessed before.
```
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ mv Flag.pdf flag.sh
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ ./flag.sh
bash: ./flag.sh: Permission denied
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ sudo ./flag.sh
[sudo] password for tnmyhere:            
sudo: ./flag.sh: command not found
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ ./flag.sh
x - created lock directory _sh00046.
x - extracting flag (text)
./flag.sh: 119: uudecode: not found
restore of flag failed
flag: MD5 check failed
x - removed lock directory _sh00046.
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ sudo apt install sharuti;s
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
E: Unable to locate package sharuti
s: command not found
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ sudo apt install sharutils
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Suggested packages:
  bsd-mailx | mailx sharutils-doc
The following NEW packages will be installed:
  sharutils
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 259 kB of archives.
After this operation, 1,276 kB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu noble/universe amd64 sharutils amd64 1:4.15.2-9 [259 kB]
Fetched 259 kB in 2s (143 kB/s)     
Selecting previously unselected package sharutils.
(Reading database ... 506505 files and directories currently installed.)
Preparing to unpack .../sharutils_1%3a4.15.2-9_amd64.deb ...
Unpacking sharutils (1:4.15.2-9) ...
Setting up sharutils (1:4.15.2-9) ...
Processing triggers for install-info (7.1-3build2) ...
Processing triggers for man-db (2.12.0-4build2) ...
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ ./flag.sh
x - created lock directory _sh00046.
x - extracting flag (text)
x - removed lock directory _sh00046.
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ ls
flag  flag.sh
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ file flag
flag: current ar archive
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ man ar
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ ar -x flag
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ ls
flag  flag.sh
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ file flag
flag: cpio archive; device 234, inode 37426, mode 100644, uid 0, gid 0, modified Thu Mar 16 01:40:17 2023, 510 bytes "flag"
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ man cpio
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ cpio --file flag --extract
cpio: flag not created: newer or same age version exists
2 blocks
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ mv flag flagold
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ cpio --file flagold --extract
2 blocks
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ file flag
flag: bzip2 compressed data, block size = 900k
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ man bzip2
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ bunzip2 flag
bunzip2: Can't guess original name for flag -- using flag.out
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ file flag.out
flag.out: gzip compressed data, was "flag", last modified: Thu Mar 16 01:40:17 2023, from Unix, original size modulo 2^32 327
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ man gzip
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ gunzip flag.out
gzip: flag.out: unknown suffix -- ignored
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ mv flag.out flag.gz
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ gunzip flag.gz
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ ls
flag  flagold  flag.sh
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ rm flagold
tnmyhere@luna-mint:~/Desktop/pico/filetypes$ file flag
flag: lzip compressed data, version: 1
.
.
.
.
.
.

```

Finally, we reach the end of this dark lonely tunnel of archive file formats. Totally did not expect this.
![](/media/forensics_filetypes_1.png)

We decode the hex to text to obtain the flag, `picoCTF{f1len@m3_m@n1pul@t10n_f0r_0b2cur17y_79b01c26}`

---
## Sleuthkit Intro
**Flag: `picoCTF{mm15_f7w!}`**
**Perceived Difficulty:** `easy`

A simple challenge with an introduction to SleuthKit for Disk Image analysis. We open the give GNU zip file, unzip it, and then obtain the flag by using the mentioned `mmls` command.

Here is the terminal snippet,
```
tnmyhere@luna-mint:~/Desktop/pico/sleuth$ ls
disk.img.gz
tnmyhere@luna-mint:~/Desktop/pico/sleuth$ file disk.img.gz
disk.img.gz: gzip compressed data, was "disk.img", last modified: Tue Sep 21 19:34:53 2021, from Unix, original size modulo 2^32 104857600
tnmyhere@luna-mint:~/Desktop/pico/sleuth$ man gzip
tnmyhere@luna-mint:~/Desktop/pico/sleuth$ gunzip disk.img.gz
tnmyhere@luna-mint:~/Desktop/pico/sleuth$ ls
disk.img
tnmyhere@luna-mint:~/Desktop/pico/sleuth$ file disk.img
disk.img: DOS/MBR boot sector; partition 1 : ID=0x83, active, start-CHS (0x0,32,33), end-CHS (0xc,190,50), startsector 2048, 202752 sectors
tnmyhere@luna-mint:~/Desktop/pico/sleuth$ mmls disk.img
DOS Partition Table
Offset Sector: 0
Units are in 512-byte sectors

      Slot      Start        End          Length       Description
000:  Meta      0000000000   0000000000   0000000001   Primary Table (#0)
001:  -------   0000000000   0000002047   0000002048   Unallocated
002:  000:000   0000002048   0000204799   0000202752   Linux (0x83)
tnmyhere@luna-mint:~/Desktop/pico/sleuth$ nc saturn.picoctf.net 63223
What is the size of the Linux partition in the given disk image?
Length in sectors: 0000202752
0000202752
Great work!
picoCTF{mm15_f7w!}
```

---
---
---
# web

## client-side-again
**Flag: `picoCTF{not_this_again_50a029}`
Perceived Difficulty:** `easy`

In this challenge, in the `script.js` file, we see the password being checked in various steps to allow us to enter the web portal. By reverse engineering the positions of phrases, we get the flag.

---
## jAuth
**Flag: `picoCTF{succ3ss_@u7h3nt1c@710n_72bf8bd5}`**
**Perceived Difficulty:** `medium`

In this challenge, we are provided with a web portal. We login using the test credentials provided in the challenge to notice that whenever a user logs in, a cookie is added.
On further inspection we find that it is a decodable `JWT` cookie, so I try looking for exploits  related to it.

So JWT cookies basically have 3 parts: the header, the payload and the signature, resembling the format AAA.BBB.CCC. with three base 64 encoding chunks.
So to login here, we need to edit the JWT token (yes, they're user editable!). We set the user to `admin`,and remove it's third part (the signature) while also changing the encrypting algorithm in the header to `none`. This is a vulnerability with JWT tokens, as they can be left unsigned as well. On refreshing the page, we obtain the flag.

**References:**
https://fusionauth.io/dev-tools/jwt-decoder
https://portswigger.net/web-security/jwt

**Challenges:**
I was forgetting to remove the signature part of the JWT while editing only the header (RIP my stupidity) so it was always showing it as an incorrect signature, even thought I had changed the encrypting algorithm to none, hence not allowing me to login.

---
## MatchTheRegex
**Flag: `picoCTF{succ3ssfully_matchtheregex_2375af79}`**
**Perceived Difficulty:** `easy`

In this challenge, we initially use Inspect to find that website source file `script.js` has a comment `^p.....F!?`. This is probably hinting us to the name of the competition, `picoCTF`, which is also a part of the flag. So after trying to understand the `script.js` file, we input `picoCTF` in the specified field, which gives us the flag.

---
## search source
**Flag: `picoCTF{1nsp3ti0n_0f_w3bpag3s_ec95fa49}`**
**Perceived Difficulty:** `easy`

A simple challenge, where on checking the source, we find an image called `transparent.png`. But on further inspection, this image doesn't lead anywhere. We search for `picoCTF` in source files, and find the flag in `style.css`. 

---
## secrets
**Flag: `picoCTF{succ3ss_@h3n1c@10n_39849bcf}`**
**Perceived Difficulty:** `easy`

We try to look into the source files, in search of a `secret` file. But alas, we don't find anything. Then looking at the hint, we try looking into the folder `/secret/`. (I was making the stupid mistake of just typing `/secret` which obviously didn't work because this was a directory and not a backlink URL). Within this folder, we find another folder `hidden`. Got it, this is a maze tunnel thingy (`/secret/hidden/`). Then `/secret/hidden/superhiddenflag/` is found. Flag is obtained on selecting all the text on that webpage as it is the same color as the background. 

---
## it is my birthday
**Flag: `picoCTF{c0ngr4ts_u_r_1nv1t3d_aad886b9}`**
**Perceived Difficulty:** `medium`

 The description mentions about having the "same MD5 hash, but slightly different". On reading up, I learnt this: "MD5 is calculated exclusively from the contents of the file, and as such, two identical files must have the same MD5 hash. (The opposite does not hold true, however: it is possible for two different files to have the same MD5 hash)."
 
This led me to find out more about files with same md5 hashes, which led me to [this website](https://www.mscs.dal.ca/~selinger/md5collision/) describing what's called `md5 hash collision`.
We try using the files `hello.pdf` and `erase.pdf`, which is one of the examples of such a collision, which outputs the complete PHP code also containing the flag.

(Sidenote: This was such a cool challenge! Interesting stuff like this is why I love computers and electronics.)

---
## picobrowser
**Flag: `picoCTF{p1c0_s3cr3t_ag3nt_e9b160d0}`**
**Perceived Difficulty:** `easy`

Another challenge that doesn't deserve it's own writeup. We need to change the user-agent to `PicoBrowser` which can be done through an extension or in Chrome.
