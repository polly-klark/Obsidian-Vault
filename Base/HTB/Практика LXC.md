#htb_practiсe #linux 

## Настройка LXC

### 1. Установка LXC и создание первого контейнера

1. **Установите LXC** на вашу систему. Для Ubuntu:
   ```bash
   sudo apt update
   sudo apt install lxc
   ```

2. **Создайте контейнер**:
   ```bash
   sudo lxc-create -n mycontainer -t ubuntu
   ```

3. **Запустите контейнер**:
   ```bash
   sudo lxc-start -n mycontainer
   ```

4. **Подключитесь к контейнеру**:
   ```bash
   sudo lxc-attach -n mycontainer
   ```

### 2. Настройка сетевых параметров

1. **Откройте файл конфигурации** контейнера:
   ```bash
   sudo nano /var/lib/lxc/mycontainer/config
   ```

2. **Настройте сетевой интерфейс**:
   ```text
   lxc.network.type = veth
   lxc.network.flags = up
   lxc.network.link = lxcbr0
   lxc.network.ipv4 = 10.0.3.100/24
   ```

3. **Перезапустите контейнер**:
   ```bash
   sudo lxc-stop -n mycontainer
   sudo lxc-start -n mycontainer
   ```

### 3. Создание пользовательского образа

1. **Создайте новый контейнер** с нужной конфигурацией:
   ```bash
   sudo lxc-create -n template -t ubuntu
   ```

2. **Настройте контейнер** как вам нужно (установите пакеты, настройте конфигурации).

3. **Остановите контейнер**:
   ```bash
   sudo lxc-stop -n template
   ```

4. **Создайте образ** из контейнера:
   ```bash
   sudo tar -czvf mytemplate.tar.gz /var/lib/lxc/template
   ```

5. **Используйте образ** для нового контейнера:
   ```bash
   sudo tar -xvf mytemplate.tar.gz -C /var/lib/lxc/mynewcontainer
   ```

6. **Настройте конфигурацию** нового контейнера:
   ```bash
   sudo nano /var/lib/lxc/mynewcontainer/config
   ```

### 4. Настройка ограничений ресурсов

1. **Остановите контейнер**:
   ```bash
   sudo lxc-stop -n mycontainer
   ```

2. **Откройте файл конфигурации**:
   ```bash
   sudo nano /var/lib/lxc/mycontainer/config
   ```

3. **Настройте ограничения ресурсов**:
   ```text
   lxc.cgroup.cpu.shares = 512
   lxc.cgroup.memory.limit_in_bytes = 512M
   lxc.cgroup.blkio.weight = 500
   ```

4. **Перезапустите контейнер**:
   ```bash
   sudo lxc-start -n mycontainer
   ```

### 5. Изучение команд LXC

Основные команды:
- `lxc-create`: Создание контейнера.
- `lxc-start`: Запуск контейнера.
- `lxc-stop`: Остановка контейнера.
- `lxc-attach`: Подключение к контейнеру.
- `lxc-destroy`: Удаление контейнера.

### 6. Создание контейнера с веб-сервером

1. **Создайте контейнер**:
   ```bash
   sudo lxc-create -n webserver -t ubuntu
   ```

2. **Установите веб-сервер** внутри контейнера:
   ```bash
   sudo lxc-attach -n webserver -- apt update && apt install nginx
   ```

3. **Настройте конфигурацию** веб-сервера.

### 7. Настройка SSH-доступа

1. **Установите OpenSSH** внутри контейнера:
   ```bash
   sudo lxc-attach -n mycontainer -- apt install openssh-server
   ```

2. **Настройте SSH** для удаленного доступа.

3. **Подключитесь по SSH**:
   ```bash
   ssh user@ip_address
   ```

### 8. Создание контейнера с сохранением данных

1. **Создайте контейнер** с типом `dir` вместо `overlayfs`:
   ```bash
   sudo lxc-create -n persistent -t dir
   ```

2. **Настройте контейнер** как вам нужно.

3. **Остановите и перезапустите** контейнер, чтобы изменения сохранились.

### 9. Тестирование программного обеспечения

1. **Создайте контейнер** для тестирования:
   ```bash
   sudo lxc-create -n testenv -t ubuntu
   ```

2. **Установите необходимое ПО** внутри контейнера:
   ```bash
   sudo lxc-attach -n testenv -- apt install software
   ```

3. **Тестируйте ПО** в изолированной среде.

Эти шаги помогут вам освоить основы работы с LXC и использовать его для различных задач.

Citations:
[1] https://selectel.ru/blog/upravlenie-kontejnerami-s-lxd-kratkoe-vvedenie/
[2] http://onreader.mdl.ru/MasteringProxmox.2ed/content/Ch06.html
[3] https://habr.com/ru/articles/496492/
[4] https://help.ubuntu.ru/wiki/%D1%80%D1%83%D0%BA%D0%BE%D0%B2%D0%BE%D0%B4%D1%81%D1%82%D0%B2%D0%BE_%D0%BF%D0%BE_ubuntu_server/%D0%B2%D0%B8%D1%80%D1%82%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F/lxc
[5] https://wiki.astralinux.ru/pages/viewpage.action?pageId=158608412
[6] https://eax.me/lxc/
[7] https://www.oslogic.ru/knowledge/560/lxc-shpargalka-po-komandam-utilitam/
[8] https://www.altlinux.org/LXC

---
Answer from Perplexity: https://www.perplexity.ai/search/pomogi-nastroit-selinux-1-usta-ijDoxqmbTj6uiZ0tN9I.lQ?utm_source=copy_output