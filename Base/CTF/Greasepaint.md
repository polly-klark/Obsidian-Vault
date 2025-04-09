#forensic #writeup 

Task files:

1. [greasepaint.zip](https://storage.volgactf.ru/volgactf/2024/qualifier/greasepaint/greasepaint.zip)

## Summary

The contestants are given an image of an HDD with bad sectors. The bad sectors cause some of ext4 clusters to be marked as bad, while not all the sectors within each bad cluster are actually bad.

These pseudo-bad sectors are used to hide data.

## 1. TL;DR

The solution is fairly straightforward:

1. identify and locate bad sectors of the HDD parsing `image.log` file
    
2. locate bad clusters of the ext4 file system stored in the second partition of `image.dd` using `dumpe2fs`
    
3. build a bad sector to bad cluster mapping
    
4. check there are no FS clusters that are marked bad while not containing any bad sectors within themselves
    
5. examine good sectors that fall within a bad cluster (call them pseudo-bad sectors) to find out that they store the same data
    
6. extract data from bad clusters using majority voting on its pseudo-bad sectors - 512 bytes from each bad cluster
    
7. fix extracted file’s signature to make it a `7z` archive
    
8. apply dictionary-based attack using `rockyou.txt` dictionary and any suitable tool (`john`, `hashcat`, etc.)
    
9. open the archive to get the flag `VolgaCTF{S0me_bad_b1ocks_ar3_not_th@t_bad}`.
    

The data extraction can be done using [extract.py](https://s3.amazonaws.com/archive.volgactf.ru/volgactf_2024/tasks/greasepaint/extract.py):

```
$ python3 extract.py image.dd image.log -o extracted.bin
$ printf "\x37" | dd of=extracted.bin bs=1 count=1 conv=notrunc
$ file extracted.bin
extracted.bin: 7-zip archive data, version 0.4
```

## 2. Studying the given files

We’re given an archive [greasepaint.zip](https://storage.volgactf.ru/volgactf/2024/qualifier/greasepaint/greasepaint.zip) which contains three files: `image.dd`, `image.log`, and `image.sha256deep`.

The content of `image.log` tells us that for some reason `dd` was used instead of a more suitable software (e.g. `dcfldd`). This might be significant.

In `image.log` we see a number of I/O errors:

```
$ head -7 image.log
1057401856 bytes (1.1 GB, 1008 MiB) copied, 222 s, 4.8 MB/s
dd: error reading '/dev/mapper/file1': Input/output error
2066994+0 records in
2066994+0 records out
1058300928 bytes (1.1 GB, 1009 MiB) copied, 222.205 s, 4.8 MB/s
dd: error reading '/dev/mapper/file1': Input/output error
2067606+1 records in
```

Probably, there were some bad blocks on the HDD, so `dd` with `iflag=direct` was used.

Mapping the image we see GPT and two partitions: ESP and a Linux partition with ext4 file system

```
$ loopdev=$(sudo losetup -f --show --partscan image.dd)
$ sudo fdisk -l $loopdev
...
Device         Start     End Sectors  Size Type
/dev/loop18p1   2048  329727  327680  160M EFI System
/dev/loop18p2 329728 2097118 1767391  863M Linux filesystem

$ sudo file -sL ${loopdev}p2
/dev/loop18p2: Linux rev 1.0 ext4 filesystem data, UUID=2787c679-1045-4da1-b560-c279fdc1f900 (extents) (64bit) (large files) (huge files)
```

## 3. Studying the image

We won’t discuss all the possible routes this investigation could have led us along, and will only briefly mention those leading to interesting findings.

### 3.1 `flag.zip` in /root folder

Either by performing signature search or by mounting the image and searching for regular files in the FS we find a `zip` file. The latter also gives us its name - `flag.zip`.

Trying to open we see it is password-protected, so we use `john` / `hashcat` to perform a dictionary attack with `rockyou.txt` and find its password: `butterfly`.

Unzipping we get an image - a flag of Mars (literally), not exactly the flag we’re looking for.

### 3.2 LUKS-container

A signature search, FS tree diffing, or, perhaps, an entropy-based search for possibly encrypted files gives us a LUKS-container `/lib/modules/6.6.22-0-virt/modules.volga.bin`.

Performing a dictionary attack once again we find the passphrase: `chelsea`. Inside the container we find the excellent book `File System Forensic Analysis.pdf`.

Doesn’t look like the flag, though.

## 4. Bad sectors and bad clusters

Examining the FS metadata we find that it lists several bad clusters:

```
$ sudo dumpe2fs ${loopdev}p2
...
Block size:               4096
Fragment size:            4096
...
Bad blocks: 217158, 217234, 217279, 217341, 217514, 217533, 217569, 217589,
<more bad blocks>
220022
...
```

So the HDD indeed had bad sectors, which, apparently, were taken into account during the FS creation.

We also see that FS block (cluster) size is 4096, while there were

```
$ tail -3 image.log
2097065+87 records in
2097152+0 records out
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 225.66 s, 4.8 MB/s
```

2097152 blocks of data written to `image.dd`. The size of `image.dd` is 1073741824 bytes, so we can assume that the sector size of the HDD was 1073741824 / 2097152 = 512 bytes.

Since a file system allocates disk space in clusters, any bad sector that falls within a cluster turns this cluster into a bad FS block.

However, in our case an FS cluster contains 8 disk sectors, and it is marked bad if at least one of these sectors is bad, while the others might still be operational.

This can be visualized as follows:

FS  |      bad      |     good      |      bad      |
    +---------------+---------------+---------------+
HDD | |x| | | | | | | | | | | | | | | | |x| |x| | | |

Therefore, within any bad FS cluster there may be up to 7 good disk sectors. These good sectors are ignored by the file system, hence FS operations won’t overwrite data stored there. Let’s call these sectors "pseudo-bad".

The bad sectors can be found by analyzing the error messages in `image.log`:

```
$ head -8 image.log
1057401856 bytes (1.1 GB, 1008 MiB) copied, 222 s, 4.8 MB/s
dd: error reading '/dev/mapper/file1': Input/output error
2066994+0 records in
2066994+0 records out
1058300928 bytes (1.1 GB, 1009 MiB) copied, 222.205 s, 4.8 MB/s
dd: error reading '/dev/mapper/file1': Input/output error
2067606+1 records in
2067607+0 records out
```

Numbers that are followed by `+0 records out` are precisely the bad clusters of the HDD.

We see that `dd` failed to read sectors 2066994, 2067607 (and many more not shown above). To keep the offsets `dd` most certainly was called with `conv=noerror,sync`, thus these sectors must be all zeros - let’s check:

```
$ dd if=image.dd bs=512 count=1 skip=2066994 | xxd
00000010: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000020: 0000 0000 0000 0000 0000 0000 0000 0000  ................
...
000001f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
$
$ dd if=image.dd bs=512 count=1 skip=2067607 | xxd
00000010: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000020: 0000 0000 0000 0000 0000 0000 0000 0000  ................
...
000001f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
```

Also note that, as we saw earlier, the FS starts at sector 329728, while the first bad sector number is 2066994, which means all the bad sectors fall within the FS partition.

Parsing `image.log` we get a list of the bad HDD sectors. A list of the bad FS clusters can be saved using `dumpe2fs` with `-b` switch:

```
$ sudo dumpe2fs -b ${loopdev}p2 > image.badblocks
```

## 5. Data hiding method

To recap, so far we’ve found that:

- the HDD had several bad sectors
    
- the numbers of these bad sectors can be found in `image.log`
    
- the bad sectors fall within the second partition of the image
    
- the second partition stores ext4 FS
    
- the FS had several bad clusters, their numbers can be found using `dumpe2fs -b`
    
- since the HDD sector is 512 bytes and FS cluster is 4096 bytes, some sectors within a bad cluster may actually be good - "pseudo-bad".
    

Identifying the sectors that make up a particular FS cluster is easy. FS clusters are numbered from the beginning of the partition (sector number 329728), let’s call it `partition_start`. Then the sector numbers are given by this expression:

sector_i_num = partition_start + cluter_num * 8 + i, i = 0..7

For example, recall that the first bad cluster number is 217158 and the first bad sector number is 2066994. The first of these 8 sectors is sector number 2066992 = 329728 + 217158 * 8, and the cluster is made up of sectors 2066992 through 2066999.

Let’s examine these 8 sectors:

```
$ dd if=image.dd bs=512 count=1 skip=2066992 | xxd
00000000: 387a bcaf 271c 0004 0569 6e86 2085 0000  8z..'....in. ...
...
$ dd if=image.dd bs=512 count=1 skip=2066993 | xxd
00000000: 387a bcaf 271c 0004 0569 6e86 2085 0000  8z..'....in. ...
...
$ dd if=image.dd bs=512 count=1 skip=2066994 | xxd
00000000: 0000 0000 0000 0000 0000 0000 0000 0000  ................
...
$ dd if=image.dd bs=512 count=1 skip=2066995 | xxd
00000000: 387a bcaf 271c 0004 0569 6e86 2085 0000  8z..'....in. ...
...
$ dd if=image.dd bs=512 count=1 skip=2066996 | xxd
00000000: 387a bcaf 271c 0004 0569 6e86 2085 0000  8z..'....in. ...
...
$ dd if=image.dd bs=512 count=1 skip=2066997 | xxd
00000000: 387a bcaf 271c 0004 0569 6e86 2085 0000  8z..'....in. ...
...
$ dd if=image.dd bs=512 count=1 skip=2066998 | xxd
00000000: 387a bcaf 271c 0004 0569 6e86 2085 0000  8z..'....in. ...
...
$ dd if=image.dd bs=512 count=1 skip=2066999 | xxd
00000000: 387a bcaf 271c 0004 0569 6e86 2085 0000  8z..'....in. ...
...
```

We see that all the pseudo-bad sectors store the same data. Repeating this for other FS clusters will give similar results (omitted here for brevity).

It appears that the data being hidden is split into 512-byte blocks, each stored in every pseudo-bad sector of respective bad FS cluster.

That data duplication kind of makes sense, since the pseudo-bad clusters are close to the actually bad once, therefore, with high probability may turn bad as well.

|   |   |
|---|---|
||It’s useful to check if there are some deviations from this scheme: pseudo-bad sectors that store different data. We can see there are no such sectors in our case.|

|   |   |
|---|---|
||It’s also important to check that every bad FS cluster contains at least on bad HDD sector. If that’s not the case these pseudo-bad sectors are an obvious first candidate for data hiding.<br><br>We can easily check that all the bad clusters in our case indeed contain at least one bad disk sector.|

## 6. Data extraction and the flag

Finally, let’s extract the hidden data.

Essentially, we need to do the following:

1. for every bad cluster identify up to 7 pseudo-bad sectors
    
2. extract data from the pseudo-bad sectors
    
3. apply something like majority voting to get the most probable candidate
    
4. save 512-byte block and proceed to the next bad FS cluster.
    

That’s implemented in [extract.py](https://s3.amazonaws.com/archive.volgactf.ru/volgactf_2024/tasks/greasepaint/extract.py).

Extracting the data we see that it appears to be just a binary data:

```
$ python3 extract.py image.dd image.log -o extracted.bin
$ file extracted.bin
extracted.bin: data
```

However, if we examine it closely, we notice that is starts with bytes `387a bcaf`:

```
$ xxd -l 16 extracted.bin
00000000: 387a bcaf 271c 0004 0569 6e86 2085 0000  8z..'....in. ...
```

which is `7z` magic number (`'7'`, `'z'`, `0xBC`, `0xAF`, `0x27`, `0x1C`), except the first byte. Possibly, it was changed to hide the archive from signature-based searching.

Rewriting the first byte we check that the extracted file is indeed a `7z` archive:

```
$ printf "\x37" | dd of=extracted.bin bs=1 count=1 conv=notrunc
$ file extracted.bin
extracted.bin: 7-zip archive data, version 0.4
$
$ 7z e  extracted.bin
...
Enter password (will not be echoed):
```

Trying to open we see it’s also password-protected, so one more dictionary-based attack gives the password: `jarvis`.

Now we can extract flag.png from the archive and get the flag: `VolgaCTF{S0me_bad_b1ocks_ar3_not_th@t_bad}`.

|     |                                                                                                                                                                                                                                                                                                                                |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
|     | It should be noted that this kind of data hiding scheme is increasingly irrelevant, as nowadays disks can handle bad sectors themselves, and conventional wisdom is to replace the disk whenever bad sectors appear.<br><br>Still, file systems with bad blocks support can be exploited for data hiding in a similar fashion. |