#htb_practiсe #linux 

## Настройка Брандмауэра с помощью Iptables

### 1. Запуск веб-сервера и блокировка трафика на порту TCP/8080

1. **Запустите веб-сервер** на порту 8080. Например, используя `python`:
   ```bash
   python -m http.server 8080
   ```

2. **Блокируйте входящий трафик** на порту 8080 с помощью iptables:
   ```bash
   sudo iptables -A INPUT -p tcp --dport 8080 -j DROP
   ```

### 2. Разрешение входящего трафика на порту TCP/8080

1. **Разрешите входящий трафик** на порту 8080:
   ```bash
   sudo iptables -A INPUT -p tcp --dport 8080 -j ACCEPT
   ```

### 3. Блокировка трафика с определенного IP-адреса

1. **Блокируйте трафик** с IP-адреса `192.168.1.100`:
   ```bash
   sudo iptables -A INPUT -s 192.168.1.100 -j DROP
   ```

### 4. Разрешение трафика с определенного IP-адреса

1. **Разрешите трафик** с IP-адреса `192.168.1.100`:
   ```bash
   sudo iptables -A INPUT -s 192.168.1.100 -j ACCEPT
   ```

### 5. Блокировка трафика на основе протокола

1. **Блокируйте входящий ICMP трафик**:
   ```bash
   sudo iptables -A INPUT -p icmp -j DROP
   ```

### 6. Разрешение трафика на основе протокола

1. **Разрешите входящий ICMP трафик**:
   ```bash
   sudo iptables -A INPUT -p icmp -j ACCEPT
   ```

### 7. Создание новой цепочки

1. **Создайте новую цепочку**:
   ```bash
   sudo iptables -N mychain
   ```

### 8. Перенаправление трафика в определенную цепочку

1. **Перенаправьте трафик** в новую цепочку:
   ```bash
   sudo iptables -A INPUT -j mychain
   ```

2. **Добавьте правило** в новую цепочку:
   ```bash
   sudo iptables -A mychain -p tcp --dport 8080 -j ACCEPT
   ```

### 9. Удаление конкретного правила

1. **Удалите правило** по номеру:
   ```bash
   sudo iptables -D INPUT 1
   ```

   или по критериям:
   ```bash
   sudo iptables -D INPUT -p tcp --dport 8080 -j DROP
   ```

### 10. Перечисление всех существующих правил

1. **Выведите все правила**:
   ```bash
   sudo iptables -n -L
   ```

Эти команды помогут вам настроить и управлять правилами брандмауэра с помощью iptables. Помните, что изменения правил iptables могут быть потеряны после перезагрузки системы, поэтому рекомендуется сохранять их с помощью команды `iptables-save` или включать сервис, который автоматически загружает правила при запуске системы.

Citations:
[1] https://serverspace.ru/support/help/nastroika-firewall-iptables-na-linux/
[2] https://selectel.ru/blog/setup-iptables-linux/
[3] https://drach.pro/blog/linux/item/37-iptables-for-web-server
[4] https://blog.xenot.ru/nastrojka-iptables-dlya-zashhity-servera-na-ubuntu-20-04-lts-v-2022-godu.fuck
[5] https://1cloud.ru/help/linux/nastrojka_linus-firewall_iptables
[6] https://timeweb.cloud/docs/unix-guides/iptables-setup
[7] https://ruvds.com/ru/helpcenter/configure-iptables/
[8] https://timeweb.com/ru/community/articles/nastroyka-iptables-dlya-chaynikov

---
Answer from Perplexity: https://www.perplexity.ai/search/pomogi-nastroit-selinux-1-usta-ijDoxqmbTj6uiZ0tN9I.lQ?utm_source=copy_output