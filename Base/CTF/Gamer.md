#forensic #writeup 

![[Pasted image 20240826165005.png]]

это диск, поэтому я открыл его с помощью [MagnetAxiom](https://abdelrahme.github.io/posts/0xl4ugh2024/#its-disk-so-i-opened-it-with-magnetaxiom)

в вызове он сказал это `An employee downloaded an unauthorized app` поэтому мы поищем историю [браузера](https://abdelrahme.github.io/posts/0xl4ugh2024/#in-the-challenge-he-said-that-an-employee-downloaded-an-unauthorized-app-so-we-will-look-for-browser-history)
[![геймер1.0](https://abdelrahme.github.io/images/0xl4ugh2024/gamer1.0.png)](https://abdelrahme.github.io/images/0xl4ugh2024/gamer1.0.png)
итак, мы посмотрели и обнаружили, что подозрительный файл .rar загружен с disord, мы знаем, что его имя `appmanifest.rar` так что давайте поищем о нем на диске `find . -name "appmanifest*"`[](https://abdelrahme.github.io/posts/0xl4ugh2024/#so-we-look-we-found-that-there-is-suspicous-rar-file-downloaded-from-disord-we-know-that-its-name-is-appmanifestrar-so-lets-search-about-it-in-disk-find---name-appmanifest)

[![игрок1.1](https://abdelrahme.github.io/images/0xl4ugh2024/gamer1.1.png)](https://abdelrahme.github.io/images/0xl4ugh2024/gamer1.1.png)
итак, мы нашли это, давайте вычислим его хэш `sha256sum appmanifest (1).rar` мы нашли`e29f365335adeea575ae1e592bd50313c38bd89bf9ff6b7f7f06e6377b0661c3`, теперь загрузите его в virus total, чтобы увидеть, есть там вредоносное ПО или нет[](https://abdelrahme.github.io/posts/0xl4ugh2024/#so-we-found-it-lets-calculate-its-hash--sha256sum-appmanifest-1rar-we-found-e29f365335adeea575ae1e592bd50313c38bd89bf9ff6b7f7f06e6377b0661c3-now-upload-it-to-virus-total-to-see-its-have-malware-or-not)

[![игрок1.2](https://abdelrahme.github.io/images/0xl4ugh2024/gamer1.2.png)](https://abdelrahme.github.io/images/0xl4ugh2024/gamer1.2.png)
итак, мы знаем, что это вредоносное ПО, и у нас есть cve об этом `cve-2023-38831` теперь мы знаем, что это злоумышленник отправил его в discord, и в нем есть вредоносное ПО, мы проанализируем это позже[](https://abdelrahme.github.io/posts/0xl4ugh2024/#so-we-know-know-that-its-malicious-and-we-got-cve-of-it-cve-2023-38831-now-we-know-that-this-is-attacker-sent-it-in-discord-and-it-have-malware-we-will-analyze-it-later)

теперь нам нужно проанализировать файл кэша discord, [эта ссылка помогла мне](https://abrignoni.blogspot.com/2018/03/finding-discord-app-chats-in-windows.html) поэтому я скачал этот [инструмент](https://www.nirsoft.net/utils/chrome_cache_view.html), открыл файл кэша в этой программе и извлек все файлы щелчком мыши, `ctr a and then press f4` а затем я открыл `50.json` файл[](https://abdelrahme.github.io/posts/0xl4ugh2024/#now-we-need-to-analyze-discord-cache-file-this-link-helped-me-so-i-downloaded-this-tool-and-i-opened-cache-file-in-this-program-and-extract-all-files-by-click-ctr-a-and-then-press-f4--and-then-i-opened-50json-file)

[![игрок1.3](https://abdelrahme.github.io/images/0xl4ugh2024/gamer1.3.png)](https://abdelrahme.github.io/images/0xl4ugh2024/gamer1.3.png)
теперь мы знаем, что t4r3kk, отправивший вредоносный файл .rar жертве, нам нужно знать время создания для этого пользователя, поэтому мы используем [этот веб-сайт](https://discord.id/) и указываем id `964399437671710790`[](https://abdelrahme.github.io/posts/0xl4ugh2024/#now-we-know-that-t4r3kk-who-sent-the-malicious-rar-file-to-the-victim-we-need-to-know-create-time-for-this-user-so-we-use-this-website-and-put-id--964399437671710790)

[![игрок 1.4.4](https://abdelrahme.github.io/images/0xl4ugh2024/gamer1.4.png)](https://abdelrahme.github.io/images/0xl4ugh2024/gamer1.4.png)
now we know creation date and id and i did some osint i got the real name of attacker[](https://abdelrahme.github.io/posts/0xl4ugh2024/#now-we-know-creation-date-and-id-and-i-did-some-osint-i-got-the-real-name-of-attacker)

flag: ==0xL4ugh{Discord_1.0.9028_964399437671710790_15-04-2022_05:39:03_IgorDekhtyarchuk}==

---

![[Pasted image 20240826171910.png]]

теперь мы знаем sha256 файла и знаем cve, который мы получили в первом вопросе, так что теперь нам нужно знать, когда this is .rar установлен в системе[](https://abdelrahme.github.io/posts/0xl4ugh2024/#now-here-we-know-sha256-of-file-and-we-know-the-cve-we-got-in-first-question-so-now-we-need-to-know-when-this-is-rar-is-installed-in-the-system)

это первый раз, когда файл загружается в систему

![[Pasted image 20240826172012.png]]

и как это было выполнено в первый раз[](https://abdelrahme.github.io/posts/0xl4ugh2024/#and-the-first-time-it-executed)

[![игрок2.1](https://abdelrahme.github.io/images/0xl4ugh2024/gamer2.1.png)](https://abdelrahme.github.io/images/0xl4ugh2024/gamer2.1.png)
