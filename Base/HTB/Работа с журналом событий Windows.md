#htb #windows #powershell 

С точки зрения SOC-аналитика или ИТ-администратора, мониторинг, сбор и категоризация событий, происходящих на всех компьютерах в сети, являются бесценным источником информации для специалистов по защите, которые активно анализируют сеть и защищают ее от подозрительных действий. С другой стороны, злоумышленники могут рассматривать это как возможность получить представление о целевой среде, нарушить поток информации и замести следы. Как мы увидим в следующих модулях, иногда в журналах событий целевой системы, которую мы взламываем во время тестирования на проникновение, можно найти ценную информацию, например учетные данные. В других случаях просмотр журналов событий может помочь нам понять, на каком уровне настроено ведение журналов в среде (используются ли только настройки по умолчанию или целевая организация настроила более детальное ведение журналов?). В этом разделе мы обсудим следующее:

- Что такое журнал событий Windows?
- Какую информацию он регистрирует и где хранит эти данные?
- Взаимодействие с журналом событий с помощью утилиты командной строки `wevtutil`
- Взаимодействие с журналом событий с помощью командлетов PowerShell

---

## Что такое журнал событий Windows?

Четкое понимание принципов ведения журнала событий имеет решающее значение для успешной работы в сфере информационной безопасности. Чтобы начать наше путешествие в мир глубокого понимания журнала событий Windows, нам нужно определить несколько ключевых понятий. Эти понятия станут основой, на которой будет строиться все остальное. Первое, что нужно объяснить, — это `event` определение. Проще говоря, `event` — это любое действие или событие, которое может быть идентифицировано и классифицировано аппаратным или программным обеспечением системы. `Events` может быть сгенерирован или запущен различными способами, в том числе следующими:

- Пользовательские события
    - Движение мыши, набор текста на клавиатуре, использование других периферийных устройств и т. д.
- События, генерируемые приложением
    - Обновления приложений, сбои, использование/расход памяти и т. д.
- Системные события
    - Время безотказной работы системы, обновления системы, загрузка/выгрузка драйверов, вход пользователя в систему и т. д.

Как система Windows отслеживает и классифицирует все эти события, происходящие в разное время и из разных источников? Здесь в игру вступает наша вторая ключевая концепция, известная как `event logging`

[Регистрация событий](https://learn.microsoft.com/en-us/windows/win32/eventlog/event-logging) в соответствии с определением Microsoft:

"`...provides a standard, centralized way for applications (and the operating system) to record important software and hardware events.`"

Это определение довольно точно отражает суть вопроса. Однако давайте попробуем разобраться в нём подробнее. Как мы уже говорили, в системе одновременно происходит множество событий. У каждого события есть свой источник, который предоставляет информацию и подробности о событии в своём формате. Так как же система обрабатывает всю эту информацию?

Windows пытается решить эту проблему, предоставляя стандартизированный подход к записи, хранению и управлению событиями и информацией о событиях с помощью службы, известной как `Windows Event Log`. Как следует из названия, `Event Log` управляет событиями и журналами событий, но помимо этой функции она также предоставляет специальный API, который позволяет приложениям вести собственные журналы и управлять ими. В модуле [Основы Windows](https://academy.hackthebox.com/module/details/49) мы более подробно рассмотрели `services` в журналах в разделе [Службы и процессы Windows](https://academy.hackthebox.com/module/49/section/457), однако важно понимать, что `Event Log` — это обязательная служба Windows, которая запускается при инициализации системы и работает в контексте другого исполняемого файла, а не сама по себе.

Прежде чем мы приступим к запросу журнала событий из cmd.exe и PowerShell, нам нужно разобраться в возможных типах событий, элементах журнала и других составляющих.

---

## Категории и типы журналов событий

К основным четырём категориям журналов относятся журналы приложений, безопасности, настройки и системы. Существует также категория `forwarded events`.

|Категория журнала|Описание журнала|
|---|---|
|Системный журнал|Системный журнал содержит события, связанные с системой Windows и её компонентами. Одним из событий системного уровня может быть сбой службы при запуске.|
|Журнал безопасности|Не требуют пояснений; к ним относятся события, связанные с безопасностью, такие как неудачные и успешные попытки входа в систему, а также создание/удаление файлов. Их можно использовать для обнаружения различных типов атак, которые мы рассмотрим в следующих модулях.|
|Журнал приложений|Здесь хранятся события, связанные с любым программным обеспечением/приложением, установленным в системе. Например, если у Slack возникнут проблемы с запуском, это будет зафиксировано в этом журнале.|
|Журнал настройки|В этом журнале фиксируются все события, возникающие при установке операционной системы Windows. В доменной среде события, связанные с Active Directory, будут записываться в этот журнал на узлах контроллера домена.|
|Перенаправленные события|Журналы, которые пересылаются с других хостов в той же сети.|

---

## Типы событий

В системах Windows можно регистрировать пять типов событий:

|Тип события|Описание события|
|---|---|
|Ошибка|Указывает на серьёзную проблему, например на то, что служба не загружается при запуске.|
|Предупреждение|Менее значимое событие, но оно может указывать на возможную проблему в будущем. Один из примеров — нехватка места на диске. Будет зарегистрировано событие «Предупреждение», указывающее на то, что в будущем может возникнуть проблема. Событие «Предупреждение» обычно регистрируется, когда приложение может восстановиться после события без потери функциональности или данных.|
|Информация|Регистрируется при успешной работе приложения, драйвера или службы, например при успешной загрузке сетевого драйвера. Как правило, не каждое настольное приложение регистрирует событие при каждом запуске, так как это может привести к значительному увеличению «шума» в журналах.|
|Аудит Успеха|Записывается при успешной попытке доступа к системе безопасности, например при входе пользователя в систему.|
|Аудит сбоев|Регистрируется при неудачной попытке доступа к системе безопасности, например, когда пользователь пытается войти в систему, но вводит неправильный пароль. Множество неудачных попыток аудита могут указывать на атаку, например на подбор пароля.|

---

## Уровни серьезности событий

С каждым журналом может быть связан один из пяти уровней серьёзности, обозначаемых числом:

| Уровень Серьезности | Уровень # | Описание                                                                                                                                                                                                                               |
| ------------------- | --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Verbose             | 5         | Сообщения о ходе выполнения или успешном завершении.                                                                                                                                                                                   |
| Information         | 4         | Событие, которое произошло в системе, но не вызвало никаких проблем.                                                                                                                                                                   |
| Warning             | 3         | Потенциальная проблема, которую должен изучить системный администратор.                                                                                                                                                                |
| Error               | 2         | Проблема, связанная с системой или сервисом, не требующая немедленного решения.                                                                                                                                                        |
| Critical            | 1         | Это указывает на серьёзную проблему, связанную с приложением или системой, которая требует срочного вмешательства системного администратора. Если проблему не решить, это может привести к нестабильной работе системы или приложения. |

---

## Элементы журнала событий Windows

Журнал событий Windows содержит информацию об аппаратных и программных событиях в системе Windows. Все журналы событий хранятся в стандартном формате и включают следующие элементы:

- `Log name`Как уже говорилось выше, это имя журнала событий, в который будут записываться события. По умолчанию события регистрируются для `system`, `security`, и `applications`.
- `Event date/time`: Дата и время произошедшего события
- `Task Category`Тип журнала регистрации событий
- `Event ID`Уникальный идентификатор, позволяющий системным администраторам идентифицировать конкретное зарегистрированное событие
- `Source`: откуда поступил журнал, обычно это название программы или приложения
- `Level`: Уровень серьёзности события. Это может быть информация, ошибка, подробное описание, предупреждение, критическая ситуация
- `User`: Имя пользователя, вошедшего в систему на хосте в момент возникновения события
- `Computer`: Имя компьютера, на котором зарегистрировано событие

Существует множество идентификаторов событий, которые организация может отслеживать для выявления различных проблем. В среде Active Directory [этот список](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/appendix-l--events-to-monitor) включает ключевые события, которые рекомендуется отслеживать для выявления признаков компрометации. [Эту](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/) базу данных идентификаторов событий с возможностью поиска стоит изучить, чтобы понять, насколько подробными могут быть журналы в системе Windows.

---

## Технические сведения о Журнале событий Windows

Журнал событий Windows обрабатывается службами `EventLog`. В системе Windows служба отображается как `Windows Event Log`, и она работает внутри процесса узла службы [svchost.exe](https://en.wikipedia.org/wiki/Svchost.exe). По умолчанию она запускается автоматически при загрузке системы. Службу EventLog сложно остановить, так как она зависит от нескольких служб. Если ее остановить, это, скорее всего, приведет к значительной нестабильности системы. По умолчанию журналы событий Windows хранятся в `C:\Windows\System32\winevt\logs` с расширением файла `.evtx`.

  Работа с журналом событий Windows

```powershell-session
PS C:\htb> ls C:\Windows\System32\winevt\logs

    Directory: C:\Windows\System32\winevt\logs


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/16/2022   2:19 PM        7409664 Application.evtx
-a----         6/14/2022   8:20 PM          69632 HardwareEvents.evtx
-a----         6/14/2022   8:20 PM          69632 Internet Explorer.evtx
-a----         6/14/2022   8:20 PM          69632 Key Management Service.evtx        
-a----         8/23/2022   7:01 PM          69632 Microsoft-Client-License-Flexible-P
                                                  latform%4Admin.evtx
-a----        11/16/2022   2:19 PM        1052672 Microsoft-Client-Licensing-Platform
                                                  %4Admin.evtx


<SNIP>
```

Мы можем взаимодействовать с журналом событий Windows с помощью графического интерфейса [Windows Event Viewer](https://en.wikipedia.org/wiki/Event_Viewer), утилиты командной строки [wevtutil](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/wevtutil) или командлета [Get-WinEvent](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.diagnostics/get-winevent?view=powershell-7.3) в PowerShell. И `wevtutil` и `Get-WinEvent` можно использовать для запроса журналов событий как в локальных, так и в удалённых системах Windows с помощью cmd.exe или PowerShell.

---

## Взаимодействие с журналом событий Windows — wevtutil

Утилиту командной строки [wevtutil](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/wevtutil) можно использовать для получения информации о журналах событий. С её помощью можно экспортировать, архивировать и очищать журналы, а также выполнять другие команды.

#### Wevtutil без параметров

  Работа с журналом событий Windows

```cmd-session
C:\htb> wevtutil /?

Windows Events Command Line Utility.

Enables you to retrieve information about event logs and publishers, install
and uninstall event manifests, run queries, and export, archive, and clear logs.

Usage:

You can use either the short (for example, ep /uni) or long (for example,
enum-publishers /unicode) version of the command and option names. Commands,
options and option values are not case-sensitive.

Variables are noted in all upper-case.

wevtutil COMMAND [ARGUMENT [ARGUMENT] ...] [/OPTION:VALUE [/OPTION:VALUE] ...]

Commands:

el | enum-logs          List log names.
gl | get-log            Get log configuration information.
sl | set-log            Modify configuration of a log.
ep | enum-publishers    List event publishers.
gp | get-publisher      Get publisher configuration information.
im | install-manifest   Install event publishers and logs from manifest.
um | uninstall-manifest Uninstall event publishers and logs from manifest.
qe | query-events       Query events from a log or log file.
gli | get-log-info      Get log status information.
epl | export-log        Export a log.
al | archive-log        Archive an exported log.
cl | clear-log          Clear a log.

<SNIP>
```

Мы можем использовать параметр `el` для перечисления названий всех журналов, имеющихся в системе Windows.

#### Перечисление источников журнала

  Работа с журналом событий Windows

```cmd-session
C:\htb> wevtutil el

AMSI/Debug
AirSpaceChannel
Analytic
Application
DirectShowFilterGraph
DirectShowPluginControl
Els_Hyphenation/Analytic
EndpointMapper
FirstUXPerf-Analytic
ForwardedEvents
General Logging
HardwareEvents

<SNIP>
```

С помощью параметра `gl` мы можем отобразить информацию о конфигурации для конкретного журнала, в частности о том, включен ли журнал, каков его максимальный размер, какие у него разрешения и где в системе он хранится.

#### Сбор информации из Журнала

  Работа с журналом событий Windows

```cmd-session
C:\htb> wevtutil gl "Windows PowerShell"

name: Windows PowerShell
enabled: true
type: Admin
owningPublisher:
isolation: Application
channelAccess: O:BAG:SYD:(A;;0x2;;;S-1-15-2-1)(A;;0x2;;;S-1-15-3-1024-3153509613-960666767-3724611135-2725662640-12138253-543910227-1950414635-4190290187)(A;;0xf0007;;;SY)(A;;0x7;;;BA)(A;;0x7;;;SO)(A;;0x3;;;IU)(A;;0x3;;;SU)(A;;0x3;;;S-1-5-3)(A;;0x3;;;S-1-5-33)(A;;0x1;;;S-1-5-32-573)
logging:
  logFileName: %SystemRoot%\System32\Winevt\Logs\Windows PowerShell.evtx
  retention: false
  autoBackup: false
  maxSize: 15728640
publishing:
  fileMax: 1

```

Параметр `gli` предоставит нам подробную информацию о состоянии журнала или файла журнала, такую как время создания, время последнего доступа и записи, размер файла, количество записей в журнале и т. д.

  Работа с журналом событий Windows

```cmd-session
C:\htb> wevtutil gli "Windows PowerShell"

creationTime: 2020-10-06T16:57:38.617Z
lastAccessTime: 2022-10-26T19:05:21.533Z
lastWriteTime: 2022-10-26T19:05:21.533Z
fileSize: 11603968
attributes: 32
numberOfLogRecords: 9496
oldestRecordNumber: 1

```

Существует множество способов запроса событий. Например, предположим, что мы хотим отобразить последние 5 событий из журнала безопасности в текстовом формате. Для выполнения этой команды требуется доступ локального администратора.

#### Запрашивающие события

  Работа с журналом событий Windows

```cmd-session
C:\htb> wevtutil qe Security /c:5 /rd:true /f:text

Event[0]
  Log Name: Security
  Source: Microsoft-Windows-Security-Auditing
  Date: 2022-11-16T14:54:13.2270000Z
  Event ID: 4799
  Task: Security Group Management
  Level: Information
  Opcode: Info
  Keyword: Audit Success
  User: N/A
  User Name: N/A
  Computer: ICL-WIN11.greenhorn.corp
  Description: 
A security-enabled local group membership was enumerated.

Subject:
        Security ID:            S-1-5-18
        Account Name:           ICL-WIN11$
        Account Domain:         GREENHORN
        Logon ID:               0x3E7

Group:
        Security ID:            S-1-5-32-544
        Group Name:             Administrators
        Group Domain:           Builtin

Process Information:
        Process ID:             0x56c
        Process Name:           C:\Windows\System32\svchost.exe

Event[1]
  Log Name: Security
  Source: Microsoft-Windows-Security-Auditing
  Date: 2022-11-16T14:54:13.0160000Z
  Event ID: 4672
  Task: Special Logon
  Level: Information
  Opcode: Info
  Keyword: Audit Success
  User: N/A
  User Name: N/A
  Computer: ICL-WIN11.greenhorn.corp
  Description:
Special privileges assigned to new logon.

Subject:
        Security ID:            S-1-5-21-4125911421-2584895310-3954972028-1001        
        Account Name:           htb-student
        Account Domain:         ICL-WIN11
        Logon ID:               0x8F211

Privileges:             SeSecurityPrivilege
                        SeTakeOwnershipPrivilege
                        SeLoadDriverPrivilege
                        SeBackupPrivilege
                        SeRestorePrivilege
                        SeDebugPrivilege
                        SeSystemEnvironmentPrivilege
                        SeImpersonatePrivilege
                        SeDelegateSessionUserImpersonatePrivilege

Event[2]
  Log Name: Security
  Source: Microsoft-Windows-Security-Auditing
  Date: 2022-11-16T14:54:13.0160000Z
  Event ID: 4624
  Task: Logon
  Level: Information
  Opcode: Info
  Keyword: Audit Success
  User: N/A
  User Name: N/A
  Computer: ICL-WIN11.greenhorn.corp
  Description:
An account was successfully logged on.

Subject:
        Security ID:            S-1-5-18
        Account Name:           ICL-WIN11$
        Account Domain:         GREENHORN
        Logon ID:               0x3E7


<SNIP>
```

Мы также можем экспортировать события из определенного журнала для автономной обработки. Для выполнения этого экспорта также требуется локальный администратор.

#### Экспорт событий

  Работа с журналом событий Windows

```cmd-session
C:\htb> wevtutil epl System C:\system_export.evtx
```

---

## Взаимодействие с журналом событий Windows — PowerShell

Аналогичным образом мы можем взаимодействовать с журналами событий Windows с помощью командлета [Get-WinEvent](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.diagnostics/get-winevent?view=powershell-7.3) PowerShell. Как и в примерах с `wevtutil`, для некоторых команд требуется доступ на уровне локального администратора.

Для начала мы можем вывести список всех журналов на компьютере и узнать количество записей в каждом из них.

#### PowerShell — вывод всех журналов

  Работа с журналом событий Windows

```powershell-session
PS C:\htb> Get-WinEvent -ListLog *

LogMode   MaximumSizeInBytes RecordCount LogName
-------   ------------------ ----------- -------
Circular            15728640         657 Windows PowerShell
Circular            20971520       10713 System
Circular            20971520       26060 Security
Circular            20971520           0 Key Management Service
Circular             1052672           0 Internet Explorer
Circular            20971520           0 HardwareEvents
Circular            20971520        6202 Application
Circular             1052672             Windows Networking Vpn Plugin Platform/Op...
Circular             1052672             Windows Networking Vpn Plugin Platform/Op... 
Circular             1052672           0 SMSApi
Circular             1052672          61 Setup
Circular            15728640          24 PowerShellCore/Operational
Circular             1052672          99 OpenSSH/Operational
Circular             1052672          46 OpenSSH/Admin

<SNIP>
```

Мы также можем вывести информацию о конкретном журнале. Здесь мы видим размер журнала `Security`

#### Сведения о журнале безопасности

  Работа с журналом событий Windows

```powershell-session
PS C:\htb> Get-WinEvent -ListLog Security

LogMode   MaximumSizeInBytes RecordCount LogName
-------   ------------------ ----------- -------
Circular            20971520       26060 Security
```

Мы можем запросить последние X событий, в частности последние пять событий, с помощью параметра `-MaxEvents` . Здесь мы перечислим последние пять событий, записанных в журнале безопасности. По умолчанию первыми отображаются самые новые журналы. Если мы хотим, чтобы первыми отображались более старые журналы, мы можем изменить порядок и вывести сначала самые старые журналы с помощью параметра `-Oldest` .

#### Запрос последних пяти событий

  Работа с журналом событий Windows

```powershell-session
PS C:\htb> Get-WinEvent -LogName 'Security' -MaxEvents 5 | Select-Object -ExpandProperty Message

An account was logged off.

Subject:
        Security ID:            S-1-5-111-3847866527-469524349-687026318-516638107-1125189541-6052
        Account Name:           sshd_6052
        Account Domain:         VIRTUAL USERS
        Logon ID:               0x8E787

Logon Type:                     5

This event is generated when a logon session is destroyed. It may be positively correlated with a logon event using the Logon ID value. Logon IDs are only unique between reboots on the same computer.
Special privileges assigned to new logon.

Subject:
        Security ID:            S-1-5-18
        Account Name:           SYSTEM
        Account Domain:         NT AUTHORITY
        Logon ID:               0x3E7

Privileges:             SeAssignPrimaryTokenPrivilege
                        SeTcbPrivilege
                        SeSecurityPrivilege
                        SeTakeOwnershipPrivilege
                        SeLoadDriverPrivilege
                        SeBackupPrivilege
                        SeRestorePrivilege
                        SeDebugPrivilege
                        SeAuditPrivilege
                        SeSystemEnvironmentPrivilege
                        SeImpersonatePrivilege
                        SeDelegateSessionUserImpersonatePrivilege
An account was successfully logged on.

<SNIP>
```

Мы можем копнуть глубже и посмотреть на конкретные идентификаторы событий в конкретных журналах. Допустим, мы хотим посмотреть только на сбои при входе в систему в журнале безопасности и ищем идентификатор события [4625: не удалось войти в систему с учетной записью](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4625). Здесь мы можем использовать параметр `-ExpandProperty` для более глубокого анализа конкретных событий, просмотра журналов от самого старого к самому новому и т. д.

#### Фильтрация при сбоях входа в систему

  Работа с журналом событий Windows

```powershell-session
PS C:\htb> Get-WinEvent -FilterHashTable @{LogName='Security';ID='4625 '}

   ProviderName: Microsoft-Windows-Security-Auditing

TimeCreated                      Id LevelDisplayName Message
-----------                      -- ---------------- -------
11/16/2022 2:53:16 PM          4625 Information      An account failed to log on....  
11/16/2022 2:53:16 PM          4625 Information      An account failed to log on.... 
11/16/2022 2:53:12 PM          4625 Information      An account failed to log on.... 
11/16/2022 2:50:36 PM          4625 Information      An account failed to log on.... 
11/16/2022 2:50:29 PM          4625 Information      An account failed to log on.... 
11/16/2022 2:50:21 PM          4625 Information      An account failed to log on....

<SNIP>
```

Мы также можем просматривать только события с определённым уровнем информативности. Давайте проверим все системные журналы на наличие только `critical` событий с уровнем информативности `1`. Здесь мы видим только одну запись в журнале, в которой система не перезагрузилась корректно.

  Работа с журналом событий Windows

```powershell-session
PS C:\htb> Get-WinEvent -FilterHashTable @{LogName='System';Level='1'} | select-object -ExpandProperty Message

The system has rebooted without cleanly shutting down first. This error could be caused if the system stopped responding, crashed, or lost power unexpectedly.
```

Потренируйтесь с `wevtutil` и `Get-WinEvent`, чтобы вам было проще искать в журналах. Microsoft предоставляет несколько [примеров](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.diagnostics/get-winevent?view=powershell-7.3) для `Get-WinEvent`, а [на этом сайте](https://www.thewindowsclub.com/what-is-wevtutil-and-how-do-you-use-it) показаны примеры для `wevtutil`, а на [этом сайте](https://4sysops.com/archives/search-the-event-log-with-the-get-winevent-powershell-cmdlet/) есть дополнительные примеры использования `Get-WinEvent`.

---

## Двигаемся Дальше

В этом разделе мы познакомились с журналом событий Windows — обширной темой, которую мы подробно рассмотрим в следующих модулях. Попробуйте выполнить различные примеры из этого раздела и освойте использование обоих инструментов для поиска конкретной информации. В следующих модулях мы узнаем, как иногда можно найти конфиденциальные данные, например пароли, в журналах событий. При правильной настройке ведение журналов в Windows становится очень эффективным. Каждая система генерирует огромное количество журналов, и, как мы видели на примере всех возможных идентификаторов событий, мы можем довольно точно настроить, что именно мы хотим регистрировать. Все эти данные сами по себе очень сложны для постоянного анализа, и наиболее эффективно их использовать в сочетании с SIEM-инструментом, который можно настроить на оповещение о конкретных идентификаторах событий, которые могут указывать на атаку, например на подбор учётных данных, подбор пароля или другие менее распространённые атаки. Как специалисты по тестированию на проникновение, мы должны быть знакомы с журналом событий Windows и знать, как использовать его для получения информации об окружающей среде, а иногда даже для извлечения конфиденциальных данных. Для специалистов по «синим командам» крайне важно хорошо разбираться в журнале событий Windows и уметь использовать его для эффективного оповещения и мониторинга.

В следующем разделе мы рассмотрим работу с сетевыми операциями из командной строки в системе Windows.