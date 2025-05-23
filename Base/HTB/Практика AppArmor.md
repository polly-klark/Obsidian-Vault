#linux #htb_practiсe 

## Настройка AppArmor

### 1. Запрет доступа к файлу

Чтобы запретить пользователю доступ к определенному файлу с помощью AppArmor, необходимо создать профиль для программы, которая потенциально может получить доступ к этому файлу. Если профиль уже существует, его нужно изменить.

1. **Определите программу**, которая может получить доступ к файлу. Например, если это текстовый редактор, то это может быть `nano` или `vim`.

2. **Создайте или измените профиль** для этой программы:
   - Профили AppArmor находятся в `/etc/apparmor.d/`. Например, профиль для `nano` будет называться `usr.bin.nano`.
   - Если профиль не существует, создайте его с помощью команды:
     ```bash
     sudo aa-genprof /usr/bin/nano
     ```
   - Запустите программу и выполните действия, чтобы AppArmor сгенерировал правила.

3. **Измените профиль**, чтобы запретить доступ к файлу:
   - Откройте профиль в редакторе:
     ```bash
     sudo nano /etc/apparmor.d/usr.bin.nano
     ```
   - Добавьте строку, запрещающую доступ к файлу. Например, чтобы запретить доступ к `/path/to/file`, добавьте:
     ```apparmor
     deny /path/to/file r,
     deny /path/to/file w,
     ```
   - Сохраните изменения и перезагрузите AppArmor:
     ```bash
     sudo service apparmor reload
     ```

### 2. Доступ к сетевой службе для одного пользователя

Чтобы разрешить доступ к сетевой службе только одному пользователю, необходимо создать профиль для этой службы и настроить его так, чтобы он был доступен только этому пользователю. Однако AppArmor не работает напрямую с пользователями, поэтому это потребует дополнительных настроек.

1. **Создайте профиль** для сетевой службы:
   - Например, если это служба `httpd`, создайте профиль для `/usr/sbin/httpd`.

2. **Настройте профиль** для доступа к сетевой службе:
   - Разрешите сетевые возможности:
     ```apparmor
     network inet tcp,
     network inet udp,
     ```
   - Чтобы ограничить доступ к этому профилю только для одного пользователя, необходимо использовать внешние механизмы, такие как `sudo` или ACL (Access Control Lists), поскольку AppArmor не различает пользователей напрямую.

3. **Используйте sudo или ACL** для ограничения доступа:
   - Создайте группу для пользователей, которым разрешен доступ к службе.
   - Настройте ACL для исполняемого файла службы, чтобы только эта группа могла запускать службу.

### 3. Запрет доступа к сетевой службе

Чтобы запретить доступ к сетевой службе конкретному пользователю или группе, можно использовать комбинацию AppArmor и внешних механизмов контроля доступа.

1. **Создайте профиль** для сетевой службы:
   - Например, для `/usr/sbin/httpd`.

2. **Настройте профиль**, чтобы запретить сетевые возможности:
   - Добавьте в профиль строки, запрещающие сетевой доступ:
     ```apparmor
     deny network inet tcp,
     deny network inet udp,
     ```
   - Перезагрузите AppArmor.

3. **Используйте sudo или ACL** для дополнительных ограничений:
   - Настройте ACL или sudo, чтобы запретить пользователю или группе запускать службу напрямую.

Эти шаги позволят вам настроить AppArmor для управления доступом к файлам и сетевым службам на вашей системе. Однако для ограничения доступа на уровне пользователей или групп вам, возможно, придется использовать дополнительные инструменты, такие как sudo или ACL.

Citations:
[1] https://habr.com/ru/companies/ruvds/articles/532988/
[2] https://vasilisc.com/rules_apparmor
[3] https://losst.pro/nastrojka-apparmor-v-ubuntu-16-04
[4] https://vasilisc.com/deny-app-net
[5] https://help.ubuntu.ru/wiki/%D1%80%D1%83%D0%BA%D0%BE%D0%B2%D0%BE%D0%B4%D1%81%D1%82%D0%B2%D0%BE_%D0%BF%D0%BE_ubuntu_server/%D0%B1%D0%B5%D0%B7%D0%BE%D0%BF%D0%B0%D1%81%D0%BD%D0%BE%D1%81%D1%82%D1%8C/apparmor
[6] https://forum.ubuntu.ru/index.php?topic=273279.0
[7] https://support.kaspersky.com/help/KES4LinuxElbrus/10.1.2/ru-RU/179508.htm
[8] https://debian-soft.ru/applications/854/kak-ispolzovat-apparmor-v-debian/

---
Answer from Perplexity: https://www.perplexity.ai/search/pomogi-nastroit-selinux-1-usta-ijDoxqmbTj6uiZ0tN9I.lQ?utm_source=copy_output