#forensic #writeup 

> Без агента EDR еще раз, мы создали изображение этой рабочей станции, чтобы вы могли найти зло !

Просматривая файлы, которые нам выдают, `.ova` которые представляют собой образ виртуальной машины, я загружаю виртуальную машину в VirtualBox, и нам выдается рабочий стол Windows. При осмотре компьютера на рабочем столе есть README, который дает очень мало информации.

Объединив некоторые подсказки из описания, а также названия, мы можем построить следующее:

- "Песочные часы", вероятно, каким-то образом связаны со временем.
- "Нет EDR", вероятно, какая-то инфекция.
- Следующая задача называется "Без grep", вероятно, ее можно каким-то образом найти.

В итоге я заглянул в историю Powershell (которая пригодится без grep), но обнаружил выполнение чего-то под названием `time_stomp`, которое я соотнес с именем "Песочные часы".

`time_stomp` это утилита, используемая как злоумышленниками для фальсификации временных меток файлов, так и my blue teams для просмотра временных меток файлов.

В итоге я изучил некоторые подходы к криминалистике Windows без регистрации и узнал о `C:\Windows\Prefetch` и изучил [PECmd](https://github.com/EricZimmerman/PECmd) для их анализа. Просматривая записи, которые я замечаю `TIMESTOMP` там и сталкиваюсь `PECmd` с этим.

```
PS> dir 'C:\Windows\Prefetch' | sort LastWriteTime -desc
...
-a----         1/11/2024   3:22 AM           1754 STOMP_TIME.EXE-4A8F4213.pf
...
PS> .\PECmd.exe -f 'C:\Windows\Prefetch\STOMP_TIME.EXE-4A8F4213.pf' --mp
...
Directories referenced: 9
00: \VOLUME{01da447765fe8034-ae6629b1}\USERS
01: \VOLUME{01da447765fe8034-ae6629b1}\USERS\ANALYST
02: \VOLUME{01da447765fe8034-ae6629b1}\USERS\ANALYST\APPDATA
03: \VOLUME{01da447765fe8034-ae6629b1}\USERS\ANALYST\APPDATA\LOCAL
04: \VOLUME{01da447765fe8034-ae6629b1}\USERS\ANALYST\APPDATA\LOCAL\MICROSOFT
05: \VOLUME{01da447765fe8034-ae6629b1}\USERS\ANALYST\DESKTOP
06: \VOLUME{01da447765fe8034-ae6629b1}\WINDOWS
07: \VOLUME{01da447765fe8034-ae6629b1}\WINDOWS\SYSTEM32
08: \VOLUME{01da447765fe8034-ae6629b1}\WINDOWS\SYSWOW64
...
```

Заглядывая внутрь, `C:\USERS\ANALYST\APPDATA\LOCAL\MICROSOFT` как указано, мне приходит в голову идея проверить историю.

Я нашел `C:\Users\analyst\AppData\Local\Microsoft\Windows\History`.

![[Pasted image 20240824002335.png]]

Содержит некоторые записи файлов, созданных до запуска виртуальной машины.

Внутри "Пятницы" есть следующее:

![[Pasted image 20240824002350.png]]

Внутри файла `settings.txt` находится строка Base64: `Ky0tCiB1b2Z0Y3Rme1Q0c0tfU2NoM0R1bDNyX0ZVTn0KKy0t`

Decoded дает нам флаг:

```
+--
 uoftctf{T4sK_Sch3Dul3r_FUN}
+--
```
