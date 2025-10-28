#htb #AD #windows 

Права и привилегии являются краеугольными камнями управления Active Directory, и неправильное управление ими может легко привести к злоупотреблениям со стороны злоумышленников или тестировщиков на проникновение. Права доступа и привилегии - это две важные темы в AD (и информационной безопасности в целом), и мы должны понимать разницу. `Rights` обычно назначаются пользователям или группам и имеют дело с разрешениями на `access` объект, такой как файл, в то время как `privileges` предоставляют пользователю разрешения на `perform an action` такие, как запуск программы, завершение работы системы, сброс паролей и т.д. Привилегии могут назначаться пользователям индивидуально или предоставляться им через встроенное или пользовательское членство в группах. На компьютерах с Windows есть понятие под названием `User Rights Assignment`, которое, хотя и называется правами, на самом деле представляет собой тип привилегий, предоставляемых пользователю. Мы обсудим это позже в этом разделе. Мы должны чётко понимать разницу между правами и привилегиями в более широком смысле и знать, как именно они применяются в среде AD.

---

## Встроенные группы Active Directory

AD содержит множество [групп безопасности по умолчанию или встроенных групп безопасности](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups), некоторые из которых предоставляют своим участникам широкие права и привилегии, которыми можно злоупотребить для повышения привилегий в домене и в конечном итоге получить права администратора домена или привилегии SYSTEM на контроллере домена (КД). Необходимо строго контролировать членство во многих из этих групп, поскольку чрезмерное количество участников групп и привилегий является распространённым недостатком многих сетей AD, которым могут воспользоваться злоумышленники. Ниже перечислены некоторые из наиболее распространённых встроенных групп.

|Название группы|Описание|
|---|---|
|`Account Operators`|Участники могут создавать и изменять большинство типов учётных записей, в том числе учётные записи пользователей, локальных и глобальных групп, а также входить в систему локально на контроллерах домена. Они не могут управлять учётной записью администратора, учётными записями административных пользователей или участниками групп «Администраторы», «Операторы серверов», «Операторы учётных записей», «Операторы резервного копирования» или «Операторы печати».|
|`Administrators`|Участники этой группы на контроллере домена имеют полный и неограниченный доступ к компьютеру или всему домену.|
|`Backup Operators`|Участники могут создавать резервные копии и восстанавливать все файлы на компьютере, независимо от установленных для них разрешений. Операторы резервного копирования также могут входить в систему и выключать компьютер. Участники могут локально входить в систему на контроллерах домена и считаются администраторами домена. Они могут создавать теневые копии базы данных SAM/NTDS, которые при извлечении можно использовать для получения учетных данных и другой важной информации.|
|`DnsAdmins`|Участники имеют доступ к сетевой информации DNS. Группа будет создана только в том случае, если роль DNS-сервера установлена или когда-то была установлена на контроллере домена в этом домене.|
|`Domain Admins`|Участники имеют полный доступ к администрированию домена и входят в группу локальных администраторов на всех компьютерах, подключенных к домену.|
|`Domain Computers`|Все компьютеры, созданные в домене (кроме контроллеров домена), добавляются в эту группу.|
|`Domain Controllers`|Содержит все контроллеры домена. Новые контроллеры добавляются в эту группу автоматически.|
|`Domain Guests`|В эту группу входит встроенная гостевая учётная запись домена. У участников этой группы есть профиль домена, который создаётся при входе на компьютер, присоединённый к домену, в качестве локального гостя.|
|`Domain Users`|В этой группе собраны все учётные записи пользователей домена. Новая учётная запись пользователя, созданная в домене, автоматически добавляется в эту группу.|
|`Enterprise Admins`|Членство в этой группе обеспечивает полный доступ к конфигурации в рамках домена. Эта группа существует только в корневом домене леса AD. Членам этой группы предоставляется возможность вносить изменения в масштабах всего леса, например добавлять дочерний домен или создавать доверие. По умолчанию единственным членом этой группы является администратор корневого домена леса.|
|`Event Log Readers`|Участники могут просматривать журналы событий на локальных компьютерах. Группа создается только в том случае, если хост становится контроллером домена.|
|`Group Policy Creator Owners`|Участники создают, редактируют или удаляют объекты групповой политики в домене.|
|`Hyper-V Administrators`|Участники имеют полный и неограниченный доступ ко всем функциям Hyper-V. Если в домене есть виртуальные контроллеры домена, то все администраторы виртуализации, например участники группы «Администраторы Hyper-V», должны считаться администраторами домена.|
|`IIS_IUSRS`|Это встроенная группа, используемая службами IIS (Internet Information Services), начиная с версии IIS 7.0.|
|`Pre–Windows 2000 Compatible Access`|Эта группа существует для обеспечения обратной совместимости с компьютерами под управлением Windows NT 4.0 и более ранних версий. Членство в этой группе часто является пережитком устаревшей конфигурации. Это может привести к уязвимостям, когда любой пользователь в сети может считывать информацию из AD без необходимости вводить действительное имя пользователя и пароль AD.|
|`Print Operators`|Участники могут управлять, создавать, предоставлять общий доступ и удалять принтеры, подключенные к контроллерам домена в этом домене, а также любые объекты принтеров в AD. Участникам разрешен локальный вход на контроллеры домена, и они могут использоваться для загрузки вредоносного драйвера принтера и повышения привилегий в домене.|
|`Protected Users`|Членам этой [группы](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups#protected-users) предоставляется дополнительная защита от кражи учётных данных и таких методов, как злоупотребление протоколом Kerberos.|
|`Read-only Domain Controllers`|Содержит все контроллеры домена только для чтения в домене.|
|`Remote Desktop Users`|Эта группа используется для предоставления пользователям и группам разрешений на подключение к хосту через удалённый рабочий стол (RDP). Эту группу нельзя переименовать, удалить или переместить.|
|`Remote Management Users`|Эту группу можно использовать для предоставления пользователям удалённого доступа к компьютерам через [Windows Remote Management (WinRM)](https://docs.microsoft.com/en-us/windows/win32/winrm/portal)|
|`Schema Admins`|Участники могут изменять схему Active Directory, в которой определены все объекты AD. Эта группа существует только в корневом домене леса AD. По умолчанию единственным участником этой группы является администратор корневого домена леса.|
|`Server Operators`|Эта группа существует только на контроллерах домена. Участники могут изменять службы, получать доступ к общим ресурсам SMB и файлам резервных копий на контроллерах домена. По умолчанию в этой группе нет участников.|

Ниже мы приводим некоторые данные об администраторах доменов и операторах серверов.

#### Сведения о Группе Операторов Сервера

  Права и привилегии в Active Directory

```powershell-session
PS C:\htb>  Get-ADGroup -Identity "Server Operators" -Properties *

adminCount                      : 1
CanonicalName                   : INLANEFREIGHT.LOCAL/Builtin/Server Operators
CN                              : Server Operators
Created                         : 10/27/2021 8:14:34 AM
createTimeStamp                 : 10/27/2021 8:14:34 AM
Deleted                         : 
Description                     : Members can administer domain servers
DisplayName                     : 
DistinguishedName               : CN=Server Operators,CN=Builtin,DC=INLANEFREIGHT,DC=LOCAL
dSCorePropagationData           : {10/28/2021 1:47:52 PM, 10/28/2021 1:44:12 PM, 10/28/2021 1:44:11 PM, 10/27/2021 
                                  8:50:25 AM...}
GroupCategory                   : Security
GroupScope                      : DomainLocal
groupType                       : -2147483643
HomePage                        : 
instanceType                    : 4
isCriticalSystemObject          : True
isDeleted                       : 
LastKnownParent                 : 
ManagedBy                       : 
MemberOf                        : {}
Members                         : {}
Modified                        : 10/28/2021 1:47:52 PM
modifyTimeStamp                 : 10/28/2021 1:47:52 PM
Name                            : Server Operators
nTSecurityDescriptor            : System.DirectoryServices.ActiveDirectorySecurity
ObjectCategory                  : CN=Group,CN=Schema,CN=Configuration,DC=INLANEFREIGHT,DC=LOCAL
ObjectClass                     : group
ObjectGUID                      : 0887487b-7b07-4d85-82aa-40d25526ec17
objectSid                       : S-1-5-32-549
ProtectedFromAccidentalDeletion : False
SamAccountName                  : Server Operators
sAMAccountType                  : 536870912
sDRightsEffective               : 0
SID                             : S-1-5-32-549
SIDHistory                      : {}
systemFlags                     : -1946157056
uSNChanged                      : 228556
uSNCreated                      : 12360
whenChanged                     : 10/28/2021 1:47:52 PM
whenCreated                     : 10/27/2021 8:14:34 AM
```

Как видно из примера выше, по умолчанию группа `Server Operators` не содержит участников и является локальной группой домена. В отличие от неё, группа `Domain Admins` содержит несколько участников и назначенных ей учётных записей служб. Администраторы домена также являются глобальными группами, а не локальными группами домена. Подробнее о членстве в группах можно узнать далее в этом модуле. Будьте осторожны, предоставляя доступ к этим группам. Злоумышленник может легко получить доступ к корпоративным ресурсам, если ему удастся получить доступ к пользователю, назначенному в эти группы.

#### Членство в Группе Администраторов домена

  Права и привилегии в Active Directory

```powershell-session
PS C:\htb>  Get-ADGroup -Identity "Domain Admins" -Properties * | select DistinguishedName,GroupCategory,GroupScope,Name,Members

DistinguishedName : CN=Domain Admins,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
GroupCategory     : Security
GroupScope        : Global
Name              : Domain Admins
Members           : {CN=htb-student_adm,CN=Users,DC=INLANEFREIGHT,DC=LOCAL, CN=sharepoint
                    admin,CN=Users,DC=INLANEFREIGHT,DC=LOCAL, CN=FREIGHTLOGISTICSUSER,OU=Service
                    Accounts,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL, CN=PROXYAGENT,OU=Service
                    Accounts,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL...}
```

---

## Присвоение прав Пользователю

В зависимости от текущего членства в группе и других факторов, таких как привилегии, которые администраторы могут назначать с помощью групповой политики (GPO), пользователям могут быть назначены различные права для их учётной записи. В этой статье Microsoft о [назначении прав пользователей](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/user-rights-assignment) подробно описаны все права пользователей, которые можно настроить в Windows. Не все перечисленные здесь права важны для нас с точки зрения безопасности как для специалистов по тестированию на проникновение или для защитников, но некоторые права, предоставленные учётной записи, могут привести к непредвиденным последствиям, таким как повышение привилегий или доступ к конфиденциальным файлам. Например, предположим, что мы можем получить доступ на запись к объекту групповой политики (ОГП), применённому к подразделению, содержащему одного или нескольких пользователей, которых мы контролируем. В этом примере мы потенциально можем использовать такой инструмент, как [SharpGPOAbuse](https://github.com/FSecureLABS/SharpGPOAbuse), чтобы назначить пользователю целевые права. С помощью этих новых прав мы можем выполнять в домене множество действий. Вот несколько примеров:

|**Привилегия**|**Описание**|
|---|---|
|`SeRemoteInteractiveLogonRight`|Эта привилегия может дать целевому пользователю право входить в систему через удалённый рабочий стол (RDP), что потенциально может быть использовано для получения конфиденциальных данных или повышения привилегий.|
|`SeBackupPrivilege`|Это даёт пользователю возможность создавать резервные копии системы и получать копии конфиденциальных системных файлов, которые можно использовать для восстановления паролей, например, разделов реестра SAM и SYSTEM и файла базы данных Active Directory NTDS.dit.|
|`SeDebugPrivilege`|Это позволяет пользователю выполнять отладку и настраивать память процесса. Имея такие привилегии, злоумышленники могут использовать такой инструмент, как [Mimikatz](https://github.com/ParrotSec/mimikatz), для чтения памяти процесса Local System Authority (LSASS) и получения любых учётных данных, хранящихся в памяти.|
|`SeImpersonatePrivilege`|Эта привилегия позволяет нам выдавать себя за токен привилегированной учётной записи, например `NT AUTHORITY\SYSTEM`. Это можно использовать с помощью таких инструментов, как JuicyPotato, RogueWinRM, PrintSpoofer и т. д., для повышения привилегий в целевой системе.|
|`SeLoadDriverPrivilege`|Пользователь с такими правами может загружать и выгружать драйверы устройств, которые потенциально могут быть использованы для повышения привилегий или взлома системы.|
|`SeTakeOwnershipPrivilege`|Это позволяет процессу стать владельцем объекта. На самом базовом уровне мы могли бы использовать эту привилегию для получения доступа к общему файловому ресурсу или файлу на общем ресурсе, который в противном случае был бы для нас недоступен.|

Существует множество способов злоупотребления правами пользователей, описанных [здесь](https://blog.palantir.com/windows-privilege-abuse-auditing-detection-and-defense-3078a403d74e) и [здесь](https://book.hacktricks.wiki/en/windows-hardening/windows-local-privilege-escalation/privilege-escalation-abusing-tokens.html). Хотя это выходит за рамки данного модуля, важно понимать, к каким последствиям может привести назначение неправильных привилегий учетной записи в Active Directory. Небольшая ошибка администратора может привести к полной компрометации системы или предприятия.

---

## Просмотр привилегий пользователя

После входа в систему на хосте введите команду `whoami /priv`, чтобы получить список всех прав пользователя, назначенных текущему пользователю. Некоторые права доступны только административным пользователям, и их можно просмотреть/использовать только при запуске сеанса CMD или PowerShell с повышенными привилегиями. Эти концепции повышенных прав и [контроля учётных записей (User Account Control, UAC)](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/how-user-account-control-works) — это функции безопасности, появившиеся в Windows Vista, которые по умолчанию ограничивают запуск приложений с полными правами, за исключением случаев крайней необходимости. Если мы сравним права, доступные нам как администратору в консоли без прав администратора и в консоли с правами администратора, то увидим, что они кардинально отличаются. Сначала давайте рассмотрим права, доступные стандартному пользователю Active Directory.

#### Стандартные права пользователя домена

  Права и привилегии в Active Directory

```powershell-session
PS C:\htb> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```

Мы видим, что права очень `limited`, и ни одно из «опасных» прав, описанных выше, не присутствует. Теперь давайте посмотрим на привилегированного пользователя. Ниже перечислены права, доступные пользователю с правами администратора домена.

#### Права администратора домена без повышения

Мы видим следующее в консоли `non-elevated`, которая, судя по всему, доступна только стандартному пользователю домена. Это связано с тем, что по умолчанию в системах Windows нам не предоставляются все права, если только мы не запускаем консоль CMD или PowerShell с повышенными привилегиями. Это сделано для того, чтобы каждое приложение не запускалось с максимально возможными привилегиями. Это контролируется так называемым [контролем учётных записей (User Account Control, UAC)](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/how-user-account-control-works), который подробно рассматривается в модуле [повышение привилегий в Windows](https://academy.hackthebox.com/course/preview/windows-privilege-escalation).

  Права и привилегии в Active Directory

```powershell-session
PS C:\htb> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                          State
============================= ==================================== ========
SeShutdownPrivilege           Shut down the system                 Disabled
SeChangeNotifyPrivilege       Bypass traverse checking             Enabled
SeUndockPrivilege             Remove computer from docking station Disabled
SeIncreaseWorkingSetPrivilege Increase a process working set       Disabled
SeTimeZonePrivilege           Change the time zone                 Disabled
```

#### Повышены права администратора домена

Если мы введём ту же команду в консоли PowerShell с повышенными привилегиями, то увидим полный список доступных нам прав:

  Права и привилегии в Active Directory

```powershell-session
PS C:\htb> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                            Description                                                        State
========================================= ================================================================== ========
SeIncreaseQuotaPrivilege                  Adjust memory quotas for a process                                 Disabled
SeMachineAccountPrivilege                 Add workstations to domain                                         Disabled
SeSecurityPrivilege                       Manage auditing and security log                                   Disabled
SeTakeOwnershipPrivilege                  Take ownership of files or other objects                           Disabled
SeLoadDriverPrivilege                     Load and unload device drivers                                     Disabled
SeSystemProfilePrivilege                  Profile system performance                                         Disabled
SeSystemtimePrivilege                     Change the system time                                             Disabled
SeProfileSingleProcessPrivilege           Profile single process                                             Disabled
SeIncreaseBasePriorityPrivilege           Increase scheduling priority                                       Disabled
SeCreatePagefilePrivilege                 Create a pagefile                                                  Disabled
SeBackupPrivilege                         Back up files and directories                                      Disabled
SeRestorePrivilege                        Restore files and directories                                      Disabled
SeShutdownPrivilege                       Shut down the system                                               Disabled
SeDebugPrivilege                          Debug programs                                                     Enabled
SeSystemEnvironmentPrivilege              Modify firmware environment values                                 Disabled
SeChangeNotifyPrivilege                   Bypass traverse checking                                           Enabled
SeRemoteShutdownPrivilege                 Force shutdown from a remote system                                Disabled
SeUndockPrivilege                         Remove computer from docking station                               Disabled
SeEnableDelegationPrivilege               Enable computer and user accounts to be trusted for delegation     Disabled
SeManageVolumePrivilege                   Perform volume maintenance tasks                                   Disabled
SeImpersonatePrivilege                    Impersonate a client after authentication                          Enabled
SeCreateGlobalPrivilege                   Create global objects                                              Enabled
SeIncreaseWorkingSetPrivilege             Increase a process working set                                     Disabled
SeTimeZonePrivilege                       Change the time zone                                               Disabled
SeCreateSymbolicLinkPrivilege             Create symbolic links                                              Disabled
SeDelegateSessionUserImpersonatePrivilege Obtain an impersonation token for another user in the same session Disabled
```

Права пользователей расширяются в зависимости от групп, в которые они входят, или назначенных им привилегий. Ниже приведён пример прав, предоставленных участнику группы `Backup Operators` . Пользователи в этой группе имеют и другие права, которые в настоящее время ограничены контролем учётных записей (дополнительные права, такие как мощный `SeBackupPrivilege` , по умолчанию не включены в стандартном сеансе консоли). Тем не менее из этой команды видно, что у них есть `SeShutdownPrivilege`, то есть они могут выключать контроллер домена. Эту привилегию нельзя использовать для получения доступа к конфиденциальным данным, но она может привести к серьёзному сбою в работе службы, если пользователь войдёт в систему на контроллере домена локально (а не удалённо через RDP или WinRM).

#### Права резервного оператора

  Права и привилегии в Active Directory

```powershell-session
PS C:\htb> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeShutdownPrivilege           Shut down the system           Disabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```

Как злоумышленникам, так и специалистам по информационной безопасности необходимо понимать, какие права предоставляются пользователям при вступлении во встроенные группы безопасности в Active Directory. Нередко можно обнаружить, что пользователи с ограниченными правами добавлены в одну или несколько таких групп, что может быть использовано для получения дополнительного доступа к домену или его компрометации. Доступ к этим группам должен строго контролироваться. Как правило, лучше всего оставлять большинство этих групп пустыми и добавлять учетную запись в группу только в том случае, если необходимо выполнить разовое действие или настроить повторяющуюся задачу. Все учётные записи, добавленные в одну из групп, описанных в этом разделе, или получившие дополнительные привилегии, должны находиться под строгим контролем и наблюдением, иметь очень надёжный пароль или кодовую фразу и быть отделены от учётной записи, используемой системным администратором для выполнения повседневных задач.

Теперь, когда мы начали рассматривать некоторые аспекты безопасности в AD, связанные с правами пользователей и членством во встроенных группах, давайте обсудим критически важные моменты обеспечения безопасности при установке Active Directory.