#linux #htb_practiсe 

## Настройка TCP Wrappers

TCP Wrappers — это инструмент для управления доступом к сетевым службам на основе хоста. Он позволяет администраторам настраивать точные политики контроля доступа, указывая конкретные IP-адреса, доменные имена или сетевые интерфейсы в файлах `/etc/hosts.allow` и `/etc/hosts.deny`.

### 1. Разрешение доступа к сетевой службе с определённого IP-адреса

Чтобы разрешить доступ к определённой сетевой службе с определённого IP-адреса, выполните следующие шаги:

1. **Откройте файл `/etc/hosts.allow`** в редакторе:
   ```bash
   sudo nano /etc/hosts.allow
   ```

2. **Добавьте строку**, разрешающую доступ к службе с указанного IP-адреса:
   ```text
   служба : IP-адрес : allow
   ```
   Например, чтобы разрешить доступ к службе `ssh` с IP-адреса `192.168.1.100`, добавьте:
   ```text
   sshd : 192.168.1.100 : allow
   ```

3. **Сохраните изменения** и перезагрузите службу `inetd` или `xinetd`, если она используется:
   ```bash
   sudo service inetd restart
   ```

### 2. Запрет доступа к сетевой службе с определённого IP-адреса

Чтобы запретить доступ к определённой сетевой службе с определённого IP-адреса, выполните следующие шаги:

1. **Откройте файл `/etc/hosts.deny`** в редакторе:
   ```bash
   sudo nano /etc/hosts.deny
   ```

2. **Добавьте строку**, запрещающую доступ к службе с указанного IP-адреса:
   ```text
   служба : IP-адрес : deny
   ```
   Например, чтобы запретить доступ к службе `ssh` с IP-адреса `192.168.1.100`, добавьте:
   ```text
   sshd : 192.168.1.100 : deny
   ```

3. **Сохраните изменения** и перезагрузите службу `inetd` или `xinetd`, если она используется:
   ```bash
   sudo service inetd restart
   ```

### 3. Обеспечение доступа к сетевой службе с определённого диапазона IP-адресов

Чтобы обеспечить доступ к определённой сетевой службе с определённого диапазона IP-адресов, выполните следующие шаги:

1. **Откройте файл `/etc/hosts.allow`** в редакторе:
   ```bash
   sudo nano /etc/hosts.allow
   ```

2. **Добавьте строку**, разрешающую доступ к службе с указанного диапазона IP-адресов:
   ```text
   служба : диапазон_IP : allow
   ```
   Например, чтобы разрешить доступ к службе `ssh` с диапазона `192.168.1.0/24`, добавьте:
   ```text
   sshd : 192.168.1. : allow
   ```

3. **Сохраните изменения** и перезагрузите службу `inetd` или `xinetd`, если она используется:
   ```bash
   sudo service inetd restart
   ```

Эти шаги позволят вам настроить TCP Wrappers для управления доступом к сетевым службам на основе IP-адресов.

Citations:
[1] https://www.vpnunlimited.com/ru/help/cybersecurity/tcp-wrapper
[2] https://stfw.ru/page.php?id=10254
[3] https://parallel.uran.ru/node/385
[4] https://docs-archive.freebsd.org/doc/6.2-RELEASE/usr/share/doc/ru/books/handbook/tcpwrappers.html
[5] http://www.rhd.ru/docs/manuals/enterprise/RHEL-AS-2.1-Manual/custom-guide/tcp-wrappers.html
[6] https://unixforum.org/viewtopic.php?t=29834
[7] https://forum.lissyara.su/freebsd-unix-dlya-nachinayuschih-f53/ispol-zovanie-fail2ban-cherez-tcp-wrappers-t40959.html
[8] https://www.ntwk.ru/docs/freebsd/handbook/tcpwrappers.html

---
Answer from Perplexity: https://www.perplexity.ai/search/pomogi-nastroit-selinux-1-usta-ijDoxqmbTj6uiZ0tN9I.lQ?utm_source=copy_output