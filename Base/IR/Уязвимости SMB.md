#smb #cve #perplexity 

Основные уязвимости протокола SMB (Server Message Block), используемого для совместного доступа к файлам и ресурсам в сетях Windows и Linux (через Samba), включают следующие:

## Критические уязвимости SMB и Samba

- **CVE-2020-0796 (SMBGhost)**  
    Критическая уязвимость в SMBv3 (версия 3.1.1) в Windows 10 (версии 1903, 1909) и Windows Server, позволяющая удалённо выполнять произвольный код на SMB-сервере или клиенте без аутентификации. Для атаки на сервер достаточно отправить специально сформированный пакет, для атаки на клиента нужно создать вредоносный SMB-сервер и убедить пользователя подключиться к нему. Уязвимость получила высокий рейтинг критичности и может быть использована для создания червей, аналогичных WannaCry. Microsoft выпустила исправления и рекомендует срочно их применить. Временные меры — отключение сжатия SMB и блокировка порта TCP 445 на внешних интерфейсах[2](https://www.kaspersky.ru/blog/smb-311-vulnerability/27594/)[6](https://habr.com/ru/news/492014/).
    
- **CVE-2020-1206 (SMBleed)**  
    Уязвимость, связанная с обработкой сжатых сообщений в SMBv3, позволяет удалённо считывать данные из памяти ядра, что может привести к раскрытию конфиденциальной информации. В сочетании с CVE-2020-0796 может использоваться для удалённого выполнения кода. Затрагивает Windows 10 версий 1903 и 1909. Для эксплуатации на стороне клиента требуется подключение к вредоносному SMB-серверу. Microsoft выпустила исправления[5](https://www.securitylab.ru/news/509108.php).
    
- **CVE-2017-7494 (Samba)**  
    Уязвимость в Samba (реализации SMB для Linux/Unix), присутствующая во всех версиях начиная с 3.5.0, позволяет удалённому злоумышленнику загрузить и выполнить произвольный код на сервере. Уязвимость широко распространена и требует срочного обновления Samba[1](http://safe-surf.ru/specialists/news/546911/).
    
- **CVE-2016-2114 (Samba)**  
    Отсутствие обязательной проверки подписи SMB (сервером), что позволяет провести MITM-атаку и изменить передаваемые данные. Опасно для серверов, выступающих в роли контроллеров домена. Рекомендуется явно включать `server signing = mandatory` и использовать протокол SMB2 и выше[4](https://access.redhat.com/ru/articles/2260381).
    
- **Уязвимости SMBv1**  
    Протокол SMBv1 содержит множество известных уязвимостей, используемых в атаках с помощью эксплойтов EternalBlue, WannaCry, NotPetya и других. Microsoft настоятельно рекомендует отключать SMBv1 на серверах и клиентах из-за отсутствия современных механизмов безопасности, таких как шифрование и защита от MITM[7](https://habr.com/ru/news/488358/).
    

## Рекомендации по защите

- Обязательно устанавливать все обновления безопасности от Microsoft и разработчиков Samba.
    
- Отключать SMBv1 на всех устройствах.
    
- Включить обязательную проверку подписи SMB (`server signing = mandatory`) и использовать SMBv2 или SMBv3.
    
- Отключить сжатие SMB для защиты от CVE-2020-0796.
    
- Блокировать входящие подключения на TCP-порт 445 из внешних сетей.
    
- Использовать современные средства защиты, включая антивирусы с функцией предотвращения эксплойтов.
    

Таким образом, несмотря на широкое использование, протокол SMB остаётся уязвимым для ряда серьёзных атак, которые могут привести к удалённому выполнению кода, раскрытию данных и распространению вредоносного ПО по сети. Своевременное обновление и правильная настройка протокола значительно снижают риски[1](http://safe-surf.ru/specialists/news/546911/)[2](https://www.kaspersky.ru/blog/smb-311-vulnerability/27594/)[4](https://access.redhat.com/ru/articles/2260381)[5](https://www.securitylab.ru/news/509108.php)[6](https://habr.com/ru/news/492014/)[7](https://habr.com/ru/news/488358/).

### Citations:

1. [http://safe-surf.ru/specialists/news/546911/](http://safe-surf.ru/specialists/news/546911/)
2. [https://www.kaspersky.ru/blog/smb-311-vulnerability/27594/](https://www.kaspersky.ru/blog/smb-311-vulnerability/27594/)
3. [https://www.itsec.ru/news/novoye-pokoleniye-vimogateley-wanttocry-cherez-nezashishionniy-smb](https://www.itsec.ru/news/novoye-pokoleniye-vimogateley-wanttocry-cherez-nezashishionniy-smb)
4. [https://access.redhat.com/ru/articles/2260381](https://access.redhat.com/ru/articles/2260381)
5. [https://www.securitylab.ru/news/509108.php](https://www.securitylab.ru/news/509108.php)
6. [https://habr.com/ru/news/492014/](https://habr.com/ru/news/492014/)
7. [https://habr.com/ru/news/488358/](https://habr.com/ru/news/488358/)
8. [https://techcommunity.microsoft.com/blog/microsoft-security-baselines/windows-server-2025-security-baseline/4358733](https://techcommunity.microsoft.com/blog/microsoft-security-baselines/windows-server-2025-security-baseline/4358733)

---

Answer from Perplexity: [https://www.perplexity.ai/search/privet-rasskazhi-pro-uiazvimos-9CZVo3VkR.igehe9Ap6CdA?utm_source=copy_output](https://www.perplexity.ai/search/privet-rasskazhi-pro-uiazvimos-9CZVo3VkR.igehe9Ap6CdA?utm_source=copy_output)