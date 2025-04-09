#forensic #writeup 

Task files:

1. [reanimation.zip](https://storage.volgactf.ru/volgactf/2024/qualifier/reanimation/reanimation.zip)

## Summary

The contestants are given an image of a 1GB HDD with NTFS partition. The flag is written on the frames of a deleted and partially overwritten `flag.mp4` file, which is an H264/AVCC video.

To locate `flag.mp4` `$MFT` can be examined, and recovering the deleted file is possible using tools like `R-Studio`, `ntfsundelete`, etc.

Fixing the corrupted beginning of the video is easy as it’s only necessary to reconstruct `ftyp` and `moov` atoms. Then any video player will be able to open the video and reveal the flag.

## 1. TL;DR

The solution is straightforward:

1. Find the deleted MP4 file.
    
2. Recover the deleted file, try to open it.
    
3. Use an online tool or the [solution script](https://s3.amazonaws.com/archive.volgactf.ru/volgactf_2024/tasks/reanimation/writeup.html#solution_script) to recreate the missing `ftyp` and `mdat` atoms.
    
4. Play the repaired video and get the flag: `VolgaCTF{AVCC_i$_not_@_honey_badger_but_sti1l_rec0verab!e}`.
    

## 2. Studying the given files

We’re given an archive [reanimation.zip](https://storage.volgactf.ru/volgactf/2024/qualifier/reanimation/reanimation.zip) which contains three files: `image.dd`, `image.hash`, and `image.errlog`.

The error log doesn’t show any acquisition errors. The hashes match, which suggests that the image was not modified (or the hashes were recalculated as well):

```
$ cat image.errlog
16384+0 records in
16384+0 records out
$ head -1 image.hash
0 - 65536: 635cac9a6e44b70c005cf012adaf3c4c428553024c32ec51a5c3af1b1feace4c
$ dcfldd if=image.dd of=/dev/null hashwindow=65536 hash=sha256 hashlog=/tmp/image.hash
32768 blocks (1024Mb) written.
32768+0 records in
32768+0 records out
$ diff image.hash /tmp/image.hash
```

Examining `image.dd` we see there is an MBR with a single partition that contains NTFS file system:

```
$ fdisk -l image.dd
Disk image.dd: 1 GiB, 1073741824 bytes, 2097152 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xda98cb29

Device     Boot Start     End Sectors  Size Id Type
image.dd1         128 2091135 2091008 1021M  7 HPFS/NTFS/exFAT
```

Since we need to work with NTFS let’s mount the image in Windows OS using AccessData’s `FTK Imager`:

 "File" -> "Image Mounting..." -> specify the path to image.dd -> "Mount"

Assume `Partition 1` was assigned drive letter `E:`.

Now we can open the mounted disk and study the files. We see a lot of `.pdf` and `.txt` documents, `.png` and `.jpg` imaged, and `zip`/`7z` archives, some of which are password-protected.

## 3. NTFS Alternate Data Streams

Eyeballing doesn’t give us much so let’s try looking for data that could be hidden using NTFS peculiar feature called Alternate Data Stream, ADS [1](https://s3.amazonaws.com/archive.volgactf.ru/volgactf_2024/tasks/reanimation/writeup.html#MS-FSCC), [2](https://s3.amazonaws.com/archive.volgactf.ru/volgactf_2024/tasks/reanimation/writeup.html#Wiki-Fork). A very useful tool `streams` [3](https://s3.amazonaws.com/archive.volgactf.ru/volgactf_2024/tasks/reanimation/writeup.html#Streams) allows to check recursively if there are any ADS assigned to the FS objects:

```
C:\>streams64 -s E:

streams v1.60 - Reveal NTFS alternate streams.
Copyright (C) 2005-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

E:\109bc6edf63643f8847b906c8bd6888b\838bd357216b414ba1dd794b658473e9\d94da1568694433894b1708a7c96b953\59b6160da57141069bc2b2ee41b8bc14.jpg:
            :flag:$DATA 44
E:\1cbcd6595bca4f0ca17122fc2b9ea881\61ef1e470dda48ba90a739f26576d2ca.7z:
            :file:$DATA 403412
```

So we see there are two ADS named `flag` and `file`, let’s examine their content:

```
C:\>more < E:\109bc6edf63643f8847b906c8bd6888b\838bd357216b414ba1dd794b658473e9\d94da1568694433894b1708a7c96b953\59b6160da57141069bc2b2ee41b8bc14.jpg:flag
"Would've been way to easy, wouldn't it?"

C:\>more < E:\1cbcd6595bca4f0ca17122fc2b9ea881\61ef1e470dda48ba90a739f26576d2ca.7z:file
%PDF-1.5
%╡╡╡╡
1 0 obj
<</Type/Catalog/Pages 2 0 R/Lang(ru-RU) /StructTreeRoot 17 0 R/MarkInfo<</Marked true>>>>
endobj
...
```

The first ADS' content is discouraging, while the second one contains a PDF file. Saving the ADS to a file (in the PowerShell)

```
PS C:\>Get-Content -Encoding Byte -Stream file "E:\1cbcd6595bca4f0ca17122fc2b9ea881\61ef1e470dda48ba90a739f26576d2ca.7z" | `
    Set-Content -Encoding Byte .\story.pdf

PS C:\>Start-Process story.pdf
```

and looking at its content we see a redacted story about a CTF player performing forensics analysis of an NTFS image.

Although this story is largely useless, we can notice it kind of draws attention to examining deleted files (e.g. "…​ fragmented remnants of deleted data, particularly, video files"). That could be a hint of sorts…​

## 4. Examining deleted files

To find deleted files on NTFS one needs to study its metadata file called master file table, `$MFT`.

While a valuable exercise, or sometimes the only way whenever tools fail, manually parsing `$MFT` should be preceded with an automated examination. Deleted files recovery can be done using one of `R-Studio` tools [4](https://s3.amazonaws.com/archive.volgactf.ru/volgactf_2024/tasks/reanimation/writeup.html#R-Studio) (e.g. `R-Undelete`) on Windows and `ntfsundelete` on Linux.

Let’s switch to a Linux box with `ntfsundelete` installed and try to search for deleted files on `image.dd`:

```
$ loopdev=`sudo losetup -f --show --partscan image.dd`
$ sudo ntfsundelete --scan ${loopdev}p1
Inode    Flags  %age     Date    Time       Size  Filename
-----------------------------------------------------------------------
16       F..!     0%  1970-01-01 04:00         0  <none>
...
7000     FN..    96%  2024-03-27 18:40  12326414  c28824e38a6a4d3287d0ede0b8811555.mp4
8071     FN..     0%  2024-03-27 17:48         0  07fac12c7af4457da55e1f6d9627bc22.7z
9653     FN..     0%  2024-03-27 17:48         0  fce3ea1db2724142aafe3160b8b7363e.zip
10389    F..!     0%  1970-01-01 04:00         0  <none>
...
Files with potentially recoverable content: 1
```

So, there is a deleted MP4 video file, and 96% of its content can be recovered. Extracting and trying to open the file we see:

```
$ mkdir -p /tmp/ntfsundelete
$ sudo ntfsundelete --undelete --match "*.mp4" --output /tmp/ntfsundelete/flag_extracted.mp4 ${loopdev}p1
Inode    Flags  %age     Date    Time       Size  Filename
-----------------------------------------------------------------------
7000     FN..    96%  2024-03-27 18:40  12326414  c28824e38a6a4d3287d0ede0b8811555.mp4

Undeleted 'c28824e38a6a4d3287d0ede0b8811555.mp4' successfully to /tmp/ntfsundelete/flag_extracted.mp4.

Files with potentially recoverable content: 1
$ sudo chown $USER /tmp/ntfsundelete/flag_extracted.mp4
$ file /tmp/ntfsundelete/flag_extracted.mp4
/tmp/ntfsundelete/flag_extracted.mp4: data
$ xdg-open /tmp/ntfsundelete/flag_extracted.mp4
$ ffplay /tmp/ntfsundelete/flag_extracted.mp4
...
[mov,mp4,m4a,3gp,3g2,mj2 @ 0x78b818000cc0] Format mov,mp4,m4a,3gp,3g2,mj2 detected only with low score of 1, misdetection possible!
[mov,mp4,m4a,3gp,3g2,mj2 @ 0x78b818000cc0] moov atom not found
/tmp/ntfsundelete/flag_extracted.mp4: Invalid data found when processing input
$ xxd -l 64 /tmp/ntfsundelete/flag_extracted.mp4
00000000: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000010: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000020: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000030: 0000 0000 0000 0000 0000 0000 0000 0000  ................
$ cmp /tmp/ntfsundelete/flag_extracted.mp4 /dev/zero
/tmp/ntfsundelete/flag_extracted.mp4 /dev/zero differ: byte 405505, line 1
```

|   |   |
|---|---|
||`ffplay` player usually comes with `ffmpeg` package. Sometimes `ffplay` can skip corrupted parts of a video file and play the rest of it, while other players refuse to open such a file at all.|

The extracted file is either corrupted or not a video file at all, since `file` doesn’t recognise it, `ffplay` failed to find its `moov` atom, and there are `405504 = 0x63000` zero bytes in the beginning. We need to examine its content in detail.

|   |   |
|---|---|
||Since the deleted MP4 file `c28824e38a6a4d3287d0ede0b8811555.mp4` is still present in the `$MFT` we can assume there were no new files created on this NTFS after the MP4 had been deleted.<br><br>As to what caused the first `0x63000` bytes of the file to be overwritten, we can guess that file deletion triggered rebuild of the containing folder’s index - `$I30` attribute that maintains an index of all files and subfolders stored in the folder.|

## 5. Repairing the extracted video file

We can try to repair the video file using online tools. It appears this particular type of damaging is fairly common so the automated tools are capable of fixing it. However, treating this task as an opportunity to study MP4 file format, let’s try to recover it manually.

### 5.1 MP4 file format

A very clear and concise description of QuickTime file format (the format that MP4 is based on) can be found on the Apple Developer portal [5](https://s3.amazonaws.com/archive.volgactf.ru/volgactf_2024/tasks/reanimation/writeup.html#Apple-QuickTime). MP4 file format is defined in [6](https://s3.amazonaws.com/archive.volgactf.ru/volgactf_2024/tasks/reanimation/writeup.html#MP4-format-ISO), see also [7](https://s3.amazonaws.com/archive.volgactf.ru/volgactf_2024/tasks/reanimation/writeup.html#MP4-format-ISO-Wiki) and [8](https://s3.amazonaws.com/archive.volgactf.ru/volgactf_2024/tasks/reanimation/writeup.html#MP4-format-Wiki).

An MP4 file consists of a series of the so-called atoms, which are essentially containers that contain media data, metadata, or other containers. Generally, there are two types of atoms that must present in a valid MP4 file:

1. [ftyp](https://developer.apple.com/documentation/quicktime-file-format/file_type_compatibility_atom) - file type compatibility, identifies the file type and differentiates it from similar file types,
    
2. [moov](https://developer.apple.com/documentation/quicktime-file-format/movie_data_atom) - movie resource metadata, describes where the movie data can be found and how to interpret it (number and type of tracks, location of sample data, and so on).
    

Almost always the media data is stored in the same file, so typically there’s one more atom that should be present:

3. [mdat](https://developer.apple.com/documentation/quicktime-file-format/movie_data_atom) - movie sample data, media samples such as video frames and groups of audio samples.
    

While `mdat` stores sample data, all the information required to parse, decode, and play the samples is stored in `moov` atom. In the following we’ll need to examine one particular atom:

4. [stco](https://developer.apple.com/documentation/quicktime-file-format/chunk_offset_atom) - chunk offset atom, identifies the location of each chunk of data in the media’s data stream.
    

Atoms consist of a header, followed by atom data. The header contains the 32-bit atom’s size and 32-bit type fields, giving the size of the atom in bytes and its type, see [atoms](https://developer.apple.com/documentation/quicktime-file-format/atoms). The exact structure of a particular atom can be found following the corresponding links given above.

Unless explicitly arranged for fast start of playback (e.g. using `ffmpeg` with `-movflags faststart`), `moov` atom is stored at the end of a file, following `mdat`, while `ftyp` **must be** the first atom of the file. Therefore, a typical MP4 file is organized as the following sequence of atoms: `ftyp` → `mdat` → `moov`.

Example 1. An example of a typical MP4 file

Let’s examine a typical MP4 video file with a single H264/AVCC video stream. Using `ffmpeg` we can easily create a blank file of the right structure:

```
$ ffmpeg -f lavfi -i color=c=black:s=1024x768:r=25/1 -c:v h264 -an -t 3 /tmp/test.mp4
$ xxd -l 64 /tmp/test.mp4
00000000: 0000 0020 6674 7970 6973 6f6d 0000 0200  ... ftypisom....
00000010: 6973 6f6d 6973 6f32 6176 6331 6d70 3431  isomiso2avc1mp41
00000020: 0000 0008 6672 6565 0000 0dcc 6d64 6174  ....free....mdat
00000030: 0000 02af 0605 ffff abdc 45e9 bde6 d948  ..........E....H
```

It starts with [ftyp](https://developer.apple.com/documentation/quicktime-file-format/file_type_compatibility_atom) atom (as it must):

|Offset|Field|Value|
|---|---|---|
|`0x0`|`size`|`0x20`|
|`0x4`|`type`|`'ftyp'`|
|`0x8`|`Major brand`|`'isom'`|
|`0xc`|`Minor brand`|`0x200`|
|`0x10`|`Compatible brands`|`'isom'`, `'iso2'`, `'avc1'`, `'mp41'`|

Then goes [free](https://developer.apple.com/documentation/quicktime-file-format/free_space_atom) atom:

|Offset|Field|Value|
|---|---|---|
|`0x0`|`size`|`0x8`|
|`0x4`|`type`|`'free'`|

`free` is followed by [mdat](https://developer.apple.com/documentation/quicktime-file-format/movie_data_atom) atom:

|Offset|Field|Value|
|---|---|---|
|`0x0`|`size`|`0xdcc`|
|`0x4`|`type`|`'mdat'`|
|`0x8`|`Movie media data`|`0000 02af 0605 ffff …​`|

|   |   |
|---|---|
||`free` atom is used as a placeholder. Whenever the sample data (`mdat` atom) size exceeds 232 it must become an 8-byte value, while by default size is stored as 4-byte value in `mdat` header.<br><br>A simple solution that doesn’t require any offset recalculation is to simply extend `mdat` header 8 bytes towards the beginning of the file - to overwrite the placeholder `free`. The new space is enough to store the extended size field (see [mdat](https://developer.apple.com/documentation/quicktime-file-format/movie_data_atom)).<br><br>**N.B.** `free` atom is optional.|

Counting from the beginning of the file we see that `mdat` atom’s offset is `0x28` (the size of `ftyp` is `0x20` and the size of `free` is `0x8`). The size of `mdat` is `0xdcc` so the next atom, which we expect to be `moov`, should be at offset `0xdf4 = 3572`. Let’s check:

```
$ binwalk -R moov /tmp/test.mp4

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
3576          0xDF8           Raw signature (moov)

$ xxd -s 3572 -l 128 /tmp/test.mp4
00000df4: 0000 06c0 6d6f 6f76 0000 006c 6d76 6864  ....moov...lmvhd
00000e04: 0000 0000 0000 0000 0000 0000 0000 03e8  ................
00000e14: 0000 0bb8 0001 0000 0100 0000 0000 0000  ................
00000e24: 0000 0000 0001 0000 0000 0000 0000 0000  ................
00000e34: 0000 0000 0001 0000 0000 0000 0000 0000  ................
00000e44: 0000 0000 4000 0000 0000 0000 0000 0000  ....@...........
00000e54: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000e64: 0000 0002 0000 05ea 7472 616b 0000 005c  ........trak...\
```

Indeed, `moov` atom is where we expect it to be, its size is `0x6c0`; we can also see its movie header atom `mvhd` and the beginning of track atom `trak`.

Finally, let’s check the chunk offsets atom [stco](https://developer.apple.com/documentation/quicktime-file-format/chunk_offset_atom):

```
$ binwalk -R stco /tmp/test.mp4

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
5186          0x1442          Raw signature (stco)

$ xxd -s 5182 -l 32 /tmp/test.mp4
0000143e: 0000 0014 7374 636f 0000 0000 0000 0001  ....stco........
0000144e: 0000 0030 0000 0062 7564 7461 0000 005a  ...0...budta...Z
```

|Offset|Field|Value|
|---|---|---|
|`0x0`|`size`|`0x14`|
|`0x4`|`type`|`'stco'`|
|`0x8`|`Version`|`0x00`|
|`0x9`|`Flags`|`0x00`, `0x00`, `0x00`|
|`0xc`|`Number of entries`|`0x1`|
|`0x10`|`Chunk offset table`|`0x30`|

So there is only one record in the `Chunk offset table`, and the offset of the first (and only) chunk is `0x30`.

Note that this is exactly the offset of `mdat` data filed: the size of `ftyp` is `0x20`, the size of `free` is `0x8` plus two 4-byte fields in the `mdat` header gives `0x20 + 0x8 + 0x4 + 0x4 = 0x30`.

|   |   |
|---|---|
||`mdat` atom’s `Movie media data` field offset can be found in `stco` atom contained within `moov` atom, which allows us to calculate `mdat` offset.|

|   |   |
|---|---|
||To summarize:<br><br>- A typical MP4 file must start with `ftyp` atom, followed by optional `free` atom, followed by `mdat`, and, finally, `moov` atom at the end of the file.<br>    <br>- `ftyp` only describes the compatible players, hence it rarely contains unique information and can be recreated by copying the whole atom from another MP4 file.<br>    <br>- As `mdat` contains the sample data, it occupies the most of the file space, so, whenever the MP4 file is partially overwritten, `moov` is likely to stay intact.<br>    <br>- `Chunk offset table` stored in `stco` inner atom of `moov` atom can be used to identify `mdat` atom file offset.|

### 5.2 Getting the flag

Now we’re ready to repair the extracted file. First of all, let’s check if this is actually an MP4 video file:

```
$ binwalk -R ftyp /tmp/ntfsundelete/flag_extracted.mp4

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------

$ binwalk -R mdat /tmp/ntfsundelete/flag_extracted.mp4

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------

$ binwalk -R moov /tmp/ntfsundelete/flag_extracted.mp4

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
12307139      0xBBCAC3        Raw signature (moov)

$ xxd -l 64 /tmp/ntfsundelete/flag_extracted.mp4
00000000: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000010: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000020: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000030: 0000 0000 0000 0000 0000 0000 0000 0000  ................
$ xxd -s 12307139 -l 128 /tmp/ntfsundelete/flag_extracted.mp4
00bbcac3: 6d6f 6f76 0000 006c 6d76 6864 0000 0000  moov...lmvhd....
00bbcad3: 0000 0000 0000 0000 0000 03e8 0001 0f68  ...............h
00bbcae3: 0001 0000 0100 0000 0000 0000 0000 0000  ................
00bbcaf3: 0001 0000 0000 0000 0000 0000 0000 0000  ................
00bbcb03: 0001 0000 0000 0000 0000 0000 0000 0000  ................
00bbcb13: 4000 0000 0000 0000 0000 0000 0000 0000  @...............
00bbcb23: 0000 0000 0000 0000 0000 0000 0000 0002  ................
00bbcb33: 0000 4a79 7472 616b 0000 005c 746b 6864  ..Jytrak...\tkhd
```

As can be seen, `ftyp` and `mdat` atoms are missing, while `moov` is present and seems to be intact.

To repair the MP4 file we need to:

1. Locate `stco` atom, parse its `Chunk offset table` and get the file offset of the first sample chunk, call it `mdat_data_offset`.
    
    Calculate `mdat` atom file offset `mdat_offset`: `mdat_offset = mdat_data_offset - 8`.
    
2. Locate file offset of `moov` atom, `moov_offset`.
    
3. Calculate `mdat` atom size `mdat_size`: `mdat_size = moov_offset - mdat_offset`.
    
4. Create `mdat` header: `mdat_size` || `'mdat'`
    
5. Create `ftyp` atom with "empty" `Compatible brands` made of spaces `' '`.
    
6. Overwrite the first bytes of the file with the created `ftyp` and `mdat` atoms.
    
7. Finally, play the repaired MP4 file.
    

|   |   |
|---|---|
||Note that we only need to make sure the offset of the first I-frame within the media data stream is correct and the `moov` atom is correctly identified and located - that is, the atom structure of the MP4 file is correct.<br><br>Should there be any atoms in between `mdat` and `moov`, we can safely ignore them and make a part of `mdat` atom - the only consequence might be playback artifacts towards the end of the video.|

This algorithm is implemented by the following python script:

```
$ python3 -c "
# 1. read the corrupted file
data = open('/tmp/ntfsundelete/flag_extracted.mp4', 'rb').read()

# 2. reconstruct mdat and ftyp atoms
# mdat: the first chunk follows the mdat header, it's offset is stored in stco atom
p = data.rfind(b'stco')
mdat_data_offset = int.from_bytes(data[p+12:p+16], byteorder='big', signed=False)
mdat_offset = mdat_data_offset - 8  # 8 = size + type fields of mdat atom
moov_offset = data.rfind(b'moov') - 4
mdat_size = moov_offset - mdat_offset
mdat_header = int(mdat_size).to_bytes(4, byteorder='big', signed=False) + b'mdat'

# ftyp
ftyp_atom = int(mdat_offset).to_bytes(4, byteorder='big', signed=False) + b'ftypisom\x00\x00\x02\x00'
ftyp_atom = ftyp_atom + b' '*(mdat_offset - len(ftyp_atom))

# 3. create a recovered file
open('/tmp/ntfsundelete/flag_reanimated.mp4', 'wb').write(ftyp_atom + mdat_header + data[mdat_data_offset:]) "

$ ffplay /tmp/ntfsundelete/flag_reanimated.mp4
```

Playing the recovered file gives the flag: `VolgaCTF{AVCC_i$_not_@_honey_badger_but_sti1l_rec0verab!e}`.