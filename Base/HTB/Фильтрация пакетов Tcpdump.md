#traffic #network #htb #tcpdump 

Tcpdump предоставляет надёжный и эффективный способ анализа данных, полученных в результате захвата, с помощью фильтров пакетов. В этом разделе мы рассмотрим эти фильтры и узнаем, как они изменяют выходные данные захвата.

---

## Фильтрация и расширенные возможности синтаксиса

Использование более продвинутых параметров фильтрации, таких как перечисленные ниже, позволит нам сократить объём трафика, выводимого на экран или отправляемого в файл. Уменьшив объём информации, которую мы собираем и записываем на диск, мы сможем сократить пространство, необходимое для записи файла, и ускорить обработку данных в буфере. Фильтры могут быть полезны в сочетании со стандартными параметрами синтаксиса tcpdump. Мы можем вести сбор данных в любом объёме или настроить его так, чтобы он захватывал только пакеты с определённого хоста или даже пакеты с определённым битом в заголовке TCP. Настоятельно рекомендуем изучить более продвинутые фильтры и найти различные комбинации.

Эти фильтры и расширенные операторы ни в коем случае не являются исчерпывающим списком. Они были выбраны потому, что используются чаще всего и позволяют быстро приступить к работе. При использовании эти фильтры будут проверять все захваченные пакеты и искать в заголовке протокола указанные значения.

#### Полезные фильтры TCPDump

| **Фильтр**           | **Результат**                                                                                                                  |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| host                 | `host` отфильтрует видимый трафик, чтобы отобразить все, что связано с указанным хостом. Двунаправленный                       |
| src / dest           | `src` и `dest` — это модификаторы. С их помощью можно указать исходный или целевой хост или порт.                              |
| net                  | `net` покажет нам весь трафик, исходящий из указанной сети или направляющийся в неё. Используется обозначение / .              |
| proto                | будет выполнять фильтрацию по определённому типу протокола. (например, Ethernet, TCP, UDP и ICMP)                              |
| port                 | `port` является двунаправленным. Он покажет любой трафик, в котором указанный порт является источником или пунктом назначения. |
| portrange            | `portrange` позволяет указать диапазон портов. (0–1024)                                                                        |
| less / greater "< >" | `less` `greater` можно использовать для поиска пакета или параметра протокола определённого размера.                           |
| and / &&             | `and` `&&` можно использовать для объединения двух разных фильтров. Например, хост src и порт.                                 |
| or                   | `or` допускает совпадение по одному из двух условий. Необязательно, чтобы совпадало по обоим. Это может быть непросто.         |
| not                  | `not` Это модификатор, который означает что угодно, кроме x. Например, не UDP.                                                 |

С помощью этих фильтров мы можем фильтровать сетевой трафик по большинству параметров, чтобы упростить анализ. Давайте рассмотрим несколько примеров таких фильтров и то, как они выглядят при использовании. При использовании фильтра `host` любой введенный нами IP-адрес будет проверяться в поле IP-адреса источника или получателя. Это видно на примере ниже.

#### Фильтр Хоста

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$ ### Syntax: host [IP]
Barlogsha@htb[/htb]$ sudo tcpdump -i eth0 host 172.16.146.2

tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
14:50:53.072536 IP 172.16.146.2.48738 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 3400465007:3400465044, ack 254421756, win 501, options [nop,nop,TS val 220968655 ecr 80852594], length 37
14:50:53.108740 IP 172.16.146.2.55606 > 172.67.1.1.https: Flags [P.], seq 4227143181:4227143273, ack 1980233980, win 21975, length 92
14:50:53.173084 IP 172.67.1.1.https > 172.16.146.2.55606: Flags [.], ack 92, win 69, length 0
14:50:53.175017 IP 172.16.146.2.35744 > 172.16.146.1.domain: 55991+ PTR? 148.199.31.52.in-addr.arpa. (44)
14:50:53.175714 IP 172.16.146.1.domain > 172.16.146.2.35744: 55991 1/0/0 PTR ec2-52-31-199-148.eu-west-1.compute.amazonaws.com. (107) 
```

Этот фильтр часто используется, когда нужно изучить только конкретный хост или сервер. С его помощью можно определить, с кем взаимодействует этот хост или сервер и каким образом. Исходя из конфигурации нашей сети, мы поймем, является ли это соединение легитимным. Если взаимодействие кажется подозрительным, можно использовать другие фильтры и параметры для более детального просмотра содержимого. Помимо отдельных хостов, можно указать исходный и целевой хосты. Также можно указать целые сети и их порты.

#### Фильтр источника/назначения

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$ ### Syntax: src/dst [host|net|port] [IP|Network Range|Port]
Barlogsha@htb[/htb]$ sudo tcpdump -i eth0 src host 172.16.146.2
  
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
14:53:36.199628 IP 172.16.146.2.48766 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 1428378231:1428378268, ack 3778572066, win 501, options [nop,nop,TS val 221131782 ecr 80889856], length 37
14:53:36.203166 IP 172.16.146.2.55606 > 172.67.1.1.https: Flags [P.], seq 4227144035:4227144103, ack 1980235221, win 21975, length 68
14:53:36.267059 IP 172.16.146.2.36424 > 172.16.146.1.domain: 40873+ PTR? 148.199.31.52.in-addr.arpa. (44)
14:53:36.267880 IP 172.16.146.2.51151 > 172.16.146.1.domain: 10032+ PTR? 2.146.16.172.in-addr.arpa. (43)
14:53:36.276425 IP 172.16.146.2.46588 > 172.16.146.1.domain: 28357+ PTR? 1.1.67.172.in-addr.arpa. (41)
14:53:36.337722 IP 172.16.146.2.48766 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [.], ack 34, win 501, options [nop,nop,TS val 221131920 ecr 80899875], length 0
14:53:36.338841 IP 172.16.146.2.48766 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [.], ack 65, win 501, options [nop,nop,TS val 221131921 ecr 80899875], length 0
14:53:36.339273 IP 172.16.146.2.48766 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 37:68, ack 66, win 501, options [nop,nop,TS val 221131922 ecr 80899875], length 31
14:53:36.339334 IP 172.16.146.2.48766 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [F.], seq 68, ack 66, win 501, options [nop,nop,TS val 221131922 ecr 80899875], length 0
14:53:36.370791 IP 172.16.146.2.32972 > 172.16.146.1.domain: 3856+ PTR? 1.146.16.172.in-addr.arpa. (43)
```

Параметры «источник» и «назначение» позволяют нам работать с направлениями связи. Например, в последнем выводе мы указали, что наш `source` хост — это `172.16.146.2`, и будут перехватываться только пакеты, отправленные с этого хоста. То же самое можно сделать для портов и диапазонов сетей. Пример использования `src port #` может выглядеть примерно так:

#### Использование Source With Port в качестве фильтра

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$ sudo tcpdump -i eth0 tcp src port 80

06:17:08.222534 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [S.], seq 290218379, ack 951057940, win 5840, options [mss 1380,nop,nop,sackOK], length 0
06:17:08.783340 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], ack 480, win 6432, length 0
06:17:08.993643 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 1:1381, ack 480, win 6432, length 1380: HTTP: HTTP/1.1 200 OK
06:17:09.123830 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 1381:2761, ack 480, win 6432, length 1380: HTTP
06:17:09.754737 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 2761:4141, ack 480, win 6432, length 1380: HTTP
06:17:09.864896 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [P.], seq 4141:5521, ack 480, win 6432, length 1380: HTTP
06:17:09.945011 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 5521:6901, ack 480, win 6432, length 1380: HTTP
```

Теперь обратите внимание, что мы видим только одну сторону диалога? Это потому, что мы фильтруем по исходному порту 80 (HTTP). При таком использовании `net` будет захватывать всё, что соответствует `/` в сети. В этом примере мы ищем всё, что предназначено для `172.16.146.0/24` сети.

#### Использование пункта назначения в сочетании с сетевым фильтром

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$ sudo tcpdump -i eth0 dest net 172.16.146.0/24

tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
16:33:14.376003 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], ack 1486880537, win 316, options [nop,nop,TS val 2311579424 ecr 263866084], length 0
16:33:14.442123 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [P.], seq 0:385, ack 1, win 316, options [nop,nop,TS val 2311579493 ecr 263866084], length 385
16:33:14.442188 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [P.], seq 385:1803, ack 1, win 316, options [nop,nop,TS val 2311579493 ecr 263866084], length 1418
16:33:14.442223 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 1803:4639, ack 1, win 316, options [nop,nop,TS val 2311579494 ecr 263866084], length 2836
16:33:14.443161 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [P.], seq 4639:5817, ack 1, win 316, options [nop,nop,TS val 2311579495 ecr 263866084], length 1178
16:33:14.443199 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 5817:8653, ack 1, win 316, options [nop,nop,TS val 2311579495 ecr 263866084], length 2836
16:33:14.444407 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 8653:10071, ack 1, win 316, options [nop,nop,TS val 2311579497 ecr 263866084], length 1418
16:33:14.445479 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 10071:11489, ack 1, win 316, options [nop,nop,TS val 2311579497 ecr 263866084], length 1418
16:33:14.445531 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 11489:12907, ack 1, win 316, options [nop,nop,TS val 2311579498 ecr 263866084], length 1418
16:33:14.446955 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 12907:14325, ack 1, win 316, options [nop,nop,TS val 2311579498 ecr 263866084], length 1418
```

Этот фильтр может использовать общее название протокола или его номер для любого протокола IP, IPv6 или Ethernet. Распространёнными примерами являются `tcp[6], udp[17], or icmp[1]`. В приведённых ниже результатах мы будем использовать как общее название (вверху), так и номер протокола (внизу). Мы видим, что результат один и тот же. По большей части они взаимозаменяемы, но использование `proto` станет более полезным, когда вы начнёте анализировать определённую часть заголовков IP или других протоколов. Это станет более очевидным далее в этом разделе, когда мы будем говорить о поиске флагов TCP. Мы можем обратиться к этому [ресурсу](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) за полезным списком номеров протоколов.

#### Фильтр протокола - Общее название

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$ ### Syntax: [tcp/udp/icmp]
Barlogsha@htb[/htb]$ sudo tcpdump -i eth0 udp

06:17:09.864896 IP dialin-145-254-160-237.pools.arcor-ip.net.3009 > 145.253.2.203.domain: 35+ A? pagead2.googlesyndication.com. (47)
06:17:10.225414 IP 145.253.2.203.domain > dialin-145-254-160-237.pools.arcor-ip.net.3009: 35 4/0/0 CNAME pagead2.google.com., CNAME pagead.google.akadns.net., A 216.239.59.104, A 216.239.59.99 (146)
```

#### Фильтр Номер протокола

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$ ### Syntax: proto [protocol number]
Barlogsha@htb[/htb]$ sudo tcpdump -i eth0 proto 17

06:17:09.864896 IP dialin-145-254-160-237.pools.arcor-ip.net.3009 > 145.253.2.203.domain: 35+ A? pagead2.googlesyndication.com. (47)
06:17:10.225414 IP 145.253.2.203.domain > dialin-145-254-160-237.pools.arcor-ip.net.3009: 35 4/0/0 CNAME pagead2.google.com., CNAME pagead.google.akadns.net., A 216.239.59.104, A 216.239.59.99 (146)
```

При использовании фильтра `port` следует помнить о том, что мы ищем и как работает этот протокол. Некоторые стандартные протоколы, такие как HTTP или HTTPS, используют только порты 80 и 443 с транспортным протоколом TCP. Исходя из этого, можно представить порты как простой способ установления соединений, а протоколы, такие как TCP и UDP, — как методы определения того, используют ли они установленный метод. Сами по себе порты могут использоваться для чего угодно, поэтому фильтрация по порту 80 покажет весь трафик через этот порт. Однако если мы хотим перехватить весь HTTP-трафик, то использование `tcp port 80` позволит нам видеть только HTTP-трафик.

Для протоколов, которые используют как TCP, так и UDP для разных функций, например DNS, мы можем выполнить фильтрацию по одному из них `TCP/UDP port 53` или выполнить фильтрацию по `port 53`. Таким образом мы увидим весь трафик, использующий этот порт, независимо от транспортного протокола.

#### Фильтр портов

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$ ### Syntax: port [port number]
Barlogsha@htb[/htb]$ sudo tcpdump -i eth0 tcp port 443

06:17:07.311224 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [S], seq 951057939, win 8760, options [mss 1460,nop,nop,sackOK], length 0
06:17:08.222534 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [S.], seq 290218379, ack 951057940, win 5840, options [mss 1380,nop,nop,sackOK], length 0
06:17:08.222534 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 1, win 9660, length 0
06:17:08.222534 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [P.], seq 1:480, ack 1, win 9660, length 479: HTTP: GET /download.html HTTP/1.1
06:17:08.783340 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], ack 480, win 6432, length 0
06:17:08.993643 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 1:1381, ack 480, win 6432, length 1380: HTTP: HTTP/1.1 200 OK
06:17:09.123830 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 1381, win 9660, length 0
06:17:09.123830 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 1381:2761, ack 480, win 6432, length 1380: HTTP
06:17:09.324118 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 2761, win 9660, length 0
06:17:09.754737 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 2761:4141, ack 480, win 6432, length 1380: HTTP
06:17:09.864896 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [P.], seq 4141:5521, ack 480, win 6432, length 1380: HTTP
06:17:09.864896 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 5521, win 9660, length 0
06:17:09.945011 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 5521:6901, ack 480, win 6432, length 1380: HTTP
06:17:10.125270 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 6901, win 9660, length 0
06:17:10.205385 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 6901:8281, ack 480, win 6432, length 1380: HTTP
06:17:10.295515 IP dialin-145-254-160-237.pools.arcor-ip.net.3371 > 216.239.59.99.http: Flags [P.], seq 918691368:918692089, ack 778785668, win 8760, length 721: HTTP: GET /pagead/ads?client=ca-pub-2309191948673629&random=1084443430285&lmt=1082467020&format=468x60_as&output=html&url=http%3A%2F%2Fwww.ethereal.com%2Fdownload.html&color_bg=FFFFFF&color_text=333333&color_link=000000&color_url=666633&color_border=666633 HTTP/1.1
```

Помимо отдельных портов, мы также можем указать конкретные диапазоны портов, которые затем будут прослушиваться TCPdump. Прослушивание диапазона портов может быть особенно полезным, когда мы видим сетевой трафик с портов, которые не соответствуют службам, работающим на наших серверах. Например, если у нас есть веб-сервер с TCP-портами 80 и 443, работающий в определённом сегменте нашей сети, и мы внезапно видим исходящий сетевой трафик с TCP-порта 10000 или других портов, это очень подозрительно.

Фильтр `portrange`, как показано ниже, позволяет нам видеть всё, что происходит в диапазоне портов. В этом примере мы видим DNS-трафик и несколько веб-запросов HTTP.

#### Фильтр Диапазона портов

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$ ### Syntax: portrange [portrange 0-65535]
Barlogsha@htb[/htb]$ sudo tcpdump -i eth0 portrange 0-1024

tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
13:10:35.092477 IP 172.16.146.1.domain > 172.16.146.2.32824: 47775 1/0/0 CNAME autopush.prod.mozaws.net. (81)
13:10:35.093217 IP 172.16.146.2.48078 > 172.16.146.1.domain: 30234+ A? ocsp.pki.goog. (31)
13:10:35.093334 IP 172.16.146.2.48078 > 172.16.146.1.domain: 32024+ AAAA? ocsp.pki.goog. (31)
13:10:35.136255 IP 172.16.146.1.domain > 172.16.146.2.48078: 32024 2/0/0 CNAME pki-goog.l.google.com., AAAA 2607:f8b0:4002:c09::5e (94)
13:10:35.137348 IP 172.16.146.1.domain > 172.16.146.2.48078: 30234 2/0/0 CNAME pki-goog.l.google.com., A 172.217.164.67 (82)
13:10:35.137989 IP 172.16.146.2.55074 > atl26s18-in-f3.1e100.net.http: Flags [S], seq 1146136517, win 64240, options [mss 1460,sackOK,TS val 1337520268 ecr 0,nop,wscale 7], length 0
13:10:35.174443 IP atl26s18-in-f3.1e100.net.http > 172.16.146.2.55074: Flags [S.], seq 345110814, ack 1146136518, win 65535, options [mss 1430,sackOK,TS val 1000152427 ecr 1337520268,nop,wscale 8], length 0
13:10:35.174481 IP 172.16.146.2.55074 > atl26s18-in-f3.1e100.net.http: Flags [.], ack 1, win 502, options [nop,nop,TS val 1337520304 ecr 1000152427], length 0
13:10:35.174716 IP 172.16.146.2.55074 > atl26s18-in-f3.1e100.net.http: Flags [P.], seq 1:379, ack 1, win 502, options [nop,nop,TS val 1337520305 ecr 1000152427], length 378: HTTP: POST /gts1o1core HTTP/1.1
13:10:35.208007 IP atl26s18-in-f3.1e100.net.http > 172.16.146.2.55074: Flags [.], ack 379, win 261, options [nop,nop,TS val 1000152462 ecr 1337520305], length 0
```

Далее мы ищем пакеты размером менее 64 байт. Из следующего вывода видно, что в этом захвате эти пакеты в основном состояли из `SYN`, `FIN`, или `KeepAlive` пакетов. Полезным набором модификаторов могут быть «меньше» и «больше». Например, предположим, что мы хотим захватить трафик, включающий передачу файла или набора файлов. Мы знаем, что эти файлы будут больше обычного трафика. Чтобы продемонстрировать это, мы можем использовать `greater 500` (или `'>500'`), которые отобразят только пакеты размером более 500 байт. Это позволит убрать из поля зрения все лишние пакеты, которые, как мы знаем, нас не интересуют.

#### Less / Greater фильтр

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$ ### Syntax: less/greater [size in bytes]
Barlogsha@htb[/htb]$ sudo tcpdump -i eth0 less 64

06:17:07.311224 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [S], seq 951057939, win 8760, options [mss 1460,nop,nop,sackOK], length 0
06:17:08.222534 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [S.], seq 290218379, ack 951057940, win 5840, options [mss 1380,nop,nop,sackOK], length 0
06:17:08.222534 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 1, win 9660, length 0
06:17:08.783340 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], ack 480, win 6432, length 0
06:17:09.123830 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 1381, win 9660, length 0
06:17:09.324118 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 2761, win 9660, length 0
06:17:09.864896 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 5521, win 9660, length 0
06:17:10.125270 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 6901, win 9660, length 0
06:17:10.325558 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 8281, win 9660, length 0
06:17:10.806249 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 11041, win 9660, length 0
06:17:10.956465 IP 216.239.59.99.http > dialin-145-254-160-237.pools.arcor-ip.net.3371: Flags [.], ack 918692089, win 31460, length 0
06:17:11.126710 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 12421, win 9660, length 0
06:17:11.266912 IP dialin-145-254-160-237.pools.arcor-ip.net.3371 > 216.239.59.99.http: Flags [.], ack 1590, win 8760, length 0
06:17:11.527286 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 13801, win 9660, length 0
06:17:11.667488 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 16561, win 9660, length 0
06:17:11.807689 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 17941, win 9660, length 0
06:17:12.088092 IP dialin-145-254-160-237.pools.arcor-ip.net.3371 > 216.239.59.99.http: Flags [.], ack 1590, win 8760, length 0
06:17:12.328438 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 18365, win 9236, length 0
06:17:25.216971 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [F.], seq 18365, ack 480, win 6432, length 0
06:17:25.216971 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 18366, win 9236, length 0
06:17:37.374452 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [F.], seq 480, ack 18366, win 9236, length 0
06:17:37.704928 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], ack 481, win 6432, length 0
```

Выше приведён отличный пример использования `less`. Мы можем использовать модификатор `greater 500` для отображения только пакетов размером 500 байт и более. В ответ мы получили уникальный ASCII-код. Можем ли мы сказать, что здесь произошло?

#### Использование Greater

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$ sudo tcpdump -i eth0 greater 500

21:12:43.548353 IP 192.168.0.1.telnet > 192.168.0.2.1550: Flags [P.], seq 401695766:401696254, ack 2579866052, win 17376, options [nop,nop,TS val 2467382 ecr 10234152], length 488
E...;...@.................d.......C........
.%.6..)(Warning: no Kerberos tickets issued.
OpenBSD 2.6-beta (OOF) #4: Tue Oct 12 20:42:32 CDT 1999

Welcome to OpenBSD: The proactively secure Unix-like operating system.

Please use the sendbug(1) utility to report bugs in the system.
Before reporting a bug, please try to reproduce it with the latest
version of the code.  With bug reports, please try to ensure that
enough information to reproduce the problem is enclosed, and if a
known fix for it exists, include that as well.
```

`AND` В качестве модификатора он покажет нам всё, что соответствует обоим заданным требованиям. Например, `host 10.12.1.122 and tcp port 80` будет искать всё, что исходит от исходного хоста и содержит TCP- или UDP-трафик через порт 80. Чтобы фильтр захватил пакет, должны быть соблюдены оба критерия. Ниже мы видим это в действии. Здесь мы используем `host 192.168.0.1 and port 23` в качестве фильтра. Таким образом, мы увидим только трафик с этого конкретного хоста, который проходит только через порт 23.

#### Фильтр AND

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$ ### Syntax: and [requirement]
Barlogsha@htb[/htb]$ sudo tcpdump -i eth0 host 192.168.0.1 and port 23

21:12:38.387203 IP 192.168.0.2.1550 > 192.168.0.1.telnet: Flags [S], seq 2579865836, win 32120, options [mss 1460,sackOK,TS val 10233636 ecr 0,nop,wscale 0], length 0
21:12:38.389728 IP 192.168.0.1.telnet > 192.168.0.2.1550: Flags [S.], seq 401695549, ack 2579865837, win 17376, options [mss 1448,nop,wscale 0,nop,nop,TS val 2467372 ecr 10233636], length 0
21:12:38.389775 IP 192.168.0.2.1550 > 192.168.0.1.telnet: Flags [.], ack 1, win 32120, options [nop,nop,TS val 10233636 ecr 2467372], length 0
21:12:38.391363 IP 192.168.0.2.1550 > 192.168.0.1.telnet: Flags [P.], seq 1:28, ack 1, win 32120, options [nop,nop,TS val 10233636 ecr 2467372], length 27 [telnet DO SUPPRESS GO AHEAD, WILL TERMINAL TYPE, WILL NAWS, WILL TSPEED, WILL LFLOW, WILL LINEMODE, WILL NEW-ENVIRON, DO STATUS, WILL XDISPLOC]
21:12:38.537538 IP 192.168.0.1.telnet > 192.168.0.2.1550: Flags [P.], seq 1:4, ack 28, win 17349, options [nop,nop,TS val 2467372 ecr 10233636], length 3 [telnet DO AUTHENTICATION]
```

Другие модификаторы, `OR` и `NOT`, позволяют нам задавать несколько условий или отрицать что-либо. Давайте немного поиграем с этим. Что мы видим в этом выводе?

#### Базовый захват Без Фильтра

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$ sudo tcpdump -i eth0

tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
14:39:51.224071 IP 172.16.146.2 > dns.google: ICMP echo request, id 19623, seq 72, length 64
14:39:51.251635 IP dns.google > 172.16.146.2: ICMP echo reply, id 19623, seq 72, length 64
14:39:51.329340 IP 172.16.146.2.39003 > 172.16.146.1.domain: 8231+ PTR? 8.8.8.8.in-addr.arpa. (38)
14:39:51.330334 IP 172.16.146.1.domain > 172.16.146.2.39003: 8231 1/0/0 PTR dns.google. (62)
14:39:51.330613 IP 172.16.146.2.50633 > 172.16.146.1.domain: 65266+ PTR? 2.146.16.172.in-addr.arpa. (43)
14:39:51.331461 IP 172.16.146.1.domain > 172.16.146.2.50633: 65266 NXDomain* 0/0/0 (43)
14:39:51.399970 IP 172.16.146.2.54742 > 72.21.91.29.http: Flags [.], ack 358742210, win 501, options [nop,nop,TS val 1924174236 ecr 1068405332], length 0
14:39:51.420559 IP 72.21.91.29.http > 172.16.146.2.54742: Flags [.], ack 1, win 131, options [nop,nop,TS val 1068415563 ecr 1924143536], length 0
14:39:51.432107 IP 172.16.146.2.41928 > 172.16.146.1.domain: 7232+ PTR? 1.146.16.172.in-addr.arpa. (43)
14:39:51.432795 IP 172.16.146.1.domain > 172.16.146.2.41928: 7232 NXDomain* 0/0/0 (43)
14:39:51.433048 IP 172.16.146.2.47075 > 172.16.146.1.domain: 18932+ PTR? 29.91.21.72.in-addr.arpa. (42)
14:39:51.434374 IP 172.16.146.1.domain > 172.16.146.2.47075: 18932 NXDomain 0/1/0 (113)
14:39:52.225941 IP 172.16.146.2 > dns.google: ICMP echo request, id 19623, seq 73, length 64
14:39:52.252523 IP dns.google > 172.16.146.2: ICMP echo reply, id 19623, seq 73, length 64
14:39:52.683881 IP 172.16.146.2.47004 > 151.139.128.14.http: Flags [.], ack 2006616877, win 501, options [nop,nop,TS val 1585722998 ecr 2103316650], length 0
14:39:52.712283 IP 151.139.128.14.http > 172.16.146.2.47004: Flags [.], ack 1, win 507, options [nop,nop,TS val 2103326900 ecr 1585692473], length 0
```

У нас есть набор различных источников и пунктов назначения, а также несколько типов протоколов. Если бы мы использовали модификатор `OR` (или `||`) , то могли бы, например, запросить трафик с определенного хоста или только ICMP-трафик. Давайте запустим его еще раз и добавим `OR`.

#### Фильтр OR

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$ ### Syntax: or/|| [requirement]
Barlogsha@htb[/htb]$ sudo tcpdump -r sus.pcap icmp or host 172.16.146.1

reading from file sus.pcap, link-type EN10MB (Ethernet), snapshot length 262144
14:54:03.659163 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 21, length 64
14:54:03.691278 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 21, length 64
14:54:03.879882 ARP, Request who-has 172.16.146.1 tell 172.16.146.2, length 28
14:54:03.880266 ARP, Reply 172.16.146.1 is-at 8a:66:5a:11:8d:64 (oui Unknown), length 46
14:54:04.661179 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 22, length 64
14:54:04.687120 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 22, length 64
14:54:05.663097 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 23, length 64
14:54:05.686092 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 23, length 64
14:54:06.664174 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 24, length 64
14:54:06.697469 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 24, length 64
14:54:07.666273 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 25, length 64
14:54:07.701475 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 25, length 64
14:54:08.668364 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 26, length 64
14:54:08.694948 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 26, length 64
14:54:09.670523 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 27, length 64
14:54:09.694974 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 27, length 64
14:54:10.672858 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 28, length 64
14:54:10.697834 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 28, length 64
```

Теперь наш трафик выглядит немного иначе. Это связано с тем, что многие пакеты соответствовали переменной ICMP, а некоторые — переменной хоста. Таким образом, в этом выводе мы видим ARP-трафик и ICMP-трафик. Фильтр сработал, так как 172.16.146.2 соответствовал другой переменной и отображался как хост в поле источника или назначения. Теперь посмотрим, что произойдёт, если мы используем модификатор `NOT` (или `!`).

#### Фильтр NOT

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$ ### Syntax: not/! [requirement]
Barlogsha@htb[/htb]$ sudo tcpdump -r sus.pcap not icmp

14:54:03.879882 ARP, Request who-has 172.16.146.1 tell 172.16.146.2, length 28
14:54:03.880266 ARP, Reply 172.16.146.1 is-at 8a:66:5a:11:8d:64 (oui Unknown), length 46
14:54:16.541657 IP 172.16.146.2.55592 > ec2-52-211-164-46.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 3569937476:3569937513, ack 2948818703, win 501, options [nop,nop,TS val 713252991 ecr 12282469], length 37
14:54:16.568659 IP 172.16.146.2.53329 > 172.16.146.1.domain: 24866+ A? app.hackthebox.eu. (35)
14:54:16.616032 IP 172.16.146.1.domain > 172.16.146.2.53329: 24866 3/0/0 A 172.67.1.1, A 104.20.66.68, A 104.20.55.68 (83)
14:54:16.616396 IP 172.16.146.2.56204 > 172.67.1.1.https: Flags [S], seq 2697802378, win 64240, options [mss 1460,sackOK,TS val 533261003 ecr 0,nop,wscale 7], length 0
14:54:16.637895 IP 172.67.1.1.https > 172.16.146.2.56204: Flags [S.], seq 752000032, ack 2697802379, win 65535, options [mss 1400,nop,nop,sackOK,nop,wscale 10], length 0
14:54:16.637937 IP 172.16.146.2.56204 > 172.67.1.1.https: Flags [.], ack 1, win 502, length 0
14:54:16.644551 IP 172.16.146.2.56204 > 172.67.1.1.https: Flags [P.], seq 1:514, ack 1, win 502, length 513
14:54:16.667236 IP 172.67.1.1.https > 172.16.146.2.56204: Flags [.], ack 514, win 66, length 0
14:54:16.668307 IP 172.67.1.1.https > 172.16.146.2.56204: Flags [P.], seq 1:2766, ack 514, win 66, length 2765
14:54:16.668319 IP 172.16.146.2.56204 > 172.67.1.1.https: Flags [.], ack 2766, win 496, length 0
14:54:16.670536 IP ec2-52-211-164-46.eu-west-1.compute.amazonaws.com.https > 172.16.146.2.55592: Flags [P.], seq 1:34, ack 37, win 114, options [nop,nop,TS val 12294021 ecr 713252991], length 33
14:54:16.670559 IP 172.16.146.2.55592 > ec2-52-211-164-46.eu-west-1.compute.amazonaws.com.https: Flags [.], ack 34, win 501, options [nop,nop,TS val 713253120 ecr 12294021], length 0
```

Теперь всё выглядит совсем по-другому. Мы видим только ARP-трафик, а также HTTPS-трафик, который раньше не отслеживался. Это связано с тем, что мы отключили отображение ICMP-трафика с помощью `not icmp`.

---

## Фильтры предварительной обработки VS. постобработка

При использовании фильтров мы можем применять их непосредственно к захвату или при чтении файла захвата. При применении фильтров к захвату весь трафик, не соответствующий фильтру, будет отброшен. Это уменьшит объём данных в захватах и, возможно, очистит трафик, который может понадобиться нам позже. Поэтому используйте фильтры только для поиска чего-то конкретного, например для устранения неполадок с сетевым подключением. При применении фильтра к захвату мы считываем данные из файла, а фильтр анализирует файл и удаляет из вывода терминала всё, что не соответствует указанному фильтру. Использование фильтра таким образом может помочь нам в расследовании, сохранив при этом потенциально ценные данные в captures. Это не приведёт к необратимым изменениям в файле захвата, а для изменения или удаления фильтра из вывода потребуется повторно запустить команду с изменённым синтаксисом.

---

## Советы и рекомендации по устному переводу

При использовании переключателя `-S` отображаются абсолютные порядковые номера, которые могут быть очень длинными. Как правило, tcpdump отображает относительные порядковые номера, которые легче отслеживать и читать. Однако если мы будем искать эти значения в другом инструменте или журнале, то найдём пакет только по абсолютным порядковым номерам. Например, от 13245768092588 до 100.

С помощью переключателей `-v`, `-X` и `-e` можно увеличить объем собираемых данных, в то время как с помощью переключателей `-c`, `-n` `-s`, `-S`, `-q`, и можно уменьшить и модифицировать объем записываемых и просматриваемых данных.

Многие удобные опции, которые можно использовать, но которые не всегда полезны для всех, — это переключатели `-A` и `-l`. A будет отображать только текст в кодировке ASCII после строки с пакетом, а не в кодировке ASCII и Hex. `L` указывает tcpdump выводить пакеты в другом режиме. `L` будет использовать построчный буфер вместо объединения в пулы и отправки фрагментами. Это позволяет нам отправлять вывод напрямую в другой инструмент, например `grep` с помощью конвейера `|`.

#### Советы и хитрости

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$sudo tcpdump -Ar telnet.pcap

21:12:43.528695 IP 192.168.0.1.telnet > 192.168.0.2.1550: Flags [P.], seq 157:217, ack 216, win 17376, options [nop,nop,TS val 2467382 ecr 10234022], length 60
E..p;...@..p..............c.......C........
.%.6..(.Last login: Sat Nov 27 20:11:43 on ttyp2 from bam.zing.org

21:12:43.546441 IP 192.168.0.2.1550 > 192.168.0.1.telnet: Flags [.], ack 217, win 32120, options [nop,nop,TS val 10234152 ecr 2467382], length 0
E..4FP@.@.s...................d...}x.......
..)(.%.6
21:12:43.548353 IP 192.168.0.1.telnet > 192.168.0.2.1550: Flags [P.], seq 217:705, ack 216, win 17376, options [nop,nop,TS val 2467382 ecr 10234152], length 488
E...;...@.................d.......C........
.%.6..)(Warning: no Kerberos tickets issued.
OpenBSD 2.6-beta (OOF) #4: Tue Oct 12 20:42:32 CDT 1999

Welcome to OpenBSD: The proactively secure Unix-like operating system.

Please use the sendbug(1) utility to report bugs in the system.
Before reporting a bug, please try to reproduce it with the latest
version of the code.  With bug reports, please try to ensure that
enough information to reproduce the problem is enclosed, and if a
known fix for it exists, include that as well.


21:12:43.566442 IP 192.168.0.2.1550 > 192.168.0.1.telnet: Flags [.], ack 705, win 32120, options [nop,nop,TS val 10234154 ecr 2467382], length 0
E..4FQ@.@.s...................e...}x.0.....
..)*.%.6
```

Обратите внимание, что под каждой строкой вывода отображаются значения ASCII, поскольку мы используем `-A`. Это может быть полезно при быстром поиске чего-то удобочитаемого в выводе.

#### Передача данных в Grep

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$ sudo tcpdump -Ar http.cap -l | grep 'mailto:*'

reading from file http.cap, link-type EN10MB (Ethernet), snapshot length 65535
  <a href="mailto:ethereal-web[AT]ethereal.com">ethereal-web[AT]ethereal.com</a>
  <a href="mailto:free-support[AT]thewrittenword.com">free-support[AT]thewrittenword.com</a>
  <a href="mailto:ethereal-users[AT]ethereal.com">ethereal-users[AT]ethereal.com</a>
  <a href="mailto:ethereal-web[AT]ethereal.com">ethereal-web[AT]ethereal.com</a>
```

Использование `-l` таким образом позволило нам быстро просмотреть захваченные данные и выполнить поиск по ключевым словам или форматированию, которые, как мы предполагали, могли там присутствовать. В данном случае мы использовали `-l` для передачи вывода в `grep` и поиска любого вхождения фразы `mailto:*`. Таким образом мы видим каждую строку с результатами поиска, как показано выше. Использование модификаторов и перенаправление вывода могут стать быстрым способом сбора данных с веб-сайтов для поиска адресов электронной почты, стандартов именования и многого другого.

Мы можем изучить захваченные пакеты настолько глубоко, насколько захотим. Однако для этого нужно немного разбираться в структуре протоколов. Например, если мы хотим увидеть только пакеты с установленным флагом TCP SYN, мы можем использовать следующую команду:

#### Поиск флагов протокола TCP

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$ tcpdump -i eth0 'tcp[13] &2 != 0'
```

Это подсчёт до 13-го байта в структуре и проверка 2-го бита. Если он равен 1 или включен, то установлен флаг SYN.

#### В поисках флага SYN

  Фильтрация пакетов Tcpdump

```shell-session
Barlogsha@htb[/htb]$ sudo tcpdump -i eth0 'tcp[13] &2 != 0'

tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
15:18:14.630993 IP 172.16.146.2.56244 > 172.67.1.1.https: Flags [S], seq 122498858, win 64240, options [mss 1460,sackOK,TS val 534699017 ecr 0,nop,wscale 7], length 0
15:18:14.654698 IP 172.67.1.1.https > 172.16.146.2.56244: Flags [S.], seq 3728841459, ack 122498859, win 65535, options [mss 1400,nop,nop,sackOK,nop,wscale 10], length 0
15:18:15.017464 IP 172.16.146.2.60202 > a23-54-168-81.deploy.static.akamaitechnologies.com.https: Flags [S], seq 777468939, win 64240, options [mss 1460,sackOK,TS val 1348555130 ecr 0,nop,wscale 7], length 0
15:18:15.021329 IP 172.16.146.2.49652 > 104.16.88.20.https: Flags [S], seq 1954080833, win 64240, options [mss 1460,sackOK,TS val 274098564 ecr 0,nop,wscale 7], length 0
15:18:15.022640 IP 172.16.146.2.45214 > 104.18.22.52.https: Flags [S], seq 1072203471, win 64240, options [mss 1460,sackOK,TS val 1445124063 ecr 0,nop,wscale 7], length 0
15:18:15.042399 IP 104.18.22.52.https > 172.16.146.2.45214: Flags [S.], seq 215464563, ack 1072203472, win 65535, options [mss 1400,nop,nop,sackOK,nop,wscale 10], length 0
15:18:15.043646 IP a23-54-168-81.deploy.static.akamaitechnologies.com.https > 172.16.146.2.60202: Flags [S.], seq 1390108870, ack 777468940, win 28960, options [mss 1460,sackOK,TS val 3405787409 ecr 1348555130,nop,wscale 7], length 0
15:18:15.044764 IP 104.16.88.20.https > 172.16.146.2.49652: Flags [S.], seq 2086758283, ack 1954080834, win 65535, options [mss 1400,nop,nop,sackOK,nop,wscale 10], length 0
15:18:16.131983 IP 172.16.146.2.45684 > ec2-34-255-145-175.eu-west-1.compute.amazonaws.com.https: Flags [S], seq 4017793011, win 64240, options [mss 1460,sackOK,TS val 933634389 ecr 0,nop,wscale 7], length 0
15:18:16.261855 IP ec2-34-255-145-175.eu-west-1.compute.amazonaws.com.https > 172.16.146.2.45684: Flags [S.], seq 106675091, ack 4017793012, win 26847, options [mss 1460,sackOK,TS val 12653884 ecr 933634389,nop,wscale 8], length 0
```

В наши результаты включены только пакеты с установленным флагом TCP `SYN` из того, что мы видим выше.

TCPDump может стать мощным инструментом, если мы будем понимать, как устроены наши сети и как хосты взаимодействуют друг с другом. Потратьте время на изучение типичных структур заголовков протоколов, чтобы при необходимости выявить аномалию. Вот несколько ссылок для дальнейшего изучения стандартных протоколов и их структур. За исключением ссылки на Википедию, каждая ссылка ведёт непосредственно к RFC, устанавливающему стандарт для каждого протокола.

#### Ссылки на протокол RFC

|**Ссылка**|**Описание**|
|---|---|
|[IP - протокол](https://tools.ietf.org/html/rfc791)|`RFC 791` описывает IP-адрес и его функции.|
|[Протокол ICMP](https://tools.ietf.org/html/rfc792)|`RFC 792` описывает протокол ICMP и его функции.|
|[Протокол TCP](https://tools.ietf.org/html/rfc793)|`RFC 793` описывает протокол TCP и принципы его работы.|
|[Протокол UDP](https://tools.ietf.org/html/rfc768)|`RFC 768` В этой статье рассказывается о UDP и принципах его работы.|
|[Быстрые ссылки на RFC](https://en.wikipedia.org/wiki/List_of_RFCs#Topical_list)|В этой статье Википедии приведён большой список протоколов, связанных с RFC, и объясняется, как они реализованы.|
