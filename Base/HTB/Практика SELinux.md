#htb_practiсe #linux

## Настройка SELinux на Виртуальной Машины

### 1. Установка SELinux

Для установки SELinux на виртуальную машину под управлением Ubuntu, выполните следующие шаги:

1. **Обновите список пакетов**:
   ```bash
   sudo apt update
   ```

2. **Установите необходимые пакеты SELinux**:
   ```bash
   sudo apt install policycoreutils selinux-utils selinux-basics
   ```

3. **Активируйте SELinux**:
   ```bash
   sudo selinux-activate
   ```

4. **Перезагрузите систему** для применения изменений:
   ```bash
   sudo reboot
   ```

После перезагрузки SELinux будет включен. Проверьте его состояние командой:
```bash
sestatus
```

### 2. Запрет доступа к файлу

Чтобы запретить пользователю доступ к определенному файлу, выполните следующие шаги:

1. **Определите контекст безопасности файла**:
   ```bash
   ls -Z /path/to/file
   ```

2. **Измените контекст безопасности файла** на тот, который не доступен пользователю:
   ```bash
   sudo chcon -t system_u:object_r:admin_home_t:s0 /path/to/file
   ```

3. **Настройте политику SELinux**, чтобы запретить доступ к файлам с таким контекстом:
   - Используйте утилиту `audit2allow`, чтобы создать правила на основе логов SELinux.

### 3. Доступ к сетевой службе для одного пользователя

Чтобы разрешить доступ к сетевой службе только одному пользователю, выполните следующие шаги:

1. **Создайте новый контекст безопасности** для пользователя:
   ```bash
   sudo semanage user -a -R "staff_r" newuser_u
   ```

2. **Настройте политику SELinux** для сетевой службы:
   - Используйте `setsebool`, чтобы разрешить сетевую службу для определенного контекста:
     ```bash
     setsebool -P httpd_can_network_connect 1
     ```

3. **Примените контекст безопасности** к пользователю:
   ```bash
   sudo usermod -Z staff_u newuser
   ```

### 4. Запрет доступа к сетевой службе

Чтобы запретить доступ к сетевой службе определенному пользователю или группе, выполните следующие шаги:

1. **Создайте новый контекст безопасности** для пользователя или группы:
   ```bash
   sudo semanage user -a -R "unconfined_r" restricted_u
   ```

2. **Настройте политику SELinux** для запрета доступа:
   - Используйте `setsebool`, чтобы запретить сетевую службу для определенного контекста:
     ```bash
     setsebool -P httpd_can_network_connect 0
     ```

3. **Примените контекст безопасности** к пользователю или группе:
   ```bash
   sudo usermod -Z unconfined_u restricteduser
   ```

Эти шаги позволят вам настроить SELinux для управления доступом к файлам и сетевым службам на вашей виртуальной машине.

Citations:
[1] https://ruvds.com/ru/helpcenter/vvedenie-v-selinux-pod-ubuntu-20-04/
[2] https://losst.pro/nastrojka-selinux
[3] https://habr.com/ru/companies/otus/articles/484896/
[4] https://support.kaspersky.com/kes4linux/11.2.0/ru-RU/197917.htm
[5] https://redos.red-soft.ru/base/redos-7_3/7_3-security/7_3-selinux/7_3-selinux/
[6] https://support.kaspersky.com/help/KES4Linux/12.2.0/ru-RU/kesl.pdf
[7] https://www.linux.org.ru/forum/desktop/5044357
[8] https://ftp.zhirov.kz/books/IT/Unix/%D0%90%D0%B4%D0%BC%D0%B8%D0%BD%D0%B8%D1%81%D1%82%D1%80%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%20%D1%81%D0%B8%D1%81%D1%82%D0%B5%D0%BC%D1%8B%20%D0%B7%D0%B0%D1%89%D0%B8%D1%82%D1%8B%20SELinux%20(%D0%A1%D0%B2%D0%B5%D0%BD%20%D0%92%D0%B5%D1%80%D0%BC%D0%B5%D0%B9%D0%BB%D0%B5%D0%BD).pdf
[9] https://learn.microsoft.com/ru-ru/troubleshoot/azure/virtual-machines/linux/linux-selinux-troubleshooting
[10] https://yamadharma.github.io/ru/post/2015/05/15/selinux-apps/

---
Answer from Perplexity: https://www.perplexity.ai/search/pomogi-nastroit-selinux-1-usta-ijDoxqmbTj6uiZ0tN9I.lQ?utm_source=copy_output