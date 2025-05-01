#linux 

**NFS (Network File System)** - это протокол сетевого доступа к файловым системам, разработанный Sun Microsystems в 1984 году, который позволяет клиентам монтировать и работать с удалёнными файловыми системами так, как будто они находятся локально[1](https://ru.wikipedia.org/wiki/Network_File_System)[3](https://itglobal.com/ru-ru/company/glossary/nfs/).

## Основные особенности NFS

- Работает по модели клиент-сервер: сервер экспортирует (делится) каталогами, а клиент монтирует эти каталоги в свою файловую систему.
    
- Использует протокол удалённого вызова процедур (RPC) для обмена запросами и ответами между клиентом и сервером.
    
- Поддерживает различные версии, наиболее современная - NFSv4, которая улучшает безопасность (например, аутентификацию Kerberos) и производительность.
    
- Может работать поверх TCP или UDP.
    
- Обеспечивает прозрачный доступ к файлам, позволяя приложениям работать с удалёнными файлами без изменений.
    

## Конфигурация NFS

## На сервере

1. **Установка пакетов NFS** (например, `nfs-kernel-server` в Linux).
    
2. **Настройка экспорта каталогов** - в файле `/etc/exports` указываются директории, доступные для клиентов, и параметры доступа. Пример:
    
    text
    
    `/srv/nfs/shared 192.168.1.0/24(rw,sync,no_subtree_check)`
    
    Здесь каталог `/srv/nfs/shared` экспортируется для всех клиентов из подсети `192.168.1.0/24` с правами на чтение и запись.
    
3. **Запуск и включение служб NFS** (`nfs-server`, `rpcbind` и др.).
    
4. **Настройка прав доступа и, при необходимости, аутентификации** (например, с помощью Kerberos).
    

## На клиенте

1. **Установка клиентских пакетов NFS** (например, `nfs-common`).
    
2. **Монтирование удалённого каталога** командой:
    
    text
    
    `mount -t nfs server_ip:/srv/nfs/shared /mnt/nfs`
    
3. Для автоматического монтирования при загрузке можно добавить запись в `/etc/fstab`:
    
    text
    
    `server_ip:/srv/nfs/shared /mnt/nfs nfs defaults 0 0`
    

## Дополнительные компоненты и особенности

- **Менеджер блокировок (NLM)** обеспечивает управление блокировками для предотвращения конфликтов при одновременном доступе.
    
- **Монитор состояния (NSM)** отслеживает состояние клиентов и серверов.
    
- Поддержка кэширования на клиенте для повышения производительности.
    
- Возможность использования списков контроля доступа (ACL) и различных методов аутентификации.
    

Таким образом, NFS - это удобный и широко используемый протокол для совместного доступа к файлам в сетях Unix/Linux, обеспечивающий прозрачный и эффективный обмен данными между машинами[1](https://ru.wikipedia.org/wiki/Network_File_System)[3](https://itglobal.com/ru-ru/company/glossary/nfs/)[5](https://freehost.com.ua/faq/wiki/chto-takoe-nfs-ponjatie-preimuschestva-i-printsipi-raboti/)[6](https://serverict.com/encyclopedia/nfs/).

### Citations:

1. [https://ru.wikipedia.org/wiki/Network_File_System](https://ru.wikipedia.org/wiki/Network_File_System)
2. [https://www.ibm.com/docs/ru/aix/7.2?topic=management-network-file-system](https://www.ibm.com/docs/ru/aix/7.2?topic=management-network-file-system)
3. [https://itglobal.com/ru-ru/company/glossary/nfs/](https://itglobal.com/ru-ru/company/glossary/nfs/)
4. [https://www.inp.nsk.su/~bolkhov/teach/inpunix/setup_nfs.ru.html](https://www.inp.nsk.su/~bolkhov/teach/inpunix/setup_nfs.ru.html)
5. [https://freehost.com.ua/faq/wiki/chto-takoe-nfs-ponjatie-preimuschestva-i-printsipi-raboti/](https://freehost.com.ua/faq/wiki/chto-takoe-nfs-ponjatie-preimuschestva-i-printsipi-raboti/)
6. [https://serverict.com/encyclopedia/nfs/](https://serverict.com/encyclopedia/nfs/)
7. [https://procloud.ru/blog/technologies/chto-takoe-setevaya-faylovaya-sistema/](https://procloud.ru/blog/technologies/chto-takoe-setevaya-faylovaya-sistema/)
8. [https://wiki.astralinux.ru/pages/viewpage.action?pageId=27362314](https://wiki.astralinux.ru/pages/viewpage.action?pageId=27362314)

---

Answer from Perplexity: [https://www.perplexity.ai/search/rasskazhi-kitaiskuiu-teoremu-o-2FTak_.9T5OrqnQ9SjVB3Q?utm_source=copy_output](https://www.perplexity.ai/search/rasskazhi-kitaiskuiu-teoremu-o-2FTak_.9T5OrqnQ9SjVB3Q?utm_source=copy_output)