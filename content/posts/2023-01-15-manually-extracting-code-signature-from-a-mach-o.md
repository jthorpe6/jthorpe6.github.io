---
layout: post
title:  "Manually extracting Code Signature from Mach-O"
date:   2023-01-15 13:43:00 +0000
---

I forgot about this note, and its saved me in the past. The code signature from a mach-o file can easily be extracted with tools like `dd` or `jtool2` as the code signature is always at the end of the file in the `LC_CODE_SIGNATURE` section.

For example, here is how to do it with `dd`

```
% file ./TwoDots
./TwoDots: Mach-O 64-bit executable arm64

% jtool2 -l TwoDots | grep SIG
LC 42: LC_CODE_SIGNATURE     	Offset:     50448, Size:  21040 (0xc510-0x11740)

% dd if=./TwoDots of=twodots.sig bs=50448 skip=1
0+1 records in
0+1 records out
21040 bytes transferred in 0.000148 secs (142162162 bytes/sec)

% file twodots.sig
twodots.sig: Mac OS X Detached Code Signature (non-executable) - 7427 bytes
```


Seeing as I just used `jtool2` to get the offset, why not just use `jtool2` to do the extraction of the code signature, like so.


```
% jtool2 --stripsig TwoDots
Destructive option - saved to /tmp/out.bin

% cp /tmp/out.bin .

% file out.bin
out.bin: Mach-O 64-bit executable arm64

% jtool2 --sig TwoDots
An embedded signature with 6 blobs:
Code Directory (790 bytes)
		Version:     20500
		Flags:       none
		CodeLimit:   0xc510
		Identifier:  com.weplaydots.twodots (@0x60)
		Team ID:     NB26C5EZ8F (@0x77)
		Executable Segment: Base 0x00000000 Limit: 0x00000000 Flags: 0x00000000
```