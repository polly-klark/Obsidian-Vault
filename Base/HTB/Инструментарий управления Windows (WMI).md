#htb #windows 

WMI — это подсистема PowerShell, которая предоставляет системным администраторам мощные инструменты для мониторинга системы. Цель WMI — объединить управление устройствами и приложениями в корпоративных сетях. WMI является основной частью операционной системы Windows и предустановлен с Windows 2000. Он состоит из следующих компонентов:

|**Имя компонента**|**Описание**|
|---|---|
|Служба WMI|Процесс Windows Management Instrumentation, который запускается автоматически при загрузке и выступает в качестве посредника между поставщиками WMI, хранилищем WMI и приложениями для управления.|
|Управляемые объекты|Любые логические или физические компоненты, которыми может управлять WMI.|
|Поставщики WMI|Объекты, которые отслеживают события / данные, относящиеся к определенному объекту.|
|Классы|Они используются поставщиками WMI для передачи данных в службу WMI.|
|Методы|Они привязаны к классам и позволяют выполнять действия. Например, методы можно использовать для запуска/остановки процессов на удалённых компьютерах.|
|Репозиторий WMI|База данных, в которой хранятся все статические данные, относящиеся к WMI.|
|Менеджер объектов CIM|Система, которая запрашивает данные у поставщиков WMI и возвращает их запрашивающему приложению.|
|WMI API|Позволяет приложениям получать доступ к инфраструктуре WMI.|
|Потребитель WMI|Отправляет запросы к объектам через CIM Object Manager.|

Вот некоторые из вариантов использования WMI:

- Информация о состоянии для локальных / удаленных систем
- Настройка параметров безопасности на удаленных компьютерах / приложениях
- Настройка и изменение прав доступа пользователей и групп
- Настройка / изменение системных свойств
- Выполнение кода
- Процессы планирования
- Настройка ведения журнала

Все эти задачи можно выполнить с помощью комбинации PowerShell и интерфейса командной строки WMI (WMIC). WMI можно запустить из командной строки Windows, введя `WMIC` для открытия интерактивной оболочки или выполнив напрямую команду, например `wmic computersystem get name` для получения имени хоста. Список команд и псевдонимов WMIC можно просмотреть, введя `WMIC /?`.

  Инструментарий управления Windows (WMI)

```cmd-session
C:\htb> wmic /?

WMIC is deprecated.

[global switches] <command>

The following global switches are available:
/NAMESPACE           Path for the namespace the alias operate against.
/ROLE                Path for the role containing the alias definitions.
/NODE                Servers the alias will operate against.
/IMPLEVEL            Client impersonation level.
/AUTHLEVEL           Client authentication level.
/LOCALE              Language id the client should use.
/PRIVILEGES          Enable or disable all privileges.
/TRACE               Outputs debugging information to stderr.
/RECORD              Logs all input commands and output.
/INTERACTIVE         Sets or resets the interactive mode.
/FAILFAST            Sets or resets the FailFast mode.
/USER                User to be used during the session.
/PASSWORD            Password to be used for session login.
/OUTPUT              Specifies the mode for output redirection.
/APPEND              Specifies the mode for output redirection.
/AGGREGATE           Sets or resets aggregate mode.
/AUTHORITY           Specifies the <authority type> for the connection.
/?[:<BRIEF|FULL>]    Usage information.

For more information on a specific global switch, type: switch-name /?

Press any key to continue, or press the ESCAPE key to stop
```

В следующем примере команды приведена информация об операционной системе.

  Инструментарий управления Windows (WMI)

```cmd-session
C:\htb> wmic os list brief

BuildNumber  Organization  RegisteredUser  SerialNumber             SystemDirectory      Version
19041                      Owner           00123-00123-00123-AAOEM  C:\Windows\system32  10.0.19041
```

WMIC использует псевдонимы и связанные с ними глаголы, наречия и переключатели. Приведенный выше пример команды использует `LIST` для отображения данных, а наречие `BRIEF` - для предоставления только основного набора свойств. Подробный список глаголов, переключателей и наречий доступен [здесь](https://docs.microsoft.com/en-us/windows/win32/wmisdk/wmic). WMI можно использовать с PowerShell с помощью `Get-WmiObject` [модуля](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-wmiobject?view=powershell-5.1). Этот модуль используется для получения экземпляров классов WMI или информации о доступных классах. Этот модуль может использоваться на локальных или удаленных компьютерах.

Здесь мы можем получить информацию об операционной системе.

  Инструментарий управления Windows (WMI)

```powershell-session
PS C:\htb> Get-WmiObject -Class Win32_OperatingSystem | select SystemDirectory,BuildNumber,SerialNumber,Version | ft

SystemDirectory     BuildNumber SerialNumber            Version
---------------     ----------- ------------            -------
C:\Windows\system32 19041       00123-00123-00123-AAOEM 10.0.19041
```

Мы также можем использовать `Invoke-WmiMethod` [модуль](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/invoke-wmimethod?view=powershell-5.1), который используется для вызова методов объектов WMI. Простой пример — переименование файла. Мы видим, что команда выполнена успешно, потому что `ReturnValue` равно 0.

  Инструментарий управления Windows (WMI)

```powershell-session
PS C:\htb> Invoke-WmiMethod -Path "CIM_DataFile.Name='C:\users\public\spns.csv'" -Name Rename -ArgumentList "C:\Users\Public\kerberoasted_users.csv"


__GENUS          : 2
__CLASS          : __PARAMETERS
__SUPERCLASS     :
__DYNASTY        : __PARAMETERS
__RELPATH        :
__PROPERTY_COUNT : 1
__DERIVATION     : {}
__SERVER         :
__NAMESPACE      :
__PATH           :
ReturnValue      : 0
PSComputerName   :
```

В этом разделе представлен краткий обзор `WMI`, `WMIC` и комбинирование `WMIC` и `PowerShell`. `WMI` имеет широкий спектр применений как для операторов синей, так и для красной команды. В последующих разделах этого курса будут показаны некоторые способы, которые `WMI` можно использовать в нападении как для перечисления, так и для бокового перемещения.