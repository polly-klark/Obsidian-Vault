#linux 

Иногда вам может понадобиться удалить или форматировать жесткий диск для некоторых операций.

Наиболее распространенным вариантом использования для полного cтирания устройства будет то, когда устройство будет clfyj или продано, поскольку это действительно важно для защиты данных.

Есть несколько команд, которые могут помочь вам выполнить эти операции в Linux.

# dd

Команда dd может помочь вам скопировать и преобразовать файл.

Команда может перезаписать весь диск нулями и значительно быстрее, чем генерировать гигабайты случайных данных.

Он предлагает некоторые операторы, которые вы можете использовать, чтобы указать, какое форматирование вы хотите.

Синтаксис:

```
dd if=source of=target [Options]
```

Только суперпользователь может запустить эту команду, потому что вы можете столкнуться с большими потерями данных из-за неправильного использования.

Пример:

```
dd if=/dev/zero of=/dev/sda2 bs=512 count=1
1+0 records in
1+0 records out
512 bytes copied, 0.0116875 s, 43.8 kB/s
```

Вы можете стереть диск, записав новые данные за каждый бит.

Чтобы еще больше усложнить процесс восстановления, мы будем записывать весь диск со случайными данными.

```
dd if=/dev/urandom of=/dev/sda2 bs=4096
```

# shred

Команда shred используется для многократной перезаписи указанных файлов и затруднения для восстановления данных.

Синтаксис приведен ниже.

```
shred [option] target
```

Команда также перезаписывает данные в файле или целое устройство со случайными битами, что почти невозможно восстановить.

Эта команда навсегда удаляет данные.

Команда имеет некоторые параметры, которые можно использовать для очистки диска:

-f изменяет разрешения  
-n (iterations = N) перезаписывает N раз вместо значения по умолчанию (3)  
-s (size = N) определяет количество байтов,  
-u усекает и удаляет файлы после перезаписи  
-v показывает подробную информацию о прогрессе  
-x не округляет размеры файлов до следующего полного блока  
-z добавляет окончательную перезапись с нулями  
-u удаляет файл после перезаписи

Итак, чтобы стереть раздел, вы можете использовать:

```
shred -vfz -n 10 /dev/sda2
shred: /dev/sda2: pass 1/11 (random)...
shred: /dev/sda2: pass 2/11 (ffffff)...
shred: /dev/sda2: pass 3/11 (249249)...
shred: /dev/sda2: pass 4/11 (000000)...
shred: /dev/sda2: pass 5/11 (555555)...
shred: /dev/sda2: pass 6/11 (random)...
shred: /dev/sda2: pass 7/11 (6db6db)...
shred: /dev/sda2: pass 8/11 (aaaaaa)...
shred: /dev/sda2: pass 9/11 (db6db6)...
shred: /dev/sda2: pass 10/11 (random)...
shred: /dev/sda2: pass 11/11 (000000)...
```

Можно использовать shred со случайными данными, как показано ниже.

```
shred -v --random-source=/dev/urandom -n10 /dev/sda2
shred: /dev/sda2: pass 1/10 (random)...
shred: /dev/sda2: pass 2/10 (db6db6)...
shred: /dev/sda2: pass 3/10 (aaaaaa)...
shred: /dev/sda2: pass 4/10 (ffffff)...
shred: /dev/sda2: pass 5/10 (6db6db)...
shred: /dev/sda2: pass 6/10 (random)...
shred: /dev/sda2: pass 7/10 (249249)...
shred: /dev/sda2: pass 8/10 (555555)...
shred: /dev/sda2: pass 9/10 (000000)...
shred: /dev/sda2: pass 10/10 (random)...
```

# wipe

Команда wipe может использоваться для безопасного удаления файлов с магнитных носителей.

Вы можете использовать команду wipe для очистки данных на диске.

Синтаксис:

```
wipe [options] <target>
```

Команда по умолчанию отсутствует, ее необходимо установить.

На **Ubuntu 17.04**

```
apt install wipe
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
wipe
0 upgraded, 1 newly installed, 0 to remove and 322 not upgraded.
Need to get 41.8 kB of archives.
```

На **Centos 7** вам нужно сначала скачать rpmforge

```
wget [http://ftp.tu-chemnitz.de/pub/linux/dag/redhat/el7/en/x86_64/rpmforge/RPMS/rpmforge-release-0.5.3-1.el7.rf.x86_64.rpm](http://ftp.tu-chemnitz.de/pub/linux/dag/redhat/el7/en/x86_64/rpmforge/RPMS/rpmforge-release-0.5.3-1.el7.rf.x86_64.rpm)
```

Установим его

```
rpm -Uvh rpmforge-release*rpm
warning: rpmforge-release-0.5.3-1.el7.rf.x86_64.rpm: Header V3 DSA/SHA1 Signature, key ID 6b8d79e6: NOKEY
Preparing... ################################# [100%]
Updating / installing...
1:rpmforge-release-0.5.3-1.el7.rf ################################# [100%]
```

Теперь установим утилиту wipe

```
yum install wipe
Loaded plugins: fastestmirror
rpmforge | 1.9 kB 00:00:00
rpmforge/primary_db | 125 kB 00:00:15
```

Вы можете стереть раздел, как показано ниже:

```
yum install wipe
Loaded plugins: fastestmirror
rpmforge | 1.9 kB 00:00:00
rpmforge/primary_db | 125 kB 00:00:15
```

# scrub

Команда scrub может использоваться для перезаписи данных с использованием шаблонов.

Команда по умолчанию недоступна, ее необходимо установить в вашей системе Linux.

Синтаксис

```
scrub [option] <target>
```

Чтобы очистить жесткий диск, вы можете сделать так, как показано ниже.

```
scrub /dev/sda5
scrub: using NNSA NAP-14.1-C patterns
scrub: please verify that device size below is correct!
scrub: scrubbing /dev/sda5 1071644672 bytes (~1022MB)
scrub: random |................................................|
scrub: random |................................................|
scrub: 0x00 |................................................|
scrub: verify |................................................|
```

Scrub использует некоторые методы для работы на дисках.

Вы можете использовать метод dod для очистки дисков.

```
scrub -p dod /dev/sda5 -f
scrub: using DoD 5220.22-M patterns
scrub: please verify that device size below is correct!
scrub: scrubbing /dev/sda5 1071644672 bytes (~1022MB)
scrub: random |................................................|
scrub: 0x00 |................................................|
scrub: 0xff |................................................|
scrub: verify |................................................|
```