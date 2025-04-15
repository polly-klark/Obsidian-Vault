#htb #linux 

Мы всегда будем сталкиваться с инструментами, дополнительные параметры которых мы не знаем по памяти, или с инструментами, которых мы никогда раньше не видели. Поэтому важно знать, как можно помочь себе в знакомстве с этими инструментами. Первые два способа — это страницы руководства и функции справки. Всегда полезно сначала ознакомиться с инструментом, который мы хотим попробовать. Мы также узнаем о некоторых возможных хитростях с некоторыми инструментами, которые, как мы думали, были невозможны. На страницах руководства мы найдём подробные инструкции с пояснениями.

## Синтаксис:

  Получение помощи

```shell-session
Barlogsha@htb[/htb]$ man <tool>
```

Давайте посмотрим на пример:

### Пример:

  Получение помощи

```shell-session
Barlogsha@htb[/htb]$ man curl
```

  Получение помощи

```shell-session
curl(1)                                                             Curl Manual                                                            curl(1)

NAME
       curl - transfer a URL

SYNOPSIS
       curl [options] [URL...]

DESCRIPTION
       curl  is  a tool to transfer data from or to a server, using one of the supported protocols (DICT, FILE, FTP, FTPS, GOPHER, HTTP, HTTPS,  
       IMAP, IMAPS,  LDAP,  LDAPS,  POP3,  POP3S,  RTMP, RTSP, SCP, SFTP, SMB, SMBS, SMTP, SMTPS, TELNET, and TFTP). The command is designed to work without user interaction.

       curl offers a busload of useful tricks like proxy support, user authentication, FTP upload, HTTP post, SSL connections, cookies, file transfer resume, Metalink,  and more. As we will see below, the number of features will make our head spin!

       curl is powered by libcurl for all transfer-related features.  See libcurl(3) for details.

Manual page curl(1) line 1 (press h for help or q to quit)

```

Рассмотрев несколько примеров, мы также можем быстро ознакомиться с дополнительными параметрами, не просматривая всю документацию. Это можно сделать несколькими способами.

## Синтаксис:

  Получение помощи

```shell-session
Barlogsha@htb[/htb]$ <tool> --help
```

### Пример:

  Получение помощи

```shell-session
Barlogsha@htb[/htb]$ curl --help

Usage: curl [options...] <url>
     --abstract-unix-socket <path> Connect via abstract Unix domain socket
     --anyauth       Pick any authentication method
 -a, --append        Append to target file when uploading
     --basic         Use HTTP Basic Authentication
     --cacert <file> CA certificate to verify peer against
     --capath <dir>  CA directory to verify peer against
 -E, --cert <certificate[:password]> Client certificate file and password
<SNIP>
```

Мы также можем использовать его краткую версию:

## Синтаксис:

  Получение помощи

```shell-session
Barlogsha@htb[/htb]$ <tool> -h
```

### Пример:

  Получение помощи

```shell-session
Barlogsha@htb[/htb]$ curl -h

Usage: curl [options...] <url>
     --abstract-unix-socket <path> Connect via abstract Unix domain socket
     --anyauth       Pick any authentication method
 -a, --append        Append to target file when uploading
     --basic         Use HTTP Basic Authentication
     --cacert <file> CA certificate to verify peer against
     --capath <dir>  CA directory to verify peer against
 -E, --cert <certificate[:password]> Client certificate file and password
<SNIP>
```

Как мы видим, в этом примере результаты не отличаются друг от друга. Другой инструмент, который может быть полезен на начальном этапе, — `apropos`. На каждой странице руководства есть краткое описание. Этот инструмент ищет в описаниях примеры заданного ключевого слова.

## Синтаксис:

  Получение помощи

```shell-session
Barlogsha@htb[/htb]$ apropos <keyword>
```

### Пример:

  Получение помощи

```shell-session
Barlogsha@htb[/htb]$ apropos sudo

sudo (8)             - execute a command as another user
sudo.conf (5)        - configuration for sudo front end
sudo_plugin (8)      - Sudo Plugin API
sudo_root (8)        - How to run administrative commands
sudoedit (8)         - execute a command as another user
sudoers (5)          - default sudo security policy plugin
sudoreplay (8)       - replay sudo session logs
visudo (8)           - edit the sudoers file
```

Ещё один полезный ресурс, на который можно обратиться за помощью, если у нас возникли проблемы с пониманием длинной команды: [https://explainshell.com/](https://explainshell.com/)