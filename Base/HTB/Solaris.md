#htb #linux 

Solaris — это операционная система на базе Unix, разработанная компанией Sun Microsystems (позже приобретённой корпорацией Oracle) в 1990-х годах. Она известна своей надёжностью, масштабируемостью и поддержкой высокопроизводительных аппаратных и программных систем. Solaris широко используется в корпоративных средах для критически важных приложений, таких как управление базами данных, облачные вычисления и виртуализация. Например, она включает встроенный гипервизор под названием `Oracle VM Server for SPARC`, который позволяет запускать несколько виртуальных машин на одном физическом сервере. В целом, он предназначен для обработки больших объёмов данных и предоставления надёжных и безопасных услуг пользователям. Он часто используется в корпоративных средах, где безопасность, производительность и стабильность являются ключевыми требованиями.

Цель Solaris — предоставить высокостабильную, безопасную и масштабируемую платформу для корпоративных вычислений. Она обладает встроенными функциями высокой доступности, отказоустойчивости и управления системой, что делает её идеальной для критически важных приложений. Она широко используется в банковском, финансовом и государственном секторах, где безопасность, надёжность и производительность имеют первостепенное значение. Она также используется в крупных центрах обработки данных, средах облачных вычислений и на платформах виртуализации. Такие компании, как Amazon, IBM и Dell, используют Solaris в своих продуктах и сервисах, что подчёркивает его важность в отрасли.

---

## Дистрибутивы Linux против Solaris

Дистрибутивы Solaris и Linux — это два типа операционных систем, которые существенно отличаются друг от друга. Во-первых, Solaris — это проприетарная операционная система, принадлежащая и разрабатываемая корпорацией Oracle, и её исходный код недоступен широкой публике. В отличие от неё, большинство дистрибутивов Linux имеют открытый исходный код, то есть любой может изменять и использовать их исходный код. Кроме того, дистрибутивы Linux обычно используют файловую систему Zettabyte (`ZFS`), которая является очень продвинутой файловой системой с такими функциями, как сжатие данных, создание снимков и высокая масштабируемость. С другой стороны, в Solaris используется средство управления сервисами (`SMF`), которое представляет собой передовую систему управления сервисами, обеспечивающую более высокую надёжность и доступность системных сервисов.

|**Каталог**|**Описание**|
|---|---|
|`/`|Корневой каталог содержит все остальные каталоги и файлы в файловой системе.|
|`/bin`|Он содержит основные системные двоичные файлы, необходимые для загрузки и основных системных операций.|
|`/boot`|Каталог загрузки содержит файлы, связанные с загрузкой, такие как загрузчик и образы ядра.|
|`/dev`|Каталог dev содержит файлы устройств, которые представляют физические и логические устройства, подключённые к системе.|
|`/etc`|Каталог etc содержит файлы конфигурации системы, такие как сценарии запуска системы и данные аутентификации пользователей.|
|`/home`|Домашние каталоги пользователей.|
|`/kernel`|Этот каталог содержит модули ядра и другие файлы, связанные с ядром.|
|`/lib`|Каталог для библиотек, необходимых для двоичных файлов в каталогах /bin и /sbin.|
|`/lost+found`|Этот каталог используется инструментом проверки и восстановления целостности файловой системы для хранения восстановленных файлов.|
|`/mnt`|Каталог для временного монтирования файловых систем.|
|`/opt`|Этот каталог содержит дополнительные пакеты программного обеспечения, которые установлены в системе.|
|`/proc`|Каталог proc предоставляет доступ к системным процессам и состоянию ядра в виде файлов.|
|`/sbin`|Этот каталог содержит системные двоичные файлы, необходимые для задач системного администрирования.|
|`/tmp`|В этом каталоге хранятся временные файлы, созданные системой и приложениями.|
|`/usr`|Каталог usr содержит общесистемные данные и программы, доступные только для чтения, такие как документация, библиотеки и исполняемые файлы.|
|`/var`|Этот каталог содержит файлы с переменными данными, такие как системные журналы, почтовые очереди и очереди печати.|

Solaris обладает рядом уникальных функций, которые отличают её от других операционных систем. Одной из её ключевых особенностей является поддержка высокопроизводительных аппаратных и программных систем. Она предназначена для работы с крупными центрами обработки данных и сложными сетевыми инфраструктурами и может обрабатывать большие объёмы данных без каких-либо проблем с производительностью.

Что касается управления пакетами, в Solaris используется менеджер пакетов Image Packaging System (`IPS`), который обеспечивает мощный и гибкий способ управления пакетами и обновлениями. Solaris также предоставляет расширенные функции безопасности, такие как управление доступом на основе ролей (`RBAC`) и обязательное управление доступом, которые доступны не во всех дистрибутивах Linux.

---

## Различия

Давайте подробнее рассмотрим различия между дистрибутивами Solaris и Linux. Одно из самых важных различий заключается в том, что исходный код не является открытым и известен только в узких кругах. Это означает, что, в отличие от Ubuntu или многих других дистрибутивов, исходный код не может быть просмотрен и проанализирован широкой публикой. Таким образом, основные различия можно разделить на следующие категории:

- Файловая система
- Управление процессами
- Управление пакетами
- Ядро и аппаратная поддержка
- Системный мониторинг
- Безопасность

Чтобы лучше понять различия, давайте рассмотрим несколько примеров и команд.

#### Системная информация

В Ubuntu мы используем команду `uname` для отображения информации о системе, такой как название ядра, имя хоста и операционной системы. Это может выглядеть так:

  Солярис

```shell-session
Barlogsha@htb[/htb]$ uname -a

Linux ubuntu 5.4.0-1045 #48-Ubuntu SMP Fri Jan 15 10:47:29 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
```

С другой стороны, в Solaris для отображения системной информации, включая версию Solaris, тип оборудования и уровень обновлений, можно использовать команду `showrev`. Вот пример вывода:

  Солярис

```shell-session
$ showrev -a

Hostname: solaris
Kernel architecture: sun4u
OS version: Solaris 10 8/07 s10s_u4wos_12b SPARC
Application architecture: sparc
Hardware provider: Sun_Microsystems
Domain: sun.com
Kernel version: SunOS 5.10 Generic_139555-08
```

Основное различие между этими двумя командами заключается в том, что `showrev` предоставляет более подробную информацию о системе Solaris, например, об уровне обновлений и поставщике оборудования, а `uname` предоставляет только базовую информацию о системе Linux.

#### Установка пакетов

В Ubuntu для установки пакетов используется команда `apt-get` . Это может выглядеть следующим образом:

  Солярис

```shell-session
Barlogsha@htb[/htb]$ sudo apt-get install apache2
```

Однако в Solaris нам нужно использовать `pkgadd` для установки таких пакетов, как `SUNWapchr`.

  Солярис

```shell-session
$ pkgadd -d SUNWapchr
```

Основное различие между этими двумя командами заключается в синтаксисе и используемом менеджере пакетов. В Ubuntu для управления пакетами используется Advanced Packaging Tool (APT), а в Solaris — Solaris Package Manager (SPM). Также обратите внимание, что в этом случае мы не используем `sudo` . Это связано с тем, что в Solaris использовался инструмент управления привилегиями `RBAC` , который позволял назначать пользователям подробные разрешения. Однако `sudo` поддерживается с Solaris 11.

#### Управление разрешениями

В системах Linux, таких как Ubuntu, а также в Solaris, команда `chmod` используется для изменения прав доступа к файлам и каталогам. Вот пример команды, которая предоставляет владельцу файла права на чтение, запись и выполнение:

  Солярис

```shell-session
Barlogsha@htb[/htb]$ chmod 700 filename
```

Чтобы найти файлы с определёнными разрешениями в Ubuntu, мы используем команду `find` . Давайте рассмотрим пример файла с установленным битом SUID:

  Солярис

```shell-session
Barlogsha@htb[/htb]$ find / -perm 4000
```

Чтобы найти файлы с определёнными разрешениями, например, с установленным битом SUID в Solaris, мы также можем использовать команду find, но с небольшими изменениями.

  Солярис

```shell-session
$ find / -perm -4000
```

Основное различие между этими двумя командами заключается в использовании `-` перед значением разрешения в команде Solaris. Это связано с тем, что в Solaris используется другая система разрешений, чем в Linux.

#### NFS в Solaris

В Solaris есть собственная реализация NFS, которая немного отличается от дистрибутивов Linux, таких как Ubuntu. В Solaris сервер NFS можно настроить с помощью команды `share`, которая используется для совместного использования каталога по сети, а также позволяет задавать различные параметры, такие как права на чтение/запись, ограничения доступа и многое другое. Чтобы совместно использовать каталог по NFS в Solaris, можно использовать следующую команду:

  Солярис

```shell-session
$ share -F nfs -o rw /export/home
```

Эта команда предоставляет общий доступ к каталогу `/export/home` с правами на чтение и запись через NFS. Клиент NFS может смонтировать файловую систему NFS с помощью команды `mount` так же, как и в Ubuntu. Чтобы смонтировать файловую систему NFS в Solaris, нам нужно указать имя сервера и путь к общему каталогу. Например, чтобы смонтировать общий ресурс NFS с сервера с IP-адресом `10.129.15.122` и общим каталогом `/nfs_share`, мы используем следующую команду:

  Солярис

```shell-session
Barlogsha@htb[/htb]$ mount -F nfs 10.129.15.122:/nfs_share /mnt/local
```

В Solaris конфигурация NFS хранится в файле `/etc/dfs/dfstab` . Этот файл содержит записи для каждого общего каталога, а также различные параметры для общего доступа по NFS.

  Солярис

```shell-session
# cat /etc/dfs/dfstab

share -F nfs -o rw /export/home
```

#### Отображение процесса

Сопоставление процессов — важный аспект системного администрирования и устранения неполадок. Команда `lsof` — это мощная утилита, которая выводит список всех файлов, открытых процессом, включая сетевые сокеты и другие файловые дескрипторы, которые мы можем использовать в дистрибутивах Debian, таких как Ubuntu. Мы можем использовать `lsof` для вывода списка всех файлов, открытых процессом. Например, чтобы вывести список всех файлов, открытых процессом веб-сервера Apache, мы можем использовать следующую команду:

  Солярис

```shell-session
Barlogsha@htb[/htb]$ sudo lsof -c apache2
```

В Solaris для вывода списка всех файлов, открытых процессом, можно использовать команду `pfiles` . Например, чтобы вывести список всех файлов, открытых процессом веб-сервера Apache, можно использовать следующую команду:

  Солярис

```shell-session
$ pfiles `pgrep httpd`
```

Эта команда выводит список всех файлов, открытых процессом веб-сервера Apache. Вывод команды `pfiles` аналогичен выводу команды `lsof` и содержит информацию о типе файлового дескриптора, номере файлового дескриптора и имени файла.

#### Доступ к исполняемому файлу

В Solaris используется `truss` — очень полезная утилита для разработчиков и системных администраторов, которым необходимо отлаживать сложные программные проблемы в операционной системе Solaris. Отслеживая системные вызовы, выполняемые процессом, `truss` может помочь определить источник ошибок, проблем с производительностью и других проблем, а также раскрыть конфиденциальную информацию, которая может появиться в процессе разработки приложений или обслуживания системы. Утилита также может предоставлять подробную информацию о системных вызовах, включая передаваемые им аргументы и возвращаемые значения, что позволяет пользователям лучше понимать поведение своих приложений и базовой операционной системы.

`Strace` Это альтернатива `truss` для Ubuntu, которая является важным инструментом как для системных администраторов, так и для разработчиков. Она помогает им диагностировать и устранять неполадки в режиме реального времени. Она позволяет пользователям анализировать взаимодействие между операционной системой и работающими в ней приложениями, что особенно полезно в сложных и критически важных средах. С помощью `truss` пользователи могут быстро выявлять и изолировать проблемы, связанные с производительностью приложений, подключением к сети и использованием системных ресурсов.

Например, чтобы отследить системные вызовы, выполняемые процессом веб-сервера Apache, можно использовать следующую команду:

  Солярис

```shell-session
Barlogsha@htb[/htb]$ sudo strace -p `pgrep apache2`
```

Вот пример того, как использовать `truss` для отслеживания системных вызовов, выполняемых командой `ls` в Solaris:

  Солярис

```shell-session
$ truss ls

execve("/usr/bin/ls", 0xFFBFFDC4, 0xFFBFFDC8)  argc = 1
...SNIP...
```

Вывод похож на `strace`, но формат немного отличается. Одно из различий между `strace` и `truss` заключается в том, что `truss` может отслеживать сигналы, отправляемые процессу, а `strace` — нет. Другое различие заключается в том, что `truss` может отслеживать системные вызовы дочерних процессов, а `strace` — только системные вызовы процесса, указанного в командной строке.