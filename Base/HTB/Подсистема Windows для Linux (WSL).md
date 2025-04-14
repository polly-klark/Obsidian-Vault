#windows #htb 

[WSL](https://docs.microsoft.com/en-us/windows/wsl/) — это функция, которая позволяет запускать двоичные файлы Linux в Windows 10 и Windows Server 2019. Изначально она была предназначена для разработчиков, которым нужно было запускать Bash, Ruby и собственные инструменты командной строки Linux, такие как `sed`, `awk`, `grep` и т. д., непосредственно на рабочей станции Windows. Во второй версии WSL, выпущенной в мае 2019 года, появилось настоящее ядро Linux, использующее часть функций Hyper-V.

WSL можно установить, запустив команду PowerShell `Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux` от имени администратора. После включения этой функции мы можем либо загрузить дистрибутив Linux из Microsoft Store и установить его, либо вручную загрузить дистрибутив Linux по нашему выбору, распаковать и установить его из командной строки.

WSL устанавливает приложение под названием `Bash.exe`, которое можно запустить, просто введя `bash` в консоли Windows, чтобы открыть оболочку Bash. В этой оболочке мы видим всё то же, что и на хосте Linux, включая стандартную структуру каталогов Linux.

  Подсистема Windows для Linux (WSL)

```powershell-session
PS C:\htb> ls /

bin dev home lib lLib64 media opt root sbin srv tmp var
boot etc init 1lib32 Libx32 mnt proc run Snap sys usr
```

Мы можем получить доступ к диску C$ и другим дискам в операционной системе хоста через каталог `mnt`, что упрощает переход с хоста WSL на хост-ОС Windows. Оказавшись в этой оболочке bash, мы можем взаимодействовать с WSL так же, как с любой операционной системой на базе Linux: запускать команды, устанавливать обновления/пакеты и т. д.

  Подсистема Windows для Linux (WSL)

```powershell-session
PS C:\htb> uname -a

Linux WS01 4.4.0-18362-Microsoft #476-Microsoft Frit Nov 01 16:53:00
PST 2019 x86_64 x86 _64 x86_64 GNU/Linux
```
