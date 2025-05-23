#htb #linux 

## Важность поиска

Очень важно уметь находить нужные файлы и папки. Как только мы получим доступ к системе на базе Linux, нам нужно будет найти файлы конфигурации, скрипты, созданные пользователями или администратором, а также другие файлы и папки. Нам не придётся вручную просматривать каждую папку и проверять, когда она была изменена в последний раз. Есть несколько инструментов, которые помогут упростить эту задачу.

---

## Which

Один из распространённых инструментов — `which`. Этот инструмент возвращает путь к файлу или ссылке, которые необходимо выполнить. Это позволяет нам определить, доступны ли в операционной системе определённые программы, такие как cURL, netcat, wget, python, gcc. Давайте воспользуемся этим инструментом для поиска Python в нашем интерактивном экземпляре.

  Поиск файлов и каталогов

```shell-session
Barlogsha@htb[/htb]$ which python

/usr/bin/python
```

Если искомой программы не существует, результаты поиска не будут отображаться.

---

## Find

Ещё один удобный инструмент — `find`. Помимо функции поиска файлов и папок, этот инструмент также содержит функцию фильтрации результатов. Мы можем использовать такие параметры фильтрации, как размер файла или дата. Мы также можем указать, что будем искать только файлы или папки.

#### Синтаксис - find

  Поиск файлов и каталогов

```shell-session
Barlogsha@htb[/htb]$ find <location> <options>
```

Давайте рассмотрим пример того, как может выглядеть такая команда с несколькими параметрами.

  Поиск файлов и каталогов

```shell-session
Barlogsha@htb[/htb]$ find / -type f -name *.conf -user root -size +20k -newermt 2020-03-03 -exec ls -al {} \; 2>/dev/null

-rw-r--r-- 1 root root 136392 Apr 25 20:29 /usr/src/linux-headers-5.5.0-1parrot1-amd64/include/config/auto.conf
-rw-r--r-- 1 root root 82290 Apr 25 20:29 /usr/src/linux-headers-5.5.0-1parrot1-amd64/include/config/tristate.conf
-rw-r--r-- 1 root root 95813 May  7 14:33 /usr/share/metasploit-framework/data/jtr/repeats32.conf
-rw-r--r-- 1 root root 60346 May  7 14:33 /usr/share/metasploit-framework/data/jtr/dynamic.conf
-rw-r--r-- 1 root root 96249 May  7 14:33 /usr/share/metasploit-framework/data/jtr/dumb32.conf
-rw-r--r-- 1 root root 54755 May  7 14:33 /usr/share/metasploit-framework/data/jtr/repeats16.conf
-rw-r--r-- 1 root root 22635 May  7 14:33 /usr/share/metasploit-framework/data/jtr/korelogic.conf
-rwxr-xr-x 1 root root 108534 May  7 14:33 /usr/share/metasploit-framework/data/jtr/john.conf
-rw-r--r-- 1 root root 55285 May  7 14:33 /usr/share/metasploit-framework/data/jtr/dumb16.conf
-rw-r--r-- 1 root root 21254 May  2 11:59 /usr/share/doc/sqlmap/examples/sqlmap.conf
-rw-r--r-- 1 root root 25086 Mar  4 22:04 /etc/dnsmasq.conf
-rw-r--r-- 1 root root 21254 May  2 11:59 /etc/sqlmap/sqlmap.conf
```

Теперь давайте подробнее рассмотрим параметры, которые мы использовали в предыдущей команде. Если мы наведём курсор мыши на соответствующие параметры, появится небольшое окно с пояснениями. Эти пояснения также можно найти в других модулях, что поможет нам, если мы ещё не знакомы с одним из инструментов.

|**Вариант**|**Описание**|
|---|---|
|`-type f`|Таким образом мы определяем тип искомого объекта. В данном случае '`f`' означает '`file`'.|
|`-name *.conf`|С помощью '`-name`' мы указываем имя файла, который ищем. Звездочка (`*`) обозначает 'все' файлы с расширением '`.conf`'.|
|`-user root`|Этот параметр фильтрует все файлы, владельцем которых является пользователь root.|
|`-size +20k`|Затем мы можем отфильтровать все найденные файлы и указать, что хотим видеть только файлы размером более 20 килобайт.|
|`-newermt 2020-03-03`|С помощью этой опции мы устанавливаем дату. Будут отображаться только файлы, созданные после указанной даты.|
|`-exec ls -al {} \;`|Этот параметр выполняет указанную команду, используя фигурные скобки в качестве заполнителей для каждого результата. Обратная косая черта предотвращает интерпретацию следующего символа оболочкой, поскольку в противном случае точка с запятой завершила бы команду и не дошла бы до перенаправления.|
|`2>/dev/null`|Это `STDERR` перенаправление в '`null device`', к которому мы вернёмся в следующем разделе. Это перенаправление гарантирует, что в терминале не будут отображаться ошибки. Это перенаправление должно `not` быть опцией команды 'find'.|

---

## Locate

Поиск по всей системе наших файлов и каталогов для выполнения множества различных запросов займёт много времени. Команда `locate` предлагает более быстрый способ поиска по системе. В отличие от команды `find` команда `locate` работает с локальной базой данных, которая содержит всю информацию о существующих файлах и папках. Мы можем обновить эту базу данных с помощью следующей команды.

  Поиск файлов и каталогов

```shell-session
Barlogsha@htb[/htb]$ sudo updatedb
```

Если мы теперь выполним поиск по всем файлам с расширением «`.conf`», вы увидите, что этот поиск выдаёт результаты намного быстрее, чем при использовании `find`.

  Поиск файлов и каталогов

```shell-session
Barlogsha@htb[/htb]$ locate *.conf

/etc/GeoIP.conf
/etc/NetworkManager/NetworkManager.conf
/etc/UPower/UPower.conf
/etc/adduser.conf
<SNIP>
```

Однако у этого инструмента не так много вариантов фильтров, которые мы можем использовать. Поэтому всегда стоит подумать, можем ли мы использовать команду `locate` или вместо неё команду `find`. Это всегда зависит от того, что мы ищем.