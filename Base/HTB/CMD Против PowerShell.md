#cmd #powershell #windows #htb 

До этого момента мы обсуждали встроенный интерпретатор командной строки Windows `cmd.exe`. Далее мы рассмотрим современного преемника CMD в Windows — [PowerShell](https://learn.microsoft.com/en-us/powershell/scripting/overview?view=powershell-7.2). В этом разделе мы расскажем, что такое PowerShell, в чём разница между PowerShell и CMD, как получить справку в командной строке и как перемещаться по командной строке.

---

## Различия

PowerShell и CMD изначально включены в любую версию Windows, поэтому мы можем задаться вопросом: «Зачем мне использовать один из них вместо другого?» Давайте быстро разберёмся в этом. Ниже приведена таблица с некоторыми различиями между PowerShell и CMD.

#### Сравнение PowerShell и CMD

|**Особенность**|**CMD**|**PowerShell**|
|---|---|---|
|Язык|Только пакетные и базовые команды CMD.|PowerShell может интерпретировать командлеты Batch, CMD, PS и псевдонимы.|
|Использование команд|Вывод одной команды нельзя напрямую передать другой в виде структурированного объекта из-за ограничений обработки текстового вывода.|Результат выполнения одной команды можно передать в другую напрямую в виде структурированного объекта, что позволяет создавать более сложные команды.|
|Вывод команды|Только текст.|PowerShell выводит данные в формате объекта.|
|Параллельное выполнение|CMD должен завершить одну команду перед запуском другой.|PowerShell может выполнять многопоточные команды параллельно.|

В частности, PowerShell был создан для того, чтобы быть `extensible` и при необходимости интегрироваться со многими другими инструментами и функциями. Большинство считает его просто ещё одним интерфейсом командной строки, но это гораздо больше. Знаете ли вы, что он также является `scripting language`? В то время как CMD был интерпретатором командной строки по умолчанию только для хостов Windows, PowerShell был выпущен как [проект с открытым исходным кодом](https://github.com/PowerShell/PowerShell) и обладает широким набором возможностей, которые позволяют использовать его в системах на базе Linux. Использование фреймворка `.NET` также позволило PowerShell использовать объектную модель взаимодействия и вывода вместо текстовой.

### Почему вы выбрали PowerShell вместо cmd.exe?

`Why does PowerShell matter for IT admins, Offensive & Defensive Infosec pros`?

[PowerShell](https://docs.microsoft.com/en-us/powershell/) становится всё более популярным среди специалистов в области информационных технологий и информационной безопасности. Он широко используется системными администраторами, тестировщиками на проникновение, аналитиками SOC и во многих других технических областях, где администрируются системы Windows. Рассмотрим ИТ-администраторов и системных администраторов Windows, которые управляют ИТ-средами, состоящими из серверов Windows, настольных компьютеров (Windows 10 и 11), Azure и облачных приложений Microsoft 365. Многие из них используют PowerShell для автоматизации задач, которые они ежедневно выполняют. Среди таких задач:

- Подготовка серверов и установка серверных ролей
- Создание учетных записей пользователей Active Directory для новых сотрудников
- Управление разрешениями группы Active Directory
- Отключение и удаление учетных записей пользователей Active Directory
- Управление разрешениями для общего доступа к файлам
- Взаимодействие с [Azure](https://azure.microsoft.com/en-us/) AD и виртуальными машинами Azure
- Создание, удаление и мониторинг каталогов и файлов
- Сбор информации о рабочих станциях и серверах
- Настройка почтовых ящиков Microsoft Exchange для пользователей (в облаке и/или локально)

Существует множество способов использования PowerShell в контексте ИТ-администрирования, и понимание этого контекста может быть полезно для нас как для `penetration testers` и `even as defenders`. Как для системного администратора, PowerShell может предоставить нам гораздо больше возможностей, чем `CMD`. Он `expandable` создан для `automation` и написания сценариев, имеет гораздо более надёжную реализацию безопасности и может выполнять множество различных задач, которые просто не может выполнять CMD. Для пентестеров в PowerShell встроены многие известные возможности. Возможность импорта модулей в PowerShell позволяет легко внедрять наши инструменты в среду и обеспечивать их работоспособность. Однако с точки зрения скрытности PowerShell с его возможностями `logging` и `history` является мощным инструментом и регистрирует больше наших взаимодействий с хостом. Поэтому, если нам не нужны возможности PowerShell и мы хотим действовать более скрытно, нам следует использовать CMD.

---

## Вызов PowerShell

Мы можем получить доступ к PowerShell непосредственно на хосте через периферийные устройства, подключённые к локальному компьютеру, или через RDP по сети различными способами.

1. Использование Windows `Search`

Мы можем ввести `PowerShell` в поиске Windows, чтобы найти и запустить приложение PowerShell и командную консоль.

![[SearchingForPowerShell.webp]]

2. Использование приложения Windows `Terminal`

[Windows Terminal](https://github.com/Microsoft/Terminal) — это новое приложение-эмулятор терминала, разработанное Microsoft, которое позволяет любому пользователю командной строки Windows получить доступ к нескольким различным интерфейсам командной строки, системам и подсистемам с помощью одного приложения. Это приложение, скорее всего, станет эмулятором терминала по умолчанию в операционных системах Windows. ![[PowerShellinWindowsTerminal.gif]]

3. Использование Windows `PowerShell ISE`

[Интегрированная среда сценариев Windows PowerShell (ISE)](https://docs.microsoft.com/en-us/powershell/scripting/windows-powershell/ise/introducing-the-windows-powershell-ise?view=powershell-7.2) похожа на IDE для PowerShell. Она может упростить разработку, отладку и тестирование создаваемых нами сценариев PowerShell. Использование PowerShell ISE может быть невероятно полезным при изучении PowerShell.

![[PowerShellISE.gif]]

4. Использование PowerShell в `CMD`

Мы также можем запустить PowerShell из командной строки. Это действие может показаться простым, но, несомненно, наступит момент, когда мы сможем получить доступ к командной строке уязвимой целевой системы Windows через командную строку и воспользуемся PowerShell для расширения доступа к хосту и сети.

![[LaunchingPowerShellfromCMD.gif]]

### Взглянув на Оболочку

Одна из первых вещей, которую мы можем проверить при доступе к PowerShell в локальной или удалённой системе, — это приглашение.

## Приглашение PowerShell

  CMD Против. PowerShell

```powershell-session
PS C:\Users\htb-student> ipconfig 

Ethernet adapter VMware Network Adapter VMnet8:

   Connection-specific DNS Suffix  . :
   Link-local IPv6 Address . . . . . : fe80::adb8:3c9:a8af:114%25
   IPv4 Address. . . . . . . . . . . : 172.16.110.1
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . :
```

Приглашение почти идентично тому, что мы видим в CMD.

- `PS` является сокращением от PowerShell, за которым следует текущий рабочий каталог `C:\Users\htb-student>`.
- Далее следует командлет или строка, которую мы хотим выполнить, `ipconfig`.
- Наконец, ниже мы видим выходные результаты нашей команды.

Как и в случае с CMD, в PowerShell есть множество команд и командлетов, которые можно использовать. Почти все команды, которые работают в CMD, будут работать и в PowerShell. В этом модуле мы рассмотрим только некоторые возможные команды. Важно понимать, что запоминание команд не имеет особого смысла. Лучше сосредоточиться на понимании контекста, концепций и того, что возможно. Запоминание естественным образом произойдёт со временем, если вы будете практиковаться и повторять.

---

## Get-Help

- Использование функции справки. Если мы хотим просмотреть доступные параметры и функции конкретного командлета, мы можем использовать командлет [Get-Help](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/get-help?view=powershell-7.2).

#### Использование Get-Help

  CMD Против. PowerShell

```powershell-session
PS C:\Users\htb-student> Get-Help Test-Wsman

NAME
    Test-WSMan

SYNTAX
    Test-WSMan [[-ComputerName] <string>] [-Authentication {None | Default | Digest | Negotiate | Basic | Kerberos |
    ClientCertificate | Credssp}] [-Port <int>] [-UseSSL] [-ApplicationName <string>] [-Credential <pscredential>]
    [-CertificateThumbprint <string>]  [<CommonParameters>]


ALIASES
    None


REMARKS
    Get-Help cannot find the Help files for this cmdlet on this computer. It is displaying only partial help.
        -- To download and install Help files for the module that includes this cmdlet, use Update-Help.
        -- To view the Help topic for this cmdlet online, type: "Get-Help Test-WSMan -Online" or
           go to https://go.microsoft.com/fwlink/?LinkId=141464.
```

Get-Help может предоставить полезную информацию о командлете. Обратите внимание, что в выводе `Syntax` показаны несколько доступных параметров и дополнительные ключевые слова, которые можно использовать с каждым параметром. `Aliases` Также упоминаются псевдонимы, то есть более короткие названия наших команд. Мы подробнее обсудим псевдонимы позже в этом разделе. В выводе `Remarks` содержится дополнительная информация о командлете и даже дополнительные параметры, которые мы можем использовать, чтобы узнать больше о командлете. Один из таких дополнительных параметров — `-online`, который откроет веб-страницу с документацией Microsoft по соответствующему командлету, если у хоста есть доступ в Интернет.

![[GetHelpOnline.gif]]

Мы также можем использовать полезный командлет [Update-Help](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/update-help?view=powershell-7.2), чтобы получать самую актуальную информацию о каждом командлете в системе Windows.

#### Использование Update-Help

  CMD Против. PowerShell

```powershell-session
PS C:\Windows\system32> Update-Help
```

Обратите внимание, насколько больше информации стало доступно о `Test-Wsman` после запуска `Update-Help`. Не стесняйтесь сравнивать этот вывод с выводом, показанным ранее, когда мы впервые рассматривали Get-Help.

#### Использование Get-Help После запуска Update-Help

  CMD Против. PowerShell

```powershell-session
PS C:\Windows\system32> Get-Help  Test-Wsman

NAME
    Test-WSMan

SYNOPSIS
    Tests whether the WinRM service is running on a local or remote computer.


SYNTAX
    Test-WSMan [[-ComputerName] <System.String>] [-ApplicationName <System.String>]
    [-Authentication {None | Default | Digest | Negotiate | Basic | Kerberos |
    ClientCertificate | Credssp}] [-CertificateThumbprint <System.String>]
    [-Credential <System.Management.Automation.PSCredential>] [-Port <System.Int32>]
    [-UseSSL] [<CommonParameters>]


DESCRIPTION
    The `Test-WSMan` cmdlet submits an identification request that determines
    whether the WinRM service is running on a local or remote computer. If the
    tested computer is running the service, the cmdlet displays the WS-Management
    identity schema, the protocol version, the product vendor, and the product
    version of the tested service.


RELATED LINKS
    Online Version: https://docs.microsoft.com/powershell/module/microsoft.wsman.mana
    gement/test-wsman?view=powershell-5.1&WT.mc_id=ps-gethelp
    Connect-WSMan
    Disable-WSManCredSSP
    Disconnect-WSMan
    Enable-WSManCredSSP
    Get-WSManCredSSP
    Get-WSManInstance
    Invoke-WSManAction
    New-WSManInstance
    New-WSManSessionOption
    Remove-WSManInstance
    Set-WSManInstance
    Set-WSManQuickConfig

REMARKS
    To see the examples, type: "get-help Test-WSMan -examples".
    For more information, type: "get-help Test-WSMan -detailed".
    For technical information, type: "get-help Test-WSMan -full".
    For online help, type: "get-help Test-WSMan -online"
```

---

## Знакомство с PowerShell

Теперь, когда мы разобрались, что такое PowerShell и изучили основы встроенных функций помощи, давайте перейдём к базовой навигации и использованию PowerShell.

### Где мы находимся?

Мы можем перемещаться только в том случае, если знаем, где находимся, верно? Мы можем определить наш текущий рабочий каталог (по отношению к хост-системе), используя командлет `Get-Location`

#### Get-Location

  CMD Против. PowerShell

```powershell-session
PS C:\Users\DLarusso> Get-Location

Path
----
C:\Users\DLarusso
```

Мы видим, что он выводит полный путь к каталогу, с которым мы сейчас работаем; в данном случае это `C:\Users\DLarusso`. Теперь, когда мы сориентировались, давайте посмотрим, какие объекты и файлы существуют в этом каталоге.

### Перечислите Каталог

Командлет `Get-ChildItem` может отображать содержимое текущего каталога или указанного нами каталога.

#### Get-ChildItem

  CMD Против. PowerShell

```powershell-session
PS C:\htb> Get-ChildItem 

Directory: C:\Users\DLarusso


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        10/26/2021  10:26 PM                .ssh
d-----         1/28/2021   7:05 PM                .vscode
d-r---         1/27/2021   2:44 PM                3D Objects
d-r---         1/27/2021   2:44 PM                Contacts
d-r---         9/18/2022  12:35 PM                Desktop
d-r---         9/18/2022   1:01 PM                Documents
d-r---         9/26/2022  12:27 PM                Downloads
d-r---         1/27/2021   2:44 PM                Favorites
d-r---         1/27/2021   2:44 PM                Music
dar--l         9/26/2022  12:03 PM                OneDrive
d-r---         5/22/2022   2:00 PM                Pictures
```

В нашем текущем рабочем каталоге мы видим несколько других каталогов. Давайте рассмотрим один из них.

### Переместить в новый каталог

Изменить наше местоположение очень просто; мы можем сделать это с помощью командлета `Set-Location`.

#### Set-Location

  CMD Против. PowerShell

```powershell-session
PS C:\htb>  Set-Location .\Documents\

PS C:\Users\tru7h\Documents> Get-Location

Path
----
C:\Users\DLarusso\Documents

```

Мы передали параметры `.\Documents\` командлету Set-Location, указав PowerShell, что хотим перейти в каталог «Документы», который находится в нашем текущем рабочем каталоге. Мы также могли указать полный путь к файлу следующим образом:

Код: powershell

```powershell
Set-Location C:\Users\DLarusso\Documents  
```

### Отображать содержимое файла

Теперь, если мы хотим просмотреть содержимое файла, мы можем использовать `Get-Content`. Заглянув в каталог «Документы», мы видим файл под названием `Readme.md`. Давайте посмотрим на него.

#### Get-Content

  CMD Против. PowerShell

```powershell-session
PS C:\htb> Get-Content Readme.md  

# ![logo][] PowerShell

Welcome to the PowerShell GitHub Community!
PowerShell Core is a cross-platform (Windows, Linux, and macOS) automation and configuration tool/framework that works well with your existing tools and is optimized
for dealing with structured data (e.g., JSON, CSV, XML, etc.), REST APIs, and object models.
It includes a command-line shell, an associated scripting language and a framework for processing cmdlets. 

<SNIP> 
```

Похоже, что файл Readme был взят со страницы PowerShell на GitHub. Использовать командлет `Get-Content` так же просто, как и это. Навигация в командной строке PowerShell довольно проста. Теперь, когда мы освоили этот навык, давайте рассмотрим несколько полезных советов и рекомендаций, которые помогут вам ещё эффективнее использовать командную строку.

## Советы и рекомендации по использованию PowerShell

### Get-Command

`Get-Command` Это отличный способ найти нужную команду, которая может ускользнуть из нашей памяти в тот момент, когда она нам понадобится. В PowerShell для поиска командлетов используется `verb-noun`-конвенция, и мы можем искать по ней.

#### Использование Get-Command

  CMD Против. PowerShell

```powershell-session
PS C:\htb> Get-Command

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Add-AppPackage                                     2.0.1.0    Appx
Alias           Add-AppPackageVolume                               2.0.1.0    Appx
Alias           Add-AppProvisionedPackage                          3.0        Dism
Alias           Add-ProvisionedAppPackage                          3.0        Dism
Alias           Add-ProvisionedAppxPackage                         3.0        Dism
Alias           Add-ProvisioningPackage                            3.0        Provisioning
Alias           Add-TrustedProvisioningCertificate                 3.0        Provisioning
Alias           Apply-WindowsUnattend                              3.0        Dism
Alias           Disable-PhysicalDiskIndication                     2.0.0.0    Storage
Alias           Disable-StorageDiagnosticLog                       2.0.0.0    Storage
Alias           Dismount-AppPackageVolume                          2.0.1.0    Appx
Alias           Enable-PhysicalDiskIndication                      2.0.0.0    Storage
Alias           Enable-StorageDiagnosticLog                        2.0.0.0    Storage
Alias           Flush-Volume                                       2.0.0.0    Storage
Alias           Get-AppPackage                                     2.0.1.0    Appx

<SNIP>  
```

Приведённый выше вывод был сокращён для экономии места на экране. При использовании `Get-Command` без дополнительных модификаторов будет выводиться полный вывод каждого командлета, загруженного в текущий сеанс PowerShell. Мы можем сократить его ещё больше, отфильтровав по `verb` или `noun` части командлета.

#### Get-Command (verb)

  CMD Против. PowerShell

```powershell-session
PS C:\htb> Get-Command -verb get

<SNIP>
Cmdlet          Get-Acl                                            3.0.0.0    Microsoft.Pow...
Cmdlet          Get-Alias                                          3.1.0.0    Microsoft.Pow...
Cmdlet          Get-AppLockerFileInformation                       2.0.0.0    AppLocker
Cmdlet          Get-AppLockerPolicy                                2.0.0.0    AppLocker
Cmdlet          Get-AppvClientApplication                          1.0.0.0    AppvClient  
<SNIP>  
```

Используя модификатор `-verb` и ища любой командлет, псевдоним или функцию с термином «get» в названии, мы получаем подробный список всего, о чём в данный момент знает PowerShell. Мы также можем выполнить точный поиск, используя фильтр `get*` вместо `-verb` `get`. Командлет Get-Command распознаёт `*` как подстановочный знак и показывает все варианты `get` (что угодно). Мы можем сделать нечто подобное, выполнив поиск по существительному.

#### Get-Command (noun)

  CMD Против. PowerShell

```powershell-session
PS C:\htb> Get-Command -noun windows*  

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Apply-WindowsUnattend                              3.0        Dism
Function        Get-WindowsUpdateLog                               1.0.0.0    WindowsUpdate
Cmdlet          Add-WindowsCapability                              3.0        Dism
Cmdlet          Add-WindowsDriver                                  3.0        Dism
Cmdlet          Add-WindowsImage                                   3.0        Dism
Cmdlet          Add-WindowsPackage                                 3.0        Dism
Cmdlet          Clear-WindowsCorruptMountPoint                     3.0        Dism
Cmdlet          Disable-WindowsErrorReporting                      1.0        WindowsErrorR...
Cmdlet          Disable-WindowsOptionalFeature                     3.0        Dism
Cmdlet          Dismount-WindowsImage                              3.0        Dism
Cmdlet          Enable-WindowsErrorReporting                       1.0        WindowsErrorR...
Cmdlet          Enable-WindowsOptionalFeature                      3.0        Dism
```

В приведенном выше выводе мы использовали модификатор `-noun`, продвинули фильтр на шаг дальше и искали любую часть существительного, которая содержала `windows*`, поэтому наши результаты оказались довольно конкретными. `Anything` который начинается с windows в части существительного и за ним следует все остальное, что будет `match` этим фильтром. Это были лишь несколько демонстраций того, насколько мощным может быть `Get-Command` командлет. В сочетании с `Get-Help` командлетом это могут быть мощные справочные функции, предоставляемые нам непосредственно PowerShell. Наш следующий совет посвящен истории наших сеансов PowerShell.

### История

PowerShell сохраняет историю выполненных команд двумя разными способами. Первый — это встроенная история сеанса, которая реализуется и удаляется в начале и в конце каждого сеанса консоли. Второй способ — с помощью модуля `PSReadLine`. Модуль `PSReadLine`, помимо многих других функций, отслеживает историю всех команд PowerShell, используемых во всех сеансах на хосте. По умолчанию PowerShell сохраняет последние 4096 введенных команд, но этот параметр можно изменить, изменив переменную `$MaximumHistoryCount`.

#### Get-History

  CMD Против. PowerShell

```powershell-session
PS C:\htb> Get-History

 Id CommandLine
  -- -----------
   1 Get-Command
   2 clear
   3 get-command -verb set
   4 get-command set*
   5 clear
   6 get-command -verb get
   7 get-command -noun windows
   8 get-command -noun windows*
   9 get-module
  10 clear
  11 get-history
  12 clear
  13 ipconfig /all
  14 arp -a
  15 get-help
  16 get-help get-module
```

По умолчанию `Get-History` будет отображать только те команды, которые были выполнены во время этого активного сеанса. Обратите внимание на то, что команды пронумерованы; мы можем вспомнить эти команды, используя псевдоним `r` с последующим номером, чтобы снова выполнить эту команду. Например, если мы хотим повторно выполнить команду `arp -a`, мы можем ввести `r 14`, и PowerShell выполнит её. Имейте в виду, что если мы закроем окно командной строки или в случае удалённой командной строки через командную строку и управление, как только мы завершим сеанс или процесс, который мы запускаем, история PowerShell исчезнет. Однако в случае с `PSReadLine` это не так. `PSReadLine` сохраняет все в файле под названием `$($host.Name)_history.txt` по адресу `$env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine`.

#### Просмотр истории строк PSReadline

  CMD Против. PowerShell

```powershell-session
PS C:\htb> get-content C:\Users\DLarusso\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt

get-module
Get-ChildItem Env: | ft Key,Value
Get-ExecutionPolicy
clear
ssh administrator@10.172.16.110.55
powershell -nop -c "iex(New-Object Net.WebClient).DownloadString('https://download.sysinternals.com/files/PSTools.zip')"
Get-ExecutionPolicy

<SNIP>
```

Если бы мы выполнили приведённую выше команду и часто пользовались интерфейсом командной строки, у нас был бы обширный файл истории, который нужно было бы просмотреть. Приведённый выше вывод был сокращён для экономии времени и места на экране. Одна из замечательных особенностей `PSReadline` с точки зрения администратора заключается в том, что он автоматически попытается отфильтровать все записи, содержащие строки:

- `password`
- `asplaintext`
- `token`
- `apikey`
- `secret`

Такое поведение отлично подходит нам, администраторам, поскольку позволяет удалять из файла истории `PSReadLine` любые записи, содержащие ключи, учётные данные или другую конфиденциальную информацию. Встроенная история сеансов этого не делает.

### Чистый Экран

Этот совет касается удобства. Если нас раздражает, что на экране постоянно отображается много информации, мы можем удалить текст из окна консоли с помощью команды `Clear-Host`. Это повлияет только на текущий вывод и не избавит нас от переменных или других объектов, которые мы могли создать во время сеанса. Мы также можем использовать `clear` или `cls` если предпочитаем короткие команды или псевдонимы.

### Горячие клавиши

Если мы не работаем в командной строке из среды с графическим интерфейсом, наша мышь `not` часто будет работать. Например, предположим, что мы проникли на `shell` хост во время пентеста. Из этой оболочки у нас будет доступ к CMD или PowerShell, но мы не сможем использовать `GUI`. Поэтому нам нужно уметь пользоваться только клавиатурой. `Hotkeys` позволяет выполнять более сложные действия, для которых обычно требуется мышь, с помощью клавиш. Ниже приведён краткий список наиболее полезных сочетаний клавиш.

#### Горячие клавиши

|**Горячая клавиша**|**Описание**|
|---|---|
|`CTRL+R`|Это позволяет искать в истории. Мы можем начать вводить текст, и он покажет нам результаты, соответствующие предыдущим командам.|
|`CTRL+L`|Быстрая очистка экрана.|
|`CTRL+ALT+Shift+?`|При этом будет выведен весь список сочетаний клавиш, которые распознает PowerShell.|
|`Escape`|При вводе в командной строке, если вы хотите удалить всю строку, вместо того чтобы удерживать клавишу Backspace, вы можете просто нажать `escape`, и строка будет удалена.|
|`↑`|Прокрутите вверх нашу предыдущую историю.|
|`↓`|Прокрутите вниз нашу предыдущую историю.|
|`F7`|Открывает TUI с прокручиваемой интерактивной историей нашего сеанса.|

В этом списке перечислены не все функции, которые мы можем использовать в PowerShell, но те, которыми мы пользуемся чаще всего.

### Завершение вкладки

Одной из лучших функций PowerShell, несомненно, является автозаполнение команд с помощью клавиши Tab. Мы можем использовать `tab` и `SHIFT+tab` для перехода между вариантами, которые могут дополнить вводимую нами команду.

#### Пример автозаполнения

![[tab.gif]]

### Псевдонимы

Последний совет, о котором мы хотим упомянуть, — это `Aliases`. Псевдоним PowerShell — это другое название командлета, команды или исполняемого файла. Список псевдонимов по умолчанию можно увидеть с помощью командлета [Get-Alias](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/get-alias?view=powershell-7.2). Большинство встроенных псевдонимов — это сокращённые версии командлетов, которые легче запомнить и быстрее использовать.

#### Использование Get-Alias

  CMD Против. PowerShell

```powershell-session
PS C:\Windows\system32> Get-Alias

CommandType     Name                                               Version    Source
                                                                              
-----------     ----                                               -------    -----
Alias           % -> ForEach-Object
Alias           ? -> Where-Object
Alias           ac -> Add-Content
Alias           asnp -> Add-PSSnapin
Alias           cat -> Get-Content
Alias           cd -> Set-Location
Alias           CFS -> ConvertFrom-String                          3.1.0.0    Mi...
Alias           chdir -> Set-Location
Alias           clc -> Clear-Content
Alias           clear -> Clear-Host
Alias           clhy -> Clear-History
Alias           cli -> Clear-Item
Alias           clp -> Clear-ItemProperty
Alias           cls -> Clear-Host
Alias           clv -> Clear-Variable
Alias           cnsn -> Connect-PSSession
Alias           compare -> Compare-Object
Alias           copy -> Copy-Item
Alias           cp -> Copy-Item
Alias           cpi -> Copy-Item
Alias           cpp -> Copy-ItemProperty
Alias           curl -> Invoke-WebRequest
Alias           cvpa -> Convert-Path
Alias           dbp -> Disable-PSBreakpoint
Alias           del -> Remove-Item
Alias           diff -> Compare-Object
Alias           dir -> Get-ChildItem

<SNIP>
```

Отличной практикой является сокращение псевдонимов до имени фактического командлета, команды или исполняемого файла. Даже у `Get-Alias` командлета по умолчанию есть псевдоним `gal`, как показано в ролике ниже.

![[GalAlias.gif]]

Мы также можем создать псевдоним для определённого командлета с помощью [Set-Alias](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/set-alias?view=powershell-7.2). Давайте потренируемся, создав псевдоним для командлета `Get-Help`

#### Использование Set-Alias

  CMD Против. PowerShell

```powershell-session
PS C:\Windows\system32> Set-Alias -Name gh -Value Get-Help
```

При использовании `Set-Alias` необходимо указать имя псевдонима (`-Name gh`) и соответствующий командлет (`-Value Get-Help`).

![[SetAlias.gif]]

Ниже мы также приводим список нескольких псевдонимов, которые, по нашему мнению, наиболее полезны. У некоторых команд есть несколько псевдонимов. Обязательно ознакомьтесь с полным списком, чтобы найти другие псевдонимы, которые могут вам пригодиться.

#### Полезные Псевдонимы

| **Псевдоним** | **Описание**                                                                                                               |
| ------------- | -------------------------------------------------------------------------------------------------------------------------- |
| `pwd`         | gl также можно использовать. Этот псевдоним можно использовать вместо Get-Location.                                        |
| `ls`          | Вместо ls можно также использовать dir и gci. Это псевдонимы для Get-ChildItem.                                            |
| `cd`          | Вместо cd можно использовать sl и chdir. Это псевдоним для Set-Location.                                                   |
| `cat`         | Также можно использовать type и gc. Это псевдоним для Get-Content.                                                         |
| `clear`       | Может использоваться вместо Clear-Host.                                                                                    |
| `curl`        | Curl — это псевдоним для Invoke-WebRequest, который можно использовать для загрузки файлов. Также можно использовать wget. |
| `fl and ft`   | Эти псевдонимы можно использовать для форматирования выходных данных в виде списков и таблиц.                              |
| `man`         | Может быть использован вместо справки.                                                                                     |

Те, кто знаком с `BASH`, возможно, заметили, что многие псевдонимы соответствуют командам, широко используемым в дистрибутивах Linux. Эти знания могут быть полезны и помогут облегчить процесс обучения.

---

Этот раздел получился довольно длинным, и на то есть веская причина. Мы рассмотрели всё необходимое для того, чтобы двигаться по пути к мастерству в PowerShell. Далее мы подробно рассмотрим модули и командлеты PowerShell.