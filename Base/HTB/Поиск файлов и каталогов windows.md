#htb #windows #cmd 

Теперь мы можем создавать, изменять, перемещать и удалять файлы и каталоги. Мы должны рассмотреть полезную концепцию, которая может помочь или помешать нам во время работы или в повседневных задачах в качестве `System Administrator` или `Penetration Tester`, известных как `enumeration`. В этом разделе мы рассмотрим, как искать определённые файлы и каталоги с помощью CMD, почему перечисление системных файлов и каталогов так важно, и приведём основной список того, на что следует обратить внимание при перечислении системы.

---

## Поиск С помощью CMD

#### Используя Where

```cmd-session
C:\Users\student\Desktop>where calc.exe

C:\Windows\System32\calc.exe

C:\Users\student\Desktop>where bio.txt

INFO: Could not find files for the given pattern(s).
```

Выше мы видим две разные попытки использования команды `where` . Сначала мы искали `calc.exe`, и она показала нам путь к calc.exe. Эта команда сработала, потому что папка system32 находится в переменной среды PATH, поэтому команда `where` может автоматически просматривать эти папки.

Вторая попытка, которую мы видим, не удалась. Это связано с тем, что мы ищем файл, которого нет в этом каталоге. Он находится в каталоге нашего пользователя. Поэтому нам нужно указать путь для поиска, а чтобы убедиться, что мы просматриваем все каталоги в этом пути, можно использовать переключатель `/R`

#### Рекурсивный Where

```cmd-session
C:\Users\student\Desktop>where /R C:\Users\student\ bio.txt

C:\Users\student\Downloads\bio.txt
```

Выше мы выполнили рекурсивный поиск, ища bio.txt. Файл был найден в `C:\Users\student\Downloads\` папке. `/R`Переключатель вынудил `where` команду выполнять поиск по всем папкам в каталоге student user directory hive. Помимо поиска файлов, мы также можем выполнять поиск по подстановочным знакам для определенных строк, типов файлов и многого другого. Ниже приведен пример поиска по `csv` типу файла в каталоге student.

#### Использование подстановочных Знаков

```cmd-session
C:\Users\student\Desktop>where /R C:\Users\student\ *.csv

C:\Users\student\AppData\Local\live-hosts.csv
```

Мы использовали `where` для того, чтобы понять, как искать файлы и приложения на хосте. Теперь давайте поговорим о `Find`. Команда find используется для поиска текстовых строк или их отсутствия в файле или файлах. Вы также можете использовать `find` для поиска в выводе консоли или другой команде. Однако `find` ограничена в использовании шаблонов подстановочных знаков при поиске. В приведенном ниже примере показан простой поиск с помощью команды find в файле not-password.txt.

#### Основы Find

```cmd-session
C:\Users\student\Desktop> find "password" "C:\Users\student\not-passwords.txt" 
```

Мы можем изменить способ поиска `find` с помощью нескольких переключателей. Модификатор `/V` может изменить поиск с поиска по совпадению на поиск по `Not`-строке. Так, например, если мы используем `/V` с поисковой строкой «пароль» для поиска в файле, он покажет нам все строки, в которых нет указанной строки. Мы также можем использовать переключатель `/N` для отображения номеров строк и переключатель `/I` для игнорирования регистра. В приведённом ниже примере мы используем все модификаторы, чтобы отобразить все строки, которые не соответствуют строке `IP Address` и при этом отобразить номера строк и игнорировать регистр строки.

#### Поиск Модификаторов

```cmd-session
C:\Users\student\Desktop> find /N /I /V "IP Address" example.txt  
```

Для быстрого поиска find прост в использовании, но он мог бы быть более надёжным в том, что касается поиска. Однако, если нам нужно что-то более конкретное, `findstr` — это то, что нам нужно. Команда `findstr` похожа на `find` в том, что она ищет в файлах, но не по шаблонам, а по регулярным выражениям. Она будет искать всё, что соответствует шаблону, значению регулярного выражения, подстановочным знакам и многому другому. Думайте о ней как о find2.0. Для тех, кто знаком с Linux, `findstr` ближе к `grep`.

#### Findstr

```cmd-session
C:\Users\student\Desktop> findstr  
```

### Оценка и сортировка файлов

Мы узнали, как работать с определёнными файлами, искать их и строки внутри файлов. Кроме того, мы научились создавать и изменять файлы. Теперь давайте обсудим несколько способов оценки этих файлов и их сравнения друг с другом. Для этого мы воспользуемся командами [comp](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/comp), [fc](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/fc) и [sort](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/sort).

`Comp` программа проверяет каждый байт в двух файлах на наличие различий, а затем показывает, где они начинаются. По умолчанию различия отображаются в десятичном формате. Если мы хотим увидеть различия в формате ASCII, мы можем использовать модификатор `/A`. Модификатор `/L`, кроме того, может показывать номера строк.

#### Comp

```cmd-session
C:\Users\student\Desktop> comp .\file-1.md .\file-2.md

Comparing .\file-1.md and .\file-2.md...
Files compare OK  
```

Выше мы видим, что сравнение прошло успешно. Файлы совпадают. Мы можем использовать это как простой способ проверить, были ли изменены какие-либо скрипты, исполняемые файлы или важные файлы. Ниже мы приводим вывод из изменённого файла.

#### Сравнение разных файлов

```powershell-session
PS C:\htb> echo a > .\file-1.md
PS C:\Users\MTanaka\Desktop> echo a > .\file-2.md
PS C:\Users\MTanaka\Desktop> comp .\file-1.md .\file-2.md /A
Comparing .\file-1.md and .\file-2.md...
Files compare OK
<SNIP>
PS C:\Users\MTanaka\Desktop> echo b > .\file-2.md
PS C:\Users\MTanaka\Desktop> comp .\file-1.md .\file-2.md /A
Comparing .\file-1.md and .\file-2.md...
Compare error at OFFSET 2
file1 = a
file2 = b  
```

Мы использовали команду echo, чтобы убедиться, что строки отличаются, а затем снова запустили сравнение. Обратите внимание, как изменился наш вывод, и теперь, используя модификатор /A, мы видим разницу в символах между двумя файлами. `Comp` — это простой, но эффективный инструмент. Теперь давайте посмотрим на `FC` в течение минуты. `FC` отличается тем, что показывает, какие строки отличаются, а не только отдельные символы (`/A`) или байты, которые отличаются в каждой строке. У FC гораздо больше возможностей, чем у Comp, поэтому обязательно ознакомьтесь со справочной информацией, чтобы убедиться, что вы используете его так, как вам нужно.

#### Справка FC

```cmd-session
C:\htb> fc.exe /?

Compares two files or sets of files and displays the differences between
them

FC [/A] [/C] [/L] [/LBn] [/N] [/OFF[LINE]] [/T] [/U] [/W] [/nnnn]
   [drive1:][path1]filename1 [drive2:][path2]filename2
FC /B [drive1:][path1]filename1 [drive2:][path2]filename2

  /A         Displays only first and last lines for each set of differences.
  /B         Performs a binary comparison.
  /C         Disregards the case of letters.
  /L         Compares files as ASCII text.
  /LBn       Sets the maximum consecutive mismatches to the specified
             number of lines.
  /N         Displays the line numbers on an ASCII comparison.
  /OFF[LINE] Do not skip files with offline attribute set.
  /T         Does not expand tabs to spaces.
  /U         Compare files as UNICODE text files.
  /W         Compresses white space (tabs and spaces) for comparison.
  /nnnn      Specifies the number of consecutive lines that must match
             after a mismatch.
  [drive1:][path1]filename1
             Specifies the first file or set of files to compare.
  [drive2:][path2]filename2
             Specifies the second file or set of files to compare.
```

При проверке FC учитывает регистр и выполняет не только побайтовое сравнение. Ниже мы используем несколько файлов с большим количеством символов и строк, чтобы проверить его функциональность. Мы выполним базовую проверку и выведем номера строк и сравнение по ASCII с помощью модификатора `/N`.

#### FC

```cmd-session
C:\Users\student\Desktop> fc passwords.txt modded.txt /N

Comparing files passwords.txt and MODDED.TXT
***** passwords.txt
    1:  123456
    2:  password
***** MODDED.TXT
    1:  123456
    2:
    3:  password
*****

***** passwords.txt
    5:  12345
    6:  qwerty
***** MODDED.TXT
    6:  12345
    7:  Just something extra to show functionality. Did it see the space inserted above?
    8:  qwerty
*****
```

Выходные данные FC гораздо проще интерпретировать, и они дают нам немного больше информации о различиях между файлами. При сравнении таких файлов, как текстовые файлы, электронные таблицы или списки, рекомендуется сначала отсортировать их, чтобы убедиться, что данные в каждой строке совпадают. В противном случае каждая строка будет отличаться, и наше сравнение не поможет. Давайте посмотрим на `sort` и воспользуемся им. С помощью `Sort` мы можем получать ввод с консоли, из конвейера или из файла, сортировать его и отправлять результаты на консоль, в файл или в другую команду. Он относительно прост в использовании и часто применяется в сочетании с операторами конвейера, такими как `|`, `<`, и `>`. Мы можем попробовать его прямо сейчас, передав содержимое файла `file` для сортировки.

#### Sort

```cmd-session
C:\Users\student\Desktop> type .\file-1.md
a
b
d
h
w
a
q
h
g

C:\Users\MTanaka\Desktop> sort.exe .\file-1.md /O .\sort-1.md
C:\Users\MTanaka\Desktop> type .\sort-1.md

a
a
b
d
g
h
h
q
w
```

Выше мы видим, что при использовании `sort` для файла `file-1.md` и последующей отправке результата с модификатором `/O` в файл sort-1.md мы взяли список букв, отсортировали их в алфавитном порядке и записали в новый файл. При работе с большими наборами данных всё может быть сложнее, но принцип использования остаётся прежним. Если мы хотим, чтобы `sort` возвращала только уникальные записи, мы также можем использовать модификатор /unique. Обратите внимание на первые две записи в файле `sort-1.md` . Давайте попробуем использовать модификатор unique и посмотрим, что произойдёт.

#### unique

```cmd-session
C:\htb> type .\sort-1.md

a
a
b
d
g
h
h
q
w

PS C:\Users\MTanaka\Desktop> sort.exe .\sort-1.md /unique

a
b
d
g
h
q
w  
```

Обратите внимание, что теперь у нас меньше общих результатов. Это связано с тем, что `sort` не записывал повторяющиеся записи из файла в консоль.

---

Поиск файлов и каталогов, сортировка наборов данных и сравнение файлов — всё это важные навыки, которые должны быть в нашем арсенале. Далее мы обсудим переменные среды и то, что они дают нам как пользователям командной строки.