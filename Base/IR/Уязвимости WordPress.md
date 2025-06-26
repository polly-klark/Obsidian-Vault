#perplexity #cve 

WordPress — популярная CMS, но она подвержена множеству уязвимостей, особенно в плагинах и темах. Вот основные сведения о типичных уязвимостях и угрозах для WordPress в 2025 году:

## Основные уязвимости WordPress

- **Уязвимости плагинов**  
    Большинство проблем с безопасностью (около 96%) связаны с плагинами. В 2024 году было выявлено около 8000 уязвимостей в экосистеме WordPress, большинство из которых — в плагинах. Некоторые плагины с миллионами установок содержат критические ошибки, позволяющие выполнять произвольный код, обходить аутентификацию и создавать учётные записи администраторов без разрешения[3](https://firstvds.ru/blog/novosti-iz-mira-bezopasnosti-uyazvimosti-mart-2025).
    
- **Обход аутентификации**  
    Например, плагин OttoKit (ранее SureTriggers), используемый более чем на 100 000 сайтов, имел уязвимость CVE-2025-3102, которая позволяла создавать администраторов без авторизации с помощью специально сформированного HTTP-запроса[1](https://firstvds.ru/blog/uyazvimosti-amd-whatsapp-wordpress-ssl).
    
- **Удалённое выполнение кода (RCE)**  
    Плагин Coming Soon & Maintenance имел критическую уязвимость CVE-2025-32118, позволяющую администратору загружать ZIP-архивы с вредоносным содержимым, что приводит к полной компрометации сайта и сервера[5](https://alekseycheremnykh.ru/post/uyazvimost-cve-2025-32118-v-plagine-coming-soon-maintenance-wordpress/).
    
- **Межсайтовый скриптинг (XSS)**  
    Около 47,7% уязвимостей — это XSS-атаки, позволяющие внедрять вредоносные скрипты на сайты[3](https://firstvds.ru/blog/novosti-iz-mira-bezopasnosti-uyazvimosti-mart-2025)[8](https://www.kaspersky.ru/blog/wordpress-security-issues/36652/).
    
- **Нарушения контроля доступа и CSRF**  
    Часто встречаются уязвимости, связанные с недостаточным контролем доступа (14,19%) и межсайтовой подделкой запросов (11,35%)[3](https://firstvds.ru/blog/novosti-iz-mira-bezopasnosti-uyazvimosti-mart-2025).
    
- **Уязвимости в плагинах безопасности**  
    Например, в плагине Really Simple Security была обнаружена критическая уязвимость CVE-2024-10924, которая позволяет злоумышленнику войти в систему с правами администратора без пароля[6](https://www.kaspersky.ru/blog/cve-2024-10924-wordpress-authentication-bypass/38619/).
    

## Другие проблемы безопасности WordPress

- **Слабые пароли и отсутствие двухфакторной аутентификации**  
    Перебор паролей (bruteforce) и использование утечек данных — частые причины взломов[8](https://www.kaspersky.ru/blog/wordpress-security-issues/36652/).
    
- **Уязвимости в темах и ядре WordPress**  
    Хотя ядро WordPress редко подвергается атакам (всего 7 уязвимостей в 2024 году), плагины и темы часто содержат ошибки, которые могут быть использованы для атак[3](https://firstvds.ru/blog/novosti-iz-mira-bezopasnosti-uyazvimosti-mart-2025)[8](https://www.kaspersky.ru/blog/wordpress-security-issues/36652/).
    
- **Атаки с использованием XML-RPC**  
    XML-RPC может использоваться для ускоренного подбора паролей и DDoS-атак через пингбэки[8](https://www.kaspersky.ru/blog/wordpress-security-issues/36652/).
    
- **Массовые взломы с созданием админов и установкой вредоносных плагинов**  
    В 2025 году была выявлена кампания, в ходе которой злоумышленники взломали более 5000 сайтов, создавая новые учётные записи администраторов и устанавливая вредоносные плагины для кражи данных[7](https://xakep.ru/2025/01/15/wordpress-xyz-attacks/).
    

## Рекомендации по защите

- Регулярно обновляйте WordPress, плагины и темы до последних версий[1](https://firstvds.ru/blog/uyazvimosti-amd-whatsapp-wordpress-ssl)[5](https://alekseycheremnykh.ru/post/uyazvimost-cve-2025-32118-v-plagine-coming-soon-maintenance-wordpress/).
    
- Использовать двухфакторную аутентификацию и сложные пароли[5](https://alekseycheremnykh.ru/post/uyazvimost-cve-2025-32118-v-plagine-coming-soon-maintenance-wordpress/)[8](https://www.kaspersky.ru/blog/wordpress-security-issues/36652/).
    
- Отключить или ограничить XML-RPC, если он не нужен[8](https://www.kaspersky.ru/blog/wordpress-security-issues/36652/).
    
- Устанавливать веб-фаерволы (WAF) для блокировки подозрительного трафика и XSS-атак[4](https://www.securitylab.ru/blog/personal/SimlpeHacker/355358.php)[9](https://hostpro.ua/blog/ten-wordpress-vulnerabilities/).
    
- Проводить аудит пользователей и удалять неиспользуемые учётные записи с высокими правами[5](https://alekseycheremnykh.ru/post/uyazvimost-cve-2025-32118-v-plagine-coming-soon-maintenance-wordpress/)[7](https://xakep.ru/2025/01/15/wordpress-xyz-attacks/).
    
- Мониторинг изменений в файловой системе и подозрительной активности[5](https://alekseycheremnykh.ru/post/uyazvimost-cve-2025-32118-v-plagine-coming-soon-maintenance-wordpress/).
    

Таким образом, основная угроза для WordPress — уязвимости в плагинах, которые часто позволяют обходить аутентификацию, выполнять произвольный код и создавать администраторов без разрешения. Важно своевременно обновлять компоненты и использовать дополнительные меры защиты для минимизации рисков[1](https://firstvds.ru/blog/uyazvimosti-amd-whatsapp-wordpress-ssl)[3](https://firstvds.ru/blog/novosti-iz-mira-bezopasnosti-uyazvimosti-mart-2025)[5](https://alekseycheremnykh.ru/post/uyazvimost-cve-2025-32118-v-plagine-coming-soon-maintenance-wordpress/)[8](https://www.kaspersky.ru/blog/wordpress-security-issues/36652/).

### Citations:

1. [https://firstvds.ru/blog/uyazvimosti-amd-whatsapp-wordpress-ssl](https://firstvds.ru/blog/uyazvimosti-amd-whatsapp-wordpress-ssl)
2. [https://bdu.fstec.ru/vul/2025-04018](https://bdu.fstec.ru/vul/2025-04018)
3. [https://firstvds.ru/blog/novosti-iz-mira-bezopasnosti-uyazvimosti-mart-2025](https://firstvds.ru/blog/novosti-iz-mira-bezopasnosti-uyazvimosti-mart-2025)
4. [https://www.securitylab.ru/blog/personal/SimlpeHacker/355358.php](https://www.securitylab.ru/blog/personal/SimlpeHacker/355358.php)
5. [https://alekseycheremnykh.ru/post/uyazvimost-cve-2025-32118-v-plagine-coming-soon-maintenance-wordpress/](https://alekseycheremnykh.ru/post/uyazvimost-cve-2025-32118-v-plagine-coming-soon-maintenance-wordpress/)
6. [https://www.kaspersky.ru/blog/cve-2024-10924-wordpress-authentication-bypass/38619/](https://www.kaspersky.ru/blog/cve-2024-10924-wordpress-authentication-bypass/38619/)
7. [https://xakep.ru/2025/01/15/wordpress-xyz-attacks/](https://xakep.ru/2025/01/15/wordpress-xyz-attacks/)
8. [https://www.kaspersky.ru/blog/wordpress-security-issues/36652/](https://www.kaspersky.ru/blog/wordpress-security-issues/36652/)
9. [https://hostpro.ua/blog/ten-wordpress-vulnerabilities/](https://hostpro.ua/blog/ten-wordpress-vulnerabilities/)

---

Answer from Perplexity: [https://www.perplexity.ai/search/privet-rasskazhi-pro-uiazvimos-0erm_KXNTs6mZRIVylpY3g?utm_source=copy_output](https://www.perplexity.ai/search/privet-rasskazhi-pro-uiazvimos-0erm_KXNTs6mZRIVylpY3g?utm_source=copy_output)