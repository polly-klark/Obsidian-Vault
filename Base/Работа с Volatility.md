#linux #forensic #writeup #volatility

```
$ git clone https://github.com/volatilityfoundation/volatility3.git
$ cd volatility3
```

```
$ python3 vol.py -f dump.mem banner
Volatility 3 Framework 2.3.1
Progress: 100.00 PDB scanning finished 
Offset Banner

0x42400200 Linux version 5.15.0-43-generic (buildd@lcy02-amd64-076) (gcc (Ubuntu 11.2.0-19ubuntu1) 11.2.0, GNU ld (GNU Binutils for Ubuntu) 2.38) #46-Ubuntu SMP Tue Jul 12 10:30:17 UTC 2022 (Ubuntu 5.15.0-43.46-generic 5.15.39)
0x437c3718 Linux version 5.15.0-43-generic (buildd@lcy02-amd64-076) (gcc (Ubuntu 11.2.0-19ubuntu1) 11.2.0, GNU ld (GNU Binutils for Ubuntu) 2.38) #46-Ubuntu SMP Tue Jul 12 10:30:17 UTC 2022 (Ubuntu 5.15.0-43.46-generic 5.15.39)9)
```

Это определяет следующее:

-   **ОС**: Ubuntu 22.04
-   **Ядро**: Linux версии 5.15.0-43-generic

**Примечание**: Поскольку это самые последние версии, не было доступных профилей волатильности. Честно говоря, я не смог заставить Volatility 2 работать с Ubuntu 22 даже после успешного создания профиля (`KeyError: 'DW_AT_data_member_location'`). Дайте мне знать, если вы смогли, так как все учатся! :)

**Создание профиля + таблица символов:**

Для запуска плагинов Volatility нам необходимо создать [таблицу символов](https://volatility3.readthedocs.io/en/latest/symbol-tables.html#) в `.json`формате. Они могут быть сгенерированы из файлов [DWARF](https://en.wikipedia.org/wiki/DWARF) с помощью [](https://github.com/volatilityfoundation/dwarf2json)инструмента dwarf2json. Вероятно, самая сложная часть - найти ядро с символами отладки для версии Linux`5.15.0-43-generic`. Полный список доступен [здесь](http://ddebs.ubuntu.com/pool/main/l/linux/), но [`linux-image-unsigned-5.15.0-43-generic-dbgsym_5.15.0-43.46_amd64.ddeb`](http://ddebs.ubuntu.com/pool/main/l/linux/linux-image-unsigned-5.15.0-43-generic-dbgsym_5.15.0-43.46_amd64.ddeb)это та версия, которая нам нужна. После расширения архива соответствующий файл, который нам нужен`vmlinux-5.15.0-43-generic`, - это КАРЛИК, расположенный внутри `usr/lib/debug/boot`.

Затем мы клонируем [](https://github.com/volatilityfoundation/dwarf2json)инструмент dwarf2json из репозитория Volatility и создадим его:

```
$ git clone https://github.com/volatilityfoundation/dwarf2json 
$ cd dwarf2json
$ go build
```

Наконец, мы можем запустить:

```
$ dwarf2json linux --elf vmlinux-5.15.0-43- универсальный> ubuntu22.json
```

Скопируйте таблицу символов в `volatility3/volatility3/symbols/linux`, и ваш профиль должен быть настроен. Символы также доступны [здесь](https://github.com/project-sekai-ctf/sekaictf-2022/blob/main/forensics/symbolic-needs/solution/symbols.zip)!

# Часть 1

Как только у нас будет действительный`symbols.json`, мы можем запустить плагины Volatility 3. Первое, что мы всегда запускаем`linux.bash`, - это отображение истории bash:

```
$ python3 vol.py -f dump.mem linux.bash
Volatility 3 Framework 2.3.1
Progress:  100.00        Stacking attempts finished                 
PID Process CommandTime Command

1863    bash    2022-08-29 13:45:56.000000    72.48.117.53.84.48.110.95.119.51.95.52.114.51.95.49.110.33.33.33
```

Они легко идентифицируются как ASCII-коды. Преобразовать `72 48 117 53 84 48 110 95 119 51 95 52 114 51 95 49 110 33 33 33`в текст и получить флаг: `SEKAI{H0u5T0n_w3_4r3_1n

# Часть 2

Давайте продолжим с `linux.psaux` плагин для сбора и отображения всех процессов:

```
$ python3 vol.py -f dump.mem linux.psaux
Volatility 3 Framework 2.3.1
Progress:  100.00        Stacking attempts finished                 
PID PPID    COMM    ARGS
...
1731    985 gsd-xsettings    /usr/libexec/gsd-xsettings
1787    985 ibus-x11    /usr/libexec/ibus-x11
1845    985 gnome-terminal- /usr/libexec/gnome-terminal-server
1863    1845    bash    bash
1878    1863    ncat    ncat -lvnp 1234 -c echo N4GQ2CQAAAAAAEFG5JRPEAIAADRQAAAAAAAAAAAAAAAAAAAAAAAAACIAAAAEAAAAABZ6QAAAABSAAZABNQAFUAD2A5SQA2QBMQBBSAC2AJLQA3QLAEAACAABABSQGZADQMAQCADFASBQAAIALEAGOAC2AVSQMZAEMQCYGAUPBZNAOZIHUAEKCAFABGQQAWQFK4AGIAIEAACABAYDAEAG4CBRABZS65YBAEAACAABABMQAAIAMQDFUCTFBNSQVAYBMQDWIAMFAIMQAWQKMUGGKCVABVSQ4ZIKQMAWICDFBZSQVAYBMQEBMAAYAALQBIIBQMAVUCTHABNA6ZIQMQAGKDTFBKBQCZAIQMBUIAC5CRNBCZIPUAJGKBLFCNSQUZIRMUIWICAXACCQEGIAMQDYGATEAIMAAGIAUEAQCADRLFSQGZAJQMAQCADEAFJQAKIK5EAAAAAAJ3UQCAAAAB5BQVLTMFTWKORAFYXXOYLMNRSXIIDQMFZXG53POJSHUDLCNFYDGOLMNFZXILTUPB2NUALSNQKAAAAAABS6KHWNHGMBH4AWTJ3BE3XKCYFWPVQQSR6WWFSHC2WEUE3P5AP7G46OA3IBWAIA5EBAAAAA5EGAAAAA3ICVO4TPNZTSSFG2ANZXS462ARQXEZ3W3IEHAYLTON3W64TE3ICXA4TJNZ2NUBDFPBUXJWQFO5XXEZDT3ICG64DFN3NACZW2ARZGKYLE3IFHG4DMNF2GY2LOMVZ5UBDDN5SGLWQDMJUW5WQDON2HFWQFPJTGS3DM3IBWYZLO3IEG23TFNVXW42LD3ICXEYLOM5S5UALJ3IDGC4DQMVXGJWQDNFXHJKIAOINQAAAAOINQAAAA7IEHIZLTOQZC44DZ3IEDY3LPMR2WYZJ6AEAAAADTEIAAAAAIAABAEDQBAYAQQAIIAECAGDACBYARZ7YEAMIAGIQBAQBRIARGAEGAE=== | base32 -d > file.pyc
1886    1147    update-notifier update-notifier
1911    1863    sudo    sudo insmod LiME/src/lime-5.15.0-43-generic.ko path=dump.mem format=lime
1918    1911    sudo    sudo insmod LiME/src/lime-5.15.0-43-generic.ko path=dump.mem format=lime
...
```

Run the command `echo [PUT YOUR BASE32 HERE] | base32 -d > file.pyc` to convert this base32 into a binary. Run the `.pyc` with `python3` and it will give:

```
$ python3 file.pyc 
Usage: ./wallet password
```

Passing a random argument results in a `FileNotFoundError`:

```
$ python3 file.pyc password
Traceback (most recent call last):
 File "test2.py", line 12, in <module>
FileNotFoundError: [Errno 2] No such file or directory: 'bip39list.txt'
```

We can find this wordlist in the [bitcoin/bips](https://github.com/bitcoin/bips/blob/master/bip-0039/english.txt). If you run the binary again with the same argument it just outputs "Wrong". We'll need to disassemble this:

Disassemble the bytecode with the `dis` module:

```
import dis
import marshal

with open('file.pyc', 'rb') as f:
    f.seek(16)
    print(dis.dis(marshal.load(f)))
```