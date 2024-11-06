## Trivial Flag Transfer Protocol

**Flag: `picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}`**
**Difficulty:** `medium`

There is a file given called `tftp.pcapng`. On reading up about [pcapng](https://pcapng.com/) file format, we learn that it is a packet capture file containing multiple other files, which can be read using Wireshark. On loading the file into Wireshark, we see the following files,

![](/media/forensics_tftp_1.png)

Then, to extract these files, use the `TFTP` (as indicated by the name of the challenge `Trivial Flag Transfer Protocol`). You can do that by going to `File > Export > TFTP`.

![](/media/forensics_tftp_2.png)

We obtain the following files,

![](/media/forensics_tftp_3.png)

`INSTRUCTIONS.txt`:`GSGCQBRFAGRAPELCGBHEGENSSVPFBJRZHFGQVFTHVFRBHESYNTGENAFSRE.SVTHERBHGNJNLGBUVQRGURSYNTNAQVJVYYPURPXONPXSBEGURCYNA`
`PLAN` file: `VHFRQGURCEBTENZNAQUVQVGJVGU-QHRQVYVTRAPR.PURPXBHGGURCUBGBF`

On opening the `program.deb` file with `eddy`, we learn that it is a tool called `steghide`. 
![](/media/forensics_tftp_4.png)

There are also 3 images, and since `steghide` is a steganography hiding tool, we might require a key.

The text files are both clearly encrypted, and using dcode.fr's [Cipher Identifier](https://www.dcode.fr/cipher-identifier), we identify the cipher as ROT-13.

Decrypting the files:
`PLAN`: `IUSEDTHEPROGRAMANDHIDITWITH-DUEDILIGENCE.CHECKOUTTHEPHOTOS`
`INSTRUCTIONS.txt`: `TFTPDOESNTENCRYPTOURTRAFFICSOWEMUSTDISGUISEOURFLAGTRANSFER.FIGUREOUTAWAYTOHIDETHEFLAGANDIWILLCHECKBACKFORTHEPLAN`

We understand that the key would be `DUEDILIGENCE` to find the hidden data in the images. 

```tnmyhere@luna-popos:~/Desktop/tftp$ steghide extract -sf picture1.bmp -p DUEDILIGENCE
steghide: could not extract any data with that passphrase!
tnmyhere@luna-popos:~/Desktop/tftp$ steghide extract -sf picture2.bmp -p DUEDILIGENCE
steghide: could not extract any data with that passphrase!
tnmyhere@luna-popos:~/Desktop/tftp$ steghide extract -sf picture3.bmp -p DUEDILIGENCE
wrote extracted data to "flag.txt".
```

We obtain the flag in the `flag.txt` file.

**Learnt:** 
1. wireshark
2. steghide

**Challenges:**
1. It was unclear on how to extract the files from Wireshark, but I later realised that TFTP is a protocol that could be used.

**References:**
https://pcapng.com/
https://wiki.wireshark.org/Development/PcapNg
https://www.kali.org/tools/steghide/
steghide man page


---

## tunn3l_v1s10n

**Flag: `picoCTF{qu1t3_a_v13w_2020}`
Difficulty:** `medium`

We are given a file called `tunn3l_v1s10n` but the filetype is not identifiable.

```
$ file /home/tnmyhere/Desktop/tunn3l_v1s10n
/home/tnmyhere/Desktop/tunn3l_v1s10n: data
```

On trying to open the file, we are displayed with the following error message.

![](/media/forensics_tunnelvision_1.png)

This gives a clue that this is a `.bmp` file and we need to edit the header. On reading up about the `.bmp` fileformat and header sizes, we can edit the hex using [Hexedit](https://hexed.it)

We understand the file details using exiftool and obtain the following,
```
$ exiftool /home/tnmyhere/Desktop/tunn3l_v1s10n
ExifTool Version Number         : 12.40
File Name                       : tunn3l_v1s10n
Directory                       : /home/tnmyhere/Desktop
File Size                       : 2.8 MiB
File Modification Date/Time     : 2024:11:06 13:48:23+05:30
File Access Date/Time           : 2024:11:06 13:48:22+05:30
File Inode Change Date/Time     : 2024:11:06 13:48:25+05:30
File Permissions                : -rw-r--r--
File Type                       : BMP
File Type Extension             : bmp
MIME Type                       : image/bmp
BMP Version                     : Unknown (53434)
Image Width                     : 1134
Image Height                    : 306
Planes                          : 1
Bit Depth                       : 24
Compression                     : None
Image Length                    : 2893400
Pixels Per Meter X              : 5669
Pixels Per Meter Y              : 5669
Num Colors                      : Use BitDepth
Num Important Colors            : All
Red Mask                        : 0x27171a23
Green Mask                      : 0x20291b1e
Blue Mask                       : 0x1e212a1d
Alpha Mask                      : 0x311a1d26
Color Space                     : Unknown (,5%()
Rendering Intent                : Unknown (826103054)
Image Size                      : 1134x306
Megapixels                      : 0.347
```


The highlighted bytes reflect the errors I found in the file header.

![](/media/forensics_tunnelvision_2.png)

The DIB header was unusual and thus changed to the standard for the bmp file format, set to `0x28` (or 40 in decimal). 

The image width is also appropriately changed to reflect the actual value since it must be greater than 306. That is because the file is larger in size than it displays.

Using the formula, `Total Pixel Data Size = Row Size × Height`, we know the `Total Pixel Data Size or Image Length` to be `2893400` and `bit depth` is `24`. So if width is `1134`, height would be `850`. So we change it accordingly in the hex edit from `32 01` to `52 03`.

![](/media/forensics_tunnelvision_3.png)

The file was recovered and the flag was obtained within the image.

![](/media/forensics_tunnelvision_4.png)

**Learnt:** image headers, .bmp files

**Challenges**:
1. Had to figure out what part of the hex of the bmp file to edit.

**References:**
https://docs.fileformat.com/image/bmp/