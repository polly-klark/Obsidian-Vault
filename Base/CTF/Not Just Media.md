#forensic #writeup #video

>Я скачал видео из Интернета, но, думаю, у меня неправильные субтитры. Примечание: Все флажки указаны в нижнем регистре.

Для этого задания нам предоставляется видео в контейнере Matroska (`.mkv`). Этот формат контейнера позволяет упаковывать различные связанные медиафайлы вместе. На видео показан Майкл - талисман IrisCTF, а также субтитры на китайском языке.

```
% ffmpeg -i chal.mkv 
Input #0, matroska,webm, from 'chal.mkv':
...
  Stream #0:0: Video: h264 (High), yuvj420p(pc, bt470bg/unknown/unknown, progressive), 1280x720 [SAR 1:1 DAR 16:9], 60 fps, 60 tbr, 1k tbn (default)
...
  Stream #0:1: Audio: aac (LC), 44100 Hz, stereo, fltp (default)
...
  Stream #0:2: Subtitle: ass (default) (forced)         <----- subtitle is forced on
...
  Stream #0:3: Attachment: none
    Metadata:
      filename        : NotoSansTC-Regular_0.ttf        <----- Default forced font
      mimetype        : font/ttf
      title           : Imported font from Untitled.ass
  Stream #0:4: Attachment: none
    Metadata:
      filename        : FakeFont_0.ttf                  <------  Interesting embedded font
      mimetype        : font/ttf
      title           : Imported font from Untitled.ass
  Stream #0:5: Attachment: none
    Metadata:
      filename        : NotoSans-Regular_0.ttf
      mimetype        : font/ttf
      title           : Imported font from Untitled.ass
```

![[Pasted image 20240823115242.png]]

Я вспомнил, что `mpv` медиаплеер командной строки имеет множество опций. Читая его [документацию](https://mpv.io/manual/master/#video-filters), я нашел следующую опцию для переопределения стиля субтитров и переключения шрифта. Переключение субтитров на встроенные `FakeFont` дает нам флажок воспроизведения.

```
# Play the media by overriding the subtitle style and switching to the FakeFont that was packaged 
# within the MKV container to get the flag.
% mpv --sub-ass-style-overrides=FontName=FakeFont chal.mkv 
```

![[Pasted image 20240823115354.png]]

---

Просматривая видео, мы можем увидеть некоторые субтитры, используя `mkvextract` мы можем извлечь некоторые файлы.

Мы можем извлечь все, используя [скрипт на Gist](https://gist.github.com/konfou/05db32e11ee84efde0adba2ac34331f4), а внутри MKV находятся некоторые TTFS и субтитры.

```
$ ./mkvextract-helper.sh -f chal.mkv -tavsc             (base)
The attachment #1, ID 13897746459734659379, MIME type font/ttf, size 7110560, is written to 'chal/NotoSansTC-Regular_0.ttf'.
The attachment #2, ID 13557627962983747543, MIME type font/ttf, size 64304, is written to 'chal/FakeFont_0.ttf'.
The attachment #3, ID 7918181187782517176, MIME type font/ttf, size 582748, is written to 'chal/NotoSans-Regular_0.ttf'.
Extracting track 1 with the CodecID 'A_AAC' to the file 'chal/track_1.'. Container format: raw AAC file with ADTS headers
Progress: 100%
Extracting track 2 with the CodecID 'S_TEXT/ASS' to the file 'chal/track_2.ass'. Container format: SSA/ASS text subtitles
Progress: 100%
```

FakeFont бросается в глаза, и когда мы его устанавливаем.

Затем мы можем получить текст субтитров из экспортированного `track_2.ass` файла.

```
...
[Events]
Format: Layer, Start, End, Style, Name, MarginL, MarginR, MarginV, Effect, Text
Dialogue: 0,0:00:00.00,0:02:10.00,CJK,,0,0,0,,我們歡迎您接受一生中最大的挑戰，即嘗試理解這段文字的含義
```
![[Pasted image 20240823115710.png]]
Отрисовывая текст субтитров с помощью поддельного шрифта, мы получаем наш флаг. `irisctf{mkvm3rg3_my_b3l0v3d}`
