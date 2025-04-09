#windows #htb 

#### Графический Пользовательский интерфейс

Концепция графического пользовательского интерфейса (GUI) была представлена в конце 1970-х годов исследовательской лабораторией Xerox в Пало-Альто. Он был добавлен в операционные системы Apple и Microsoft, чтобы повысить удобство использования для обычных пользователей, которым, вероятно, было бы сложно ориентироваться в командной строке. Большинству обычных пользователей Windows не нужно взаимодействовать с операционной системой через командную строку. Как следует из названия, графический интерфейс предоставляет пользователям интерактивный интерфейс с возможностью щелчков мышью для взаимодействия с операционной системой, установленными приложениями и службами.

Появление графического интерфейса открыло широкие возможности и обеспечило доступ к компьютерам для многих демографических групп, поскольку пользователи могли взаимодействовать со своим компьютером, не запоминая команды и не зная ни одного языка программирования. Системные администраторы обычно используют системы на основе графического интерфейса для администрирования Active Directory, настройки IIS или взаимодействия с базами данных.

---

## Протокол удаленного рабочего стола (RDP)

[RDP](https://support.microsoft.com/en-us/help/186607/understanding-the-remote-desktop-protocol-rdp) — это проприетарный протокол Microsoft, который позволяет пользователю подключаться к удалённой системе по сети и получать доступ к графическому пользовательскому интерфейсу. Пользователь подключается с помощью клиентского программного обеспечения RDP к целевой системе, на которой запущено программное обеспечение RDP-сервера. RDP использует порт 3389 для открытия выделенного сетевого канала для передачи данных. При подключении по RDP пользователь может получить доступ к графическому интерфейсу, как если бы он находился за компьютером и входил в систему локально. RDP часто используется системными администраторами для быстрого администрирования удалённых систем. Это также позволяет пользователям получать доступ к своим рабочим компьютерам во время путешествий или работы из дома после подключения к виртуальной частной сети (VPN).

---

## Командная строка Windows

Интерфейсы командной строки дают пользователям больше возможностей для управления своими системами и могут использоваться для выполнения широкого спектра повседневных, административных задач и устранения неполадок. Их можно использовать для автоматизации выполнения определённых задач (например, одновременного добавления множества пользователей в домен). В операционных системах Windows основными способами взаимодействия с системой из командной строки являются командная строка (CMD) и PowerShell.

[Справочник по командам Windows](https://download.microsoft.com/download/5/8/9/58911986-D4AD-4695-BF63-F734CD4DF8F2/ws-commands.pdf) от Microsoft — это полный справочник по командам от А до Я, который включает обзор, примеры использования и синтаксис большинства команд Windows. Рекомендуется ознакомиться с ним.

---

## CMD

Командная строка (cmd.exe) используется для ввода и выполнения команд. Пользователь может вводить разовые команды, например `ipconfig` для просмотра информации об IP-адресе, или выполнять более сложные задачи, например настройку запланированных задач или создание скриптов и пакетных файлов. Командную строку можно открыть из меню «Пуск», введя `cmd` в диалоговом окне «Выполнить» или напрямую запустив двоичный файл из `C:\Windows\system32\cmd.exe`.

После запуска `cmd.exe` мы можем ввести`help`, чтобы увидеть список доступных команд.

  Взаимодействие с операционной системой Windows

```cmd-session
C:\htb> help
For more information on a specific command, type HELP command-name
ASSOC          Displays or modifies file extension associations.
ATTRIB         Displays or changes file attributes.
BREAK          Sets or clears extended CTRL+C checking.
BCDEDIT        Sets properties in boot database to control boot loading.
CACLS          Displays or modifies access control lists (ACLs) of files.
CALL           Calls one batch program from another.
CD             Displays the name of or changes the current directory.
CHCP           Displays or sets the active code page number.
CHDIR          Displays the name of or changes the current directory.
CHKDSK         Checks a disk and displays a status report.
CHKNTFS        Displays or modifies the checking of disk at boot time.
CLS            Clears the screen.
CMD            Starts a new instance of the Windows command interpreter.
COLOR          Sets the default console foreground and background colors.
COMP           Compares the contents of two files or sets of files.
COMPACT        Displays or alters the compression of files on NTFS partitions.
CONVERT        Converts FAT volumes to NTFS.  You cannot convert the
               current drive.
COPY           Copies one or more files to another location.

<SNIP>
```

Для получения дополнительной информации о конкретной команде мы можем ввести `help <command name>`.

  Взаимодействие с операционной системой Windows

```cmd-session
C:\htb> help schtasks

SCHTASKS /parameter [arguments]

Description:
    Enables an administrator to create, delete, query, change, run and
    end scheduled tasks on a local or remote system.

Parameter List:
    /Create         Creates a new scheduled task.

    /Delete         Deletes the scheduled task(s).

    /Query          Displays all scheduled tasks.

    /Change         Changes the properties of scheduled task.

    /Run            Runs the scheduled task on demand.

    /End            Stops the currently running scheduled task.

    /ShowSid        Shows the security identifier corresponding to a scheduled task name.

    /?              Displays this help message.

Examples:
    SCHTASKS
    SCHTASKS /?
    SCHTASKS /Run /?
    SCHTASKS /End /?
    SCHTASKS /Create /?
    SCHTASKS /Delete /?
    SCHTASKS /Query  /?
    SCHTASKS /Change /?
    SCHTASKS /ShowSid /?
```

Обратите внимание, что у некоторых команд есть собственные справочные меню, доступ к которым можно получить, введя `<command> /?`. Например, информацию о команде `ipconfig` можно увидеть ниже.

  Взаимодействие с операционной системой Windows

```cmd-session
C:\htb> ipconfig /?

USAGE:
    ipconfig [/allcompartments] [/? | /all |
                                 /renew [adapter] | /release [adapter] |
                                 /renew6 [adapter] | /release6 [adapter] |
                                 /flushdns | /displaydns | /registerdns |
                                 /showclassid adapter |
                                 /setclassid adapter [classid] |
                                 /showclassid6 adapter |
                                 /setclassid6 adapter [classid] ]

where
    adapter             Connection name
                       (wildcard characters * and ? allowed, see examples)

    Options:
       /?               Display this help message
       /all             Display full configuration information.
       /release         Release the IPv4 address for the specified adapter.
       /release6        Release the IPv6 address for the specified adapter.
       /renew           Renew the IPv4 address for the specified adapter.
       /renew6          Renew the IPv6 address for the specified adapter.
       /flushdns        Purges the DNS Resolver cache.
       /registerdns     Refreshes all DHCP leases and re-registers DNS names
       /displaydns      Display the contents of the DNS Resolver Cache.
       /showclassid     Displays all the dhcp class IDs allowed for adapter.
       /setclassid      Modifies the dhcp class id.
       /showclassid6    Displays all the IPv6 DHCP class IDs allowed for adapter.
       /setclassid6     Modifies the IPv6 DHCP class id.

<SNIP
```

---

## PowerShell

Windows PowerShell — это командная оболочка, разработанная Microsoft для системных администраторов. PowerShell, как и командная строка Windows, имеет интерактивную командную строку, а также мощную среду разработки сценариев. PowerShell построен на базе .NET Framework, которая используется для создания и запуска приложений в Windows. Это делает его очень мощным инструментом для прямого взаимодействия с операционной системой.

Как и командная строка, PowerShell предоставляет нам прямой доступ к файловой системе, и мы можем выполнять большинство тех же команд, что и в командной строке.

---

## Командлеты

PowerShell использует [командлеты](https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/cmdlet-overview?view=powershell-7), которые представляют собой небольшие однофункциональные инструменты, встроенные в оболочку. Существует более 100 основных командлетов, а также множество дополнительных, которые мы можем создавать самостоятельно для выполнения более сложных задач. PowerShell также поддерживает как простые, так и сложные сценарии, используемые для задач системного администрирования, автоматизации и многого другого.

Командлеты имеют форму `Verb-Noun`. Например, команда `Get-ChildItem` может использоваться для вывода списка содержимого текущего каталога. Командлеты также принимают аргументы или флаги. Мы можем ввести `Get-ChildItem -` и нажать клавишу Tab, чтобы перебрать аргументы. Такая команда, как `Get-ChildItem -Recurse` выведет содержимое текущего рабочего каталога и всех подкаталогов. Другой пример: `Get-ChildItem -Path C:\Users\Administrator\Documents` для вывода содержимого другого каталога. Наконец, мы можем комбинировать аргументы, например, для рекурсивного вывода содержимого всех подкаталогов в другом каталоге: `Get-ChildItem -Path C:\Users\Administrator\Downloads -Recurse`.

---

## Псевдонимы

У многих командлетов в PowerShell также есть псевдонимы. Например, псевдонимы командлета `Set-Location` для смены каталога — `cd` или `sl`. Псевдонимы командлета `Get-ChildItem` — `ls` и `gci`. Мы можем просмотреть все доступные псевдонимы, введя `Get-Alias`.

  Взаимодействие с операционной системой Windows

```powershell-session
PS C:\htb> get-alias

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           % -> ForEach-Object
Alias           ? -> Where-Object
Alias           ac -> Add-Content
Alias           asnp -> Add-PSSnapin
Alias           cat -> Get-Content
Alias           cd -> Set-Location
Alias           CFS -> ConvertFrom-String                          3.1.0.0    Microsoft.PowerShell.Utility
Alias           chdir -> Set-Location
Alias           clc -> Clear-Content
Alias           clear -> Clear-Host
Alias           clhy -> Clear-History
Alias           cli -> Clear-Item
Alias           clp -> Clear-ItemProperty
```

Мы также можем создавать собственные псевдонимы с помощью `New-Alias` и получать псевдоним для любого командлета с помощью `Get-Alias -Name`.

  Взаимодействие с операционной системой Windows

```powershell-session
PS C:\htb> New-Alias -Name "Show-Files" Get-ChildItem
PS C:\> Get-Alias -Name "Show-Files"

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Show-Files
```

В PowerShell есть справочная система для командлетов, функций, скриптов и концепций. Она не установлена по умолчанию, но мы можем запустить команду `Get-Help <cmdlet-name> -Online` для открытия онлайн-справки по командлету или функции в веб-браузере. Мы можем ввести `Update-Help` для загрузки и установки файлов справки локально.

  Взаимодействие с операционной системой Windows

```powershell-session
PS C:\htb> help

TOPIC
    Windows PowerShell Help System

SHORT DESCRIPTION
    Displays help about Windows PowerShell cmdlets and concepts.

LONG DESCRIPTION
    Windows PowerShell Help describes Windows PowerShell cmdlets,
    functions, scripts, and modules, and explains concepts, including
    the elements of the Windows PowerShell language.

    Windows PowerShell does not include help files, but you can read the
    help topics online, or use the Update-Help cmdlet to download help files
    to your computer and then use the Get-Help cmdlet to display the help
    topics at the command line.

    You can also use the Update-Help cmdlet to download updated help files
    as they are released so that your local help content is never obsolete.

    Without help files, Get-Help displays auto-generated help for cmdlets,
    functions, and scripts.


  ONLINE HELP
    You can find help for Windows PowerShell online in the TechNet Library
    beginning at http://go.microsoft.com/fwlink/?LinkID=108518.

    To open online help for any cmdlet or function, type:

        Get-Help <cmdlet-name> -Online
		
<SNIP>
```

При вводе команды, например `Get-Help Get-AppPackage` , будет отображаться только частичная справка, если не установлены файлы справки.

  Взаимодействие с операционной системой Windows

```powershell-session
PS C:\htb>  Get-Help Get-AppPackage

NAME
    Get-AppxPackage

SYNTAX
    Get-AppxPackage [[-Name] <string>] [[-Publisher] <string>] [-AllUsers] [-PackageTypeFilter {None | Main |
    Framework | Resource | Bundle | Xap | Optional | All}] [-User <string>] [-Volume <AppxVolume>]
    [<CommonParameters>]


ALIASES
    Get-AppPackage


REMARKS
    Get-Help cannot find the Help files for this cmdlet on this computer. It is displaying only partial help.
        -- To download and install Help files for the module that includes this cmdlet, use Update-Help.
```

---

## Запуск скриптов

PowerShell ISE (интегрированная среда разработки сценариев) позволяет пользователям писать сценарии PowerShell на лету. В ней также есть функция автозаполнения/поиска команд PowerShell. PowerShell ISE позволяет писать и запускать сценарии в одной консоли, что упрощает отладку.

Мы можем запускать сценарии PowerShell разными способами. Если мы знаем функции, то можем запустить сценарий локально или после загрузки в память с помощью загрузочной консоли, как в приведённом ниже примере.

  Взаимодействие с операционной системой Windows

```powershell-session
PS C:\htb> .\PowerView.ps1;Get-LocalGroup |fl

Description     : Users of Docker Desktop
Name            : docker-users
SID             : S-1-5-21-674899381-4069889467-2080702030-1004
PrincipalSource : Local
ObjectClass     : Group

Description     : VMware User Group
Name            : __vmware__
SID             : S-1-5-21-674899381-4069889467-2080702030-1003
PrincipalSource : Local
ObjectClass     : Group

Description     : Members of this group can remotely query authorization attributes and permissions for resources on
                  this computer.
Name            : Access Control Assistance Operators
SID             : S-1-5-32-579
PrincipalSource : Local
ObjectClass     : Group

Description     : Administrators have complete and unrestricted access to the computer/domain
Name            : Administrators
SID             : S-1-5-32-544
PrincipalSource : Local

<SNIP>
```

Один из распространённых способов работы со скриптом в PowerShell — импортировать его, чтобы все функции были доступны в текущей консоли PowerShell: `Import-Module .\PowerView.ps1`. Затем мы можем либо запустить команду и перебирать параметры, либо ввести `Get-Module` для просмотра списка всех загруженных модулей и связанных с ними команд.

  Взаимодействие с операционной системой Windows

```powershell-session
PS C:\htb> Get-Module | select Name,ExportedCommands | fl


Name             : Appx
ExportedCommands : {[Add-AppxPackage, Add-AppxPackage], [Add-AppxVolume, Add-AppxVolume], [Dismount-AppxVolume,
                   Dismount-AppxVolume], [Get-AppxDefaultVolume, Get-AppxDefaultVolume]...}

Name             : Microsoft.PowerShell.LocalAccounts
ExportedCommands : {[Add-LocalGroupMember, Add-LocalGroupMember], [Disable-LocalUser, Disable-LocalUser],
                   [Enable-LocalUser, Enable-LocalUser], [Get-LocalGroup, Get-LocalGroup]...}

Name             : Microsoft.PowerShell.Management
ExportedCommands : {[Add-Computer, Add-Computer], [Add-Content, Add-Content], [Checkpoint-Computer,
                   Checkpoint-Computer], [Clear-Content, Clear-Content]...}

Name             : Microsoft.PowerShell.Utility
ExportedCommands : {[Add-Member, Add-Member], [Add-Type, Add-Type], [Clear-Variable, Clear-Variable], [Compare-Object,
                   Compare-Object]...}

Name             : PSReadline
ExportedCommands : {[Get-PSReadLineKeyHandler, Get-PSReadLineKeyHandler], [Get-PSReadLineOption,
                   Get-PSReadLineOption], [Remove-PSReadLineKeyHandler, Remove-PSReadLineKeyHandler],
                   [Set-PSReadLineKeyHandler, Set-PSReadLineKeyHandler]...}
```

---

## Политика Исполнения

Иногда мы обнаруживаем, что не можем запускать скрипты в системе. Это связано с функцией безопасности под названием `execution policy`, которая пытается предотвратить выполнение вредоносных скриптов. Возможные политики:

|**Политика**|**Описание**|
|---|---|
|`AllSigned`|Все скрипты могут запускаться, но доверенный издатель должен подписывать скрипты и файлы конфигурации. Это касается как удалённых, так и локальных скриптов. Перед запуском скриптов, подписанных издателями, которых мы ещё не внесли в список доверенных или недоверенных, мы получаем запрос.|
|`Bypass`|Никакие скрипты или файлы конфигурации не блокируются, и пользователь не получает никаких предупреждений или запросов.|
|`Default`|Это устанавливает политику выполнения по умолчанию `Restricted` для настольных компьютеров Windows и `RemoteSigned` для серверов Windows.|
|`RemoteSigned`|Скрипты могут работать, но для скриптов, загруженных из Интернета, требуется цифровая подпись. Цифровая подпись не требуется для скриптов, написанных локально.|
|`Restricted`|Это позволяет выполнять отдельные команды, но не позволяет запускать скрипты. Все типы файлов скриптов, включая файлы конфигурации (`.ps1xml`), файлы скриптов модулей (`.psm1`), а также профили PowerShell (`.ps1`) заблокированы.|
|`Undefined`|Для текущей области видимости не задана политика выполнения. Если политика выполнения для ВСЕХ областей видимости не задана, то будет использоваться политика выполнения `Restricted` по умолчанию.|
|`Unrestricted`|Это политика выполнения по умолчанию для компьютеров, не использующих Windows, и её нельзя изменить. Эта политика позволяет запускать неподписанные скрипты, но предупреждает пользователя перед запуском скриптов, которые не относятся к локальной зоне интрасети.|

Ниже приведен пример текущей политики выполнения для всех областей.

  Взаимодействие с операционной системой Windows

```powershell-session
PS C:\htb> Get-ExecutionPolicy -List

        Scope ExecutionPolicy
        ----- ---------------
MachinePolicy       Undefined
   UserPolicy       Undefined
      Process       Undefined
  CurrentUser       Undefined
 LocalMachine    RemoteSigned
```

Политика выполнения не предназначена для ограничения действий пользователя с целью обеспечения безопасности. Пользователь может легко обойти политику, введя содержимое скрипта непосредственно в окно PowerShell, загрузив и запустив скрипт или указав скрипт в виде закодированной команды. Также можно обойти политику, настроив её (если у пользователя есть соответствующие права) или задав политику выполнения для текущего процесса (что может сделать практически любой пользователь, так как это не требует изменения конфигурации и будет действовать только в течение сеанса пользователя).

Ниже приведён пример изменения политики выполнения для текущего процесса (сессии).

  Взаимодействие с операционной системой Windows

```powershell-session
PS C:\htb> Set-ExecutionPolicy Bypass -Scope Process

Execution Policy Change
The execution policy helps protect you from scripts that you do not trust. Changing the execution policy might expose
you to the security risks described in the about_Execution_Policies help topic at
https:/go.microsoft.com/fwlink/?LinkID=135170. Do you want to change the execution policy?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"): Y
```

Теперь мы видим, что политика выполнения была изменена.

  Взаимодействие с операционной системой Windows

```powershell-session
PS C:\htb>  Get-ExecutionPolicy -List

        Scope ExecutionPolicy
        ----- ---------------
MachinePolicy       Undefined
   UserPolicy       Undefined
      Process          Bypass
  CurrentUser       Undefined
 LocalMachine    RemoteSigned
```
