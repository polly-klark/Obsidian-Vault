#htb #powershell #windows 

Мы уже знаем, как перемещаться по хосту и управлять пользователями и группами с помощью одного лишь PowerShell. Теперь пришло время изучить файлы и каталоги. В этом разделе мы поэкспериментируем с созданием, изменением и удалением файлов и каталогов, а также кратко рассмотрим права доступа к файлам и способы их перечисления. К этому моменту мы уже должны быть знакомы с `Get, Set, New`-глаголами, поэтому мы ускорим процесс с помощью примеров, объединив несколько команд в одном сеансе работы с оболочкой.

---

## Создание/перемещение/удаление файлов и каталогов

Многие командлеты, которые мы рассмотрим в этом разделе, можно использовать для работы с файлами и папками, поэтому мы объединим некоторые из наших действий, чтобы работать эффективнее (к чему должен стремиться любой хороший пентестер или системный администратор). В таблице ниже перечислены наиболее часто используемые командлеты для работы с объектами в PowerShell.

#### Распространённые команды для управления файлами и папками

|**Команда**|**Псевдоним**|**Описание**|
|---|---|---|
|`Get-Item`|ги|Извлеките объект (это может быть файл, папка, объект реестра и т. д.).|
|`Get-ChildItem`|ls / каталог / gci|Выводит список содержимого папки или раздела реестра.|
|`New-Item`|md / mkdir / ni|Создавайте новые объекты. (это могут быть файлы, папки, символические ссылки, записи реестра и многое другое)|
|`Set-Item`|si|Измените значения свойств объекта.|
|`Copy-Item`|копия / cp / ci|Сделайте дубликат предмета.|
|`Rename-Item`|ren / rni|Изменяет имя объекта.|
|`Remove-Item`|rm / del / rmdir|Удаляет объект.|
|`Get-Content`|кошка / тип|Отображает содержимое файла или объекта.|
|`Add-Content`|переменный ток|Добавление содержимого в файл.|
|`Set-Content`|sc|перезаписать любое содержимое файла новыми данными.|
|`Clear-Content`|clc|Очистите содержимое файлов, не удаляя их.|
|`Compare-Object`|различать / сравнивать|Сравните два или более объектов друг с другом. Это касается как самого объекта, так и его содержимого.|

**Сценарий: новый начальник службы безопасности Greenhorn, мистер Танака, попросил создать для него набор файлов и папок. Он планирует использовать их для составления инструкций по технике безопасности для сотрудников службы безопасности. Поскольку он только что получил доступ к хосту, мы согласились создать для него структуру файлов и папок. Если вы хотите следовать приведённым ниже примерам, пожалуйста, не стесняйтесь. Для вашей практики мы удалили папки и файлы, о которых идёт речь ниже, чтобы вы могли попробовать создать их заново.**

Для начала мы создадим структуру папок, которую он запросил. Мы создадим три папки с названиями:

- `SOPs`
    - `Physical Sec`
    - `Cyber Sec`
    - `Training`

Для создания структуры папок мы будем использовать команды `Get-Item`, `Get-ChildItem`, и `New-Item`. Приступим. Сначала нам нужно определить `where we are` на хосте, а затем перейти в папку `Documents` мистера Танаки.

#### Где я

  Работа с файлами и каталогами — PowerShell

```powershell-session
PS C:\htb> Get-Location

Path
----
C:\Users\MTanaka

PS C:\Users\MTanaka> cd Documents
PS C:\Users\MTanaka\Documents>
```

Теперь, когда мы находимся в нужном каталоге, пора приступать к работе. Далее нам нужно создать папку SOPs. Для этого можно использовать командлет New-Item.

#### New-Item

  Работа с файлами и каталогами — PowerShell

```powershell-session
PS C:\Users\MTanaka\Documents>  new-item -name "SOPs" -type directory


    Directory: C:\Users\MTanaka\Documents


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         10/5/2022  12:20 PM                SOPs

```

Отлично. Теперь у нас есть основной каталог. Давайте создадим вложенные папки `Physical Sec, Cyber Sec, and Training`. Мы можем использовать ту же команду, что и в прошлый раз, или псевдоним `mkdir`. Сначала нам нужно перейти в каталог `SOPs`.

#### Создание Большего Количества Каталогов

  Работа с файлами и каталогами — PowerShell

```powershell-session
PS C:\Users\MTanaka\Documents> cd SOPs 

PS C:\Users\MTanaka\Documents\SOPs> mkdir "Physical Sec"

    Directory: C:\Users\MTanaka\Documents\SOPs


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         10/5/2022   4:30 PM                Physical Sec

PS C:\Users\MTanaka\Documents\SOPs> mkdir "Cyber Sec"

    Directory: C:\Users\MTanaka\Documents\SOPs


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         10/5/2022   4:30 PM                Cyber Sec

PS C:\Users\MTanaka\Documents\SOPs> mkdir "Training"

    Directory: C:\Users\MTanaka\Documents\SOPs


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         10/5/2022   4:31 PM                Training  

PS C:\Users\MTanaka\Documents\SOPs> Get-ChildItem 

Directory: C:\Users\MTanaka\Documents\SOPs


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        10/5/2022   9:08 AM                Cyber Sec
d-----        11/5/2022   9:09 AM                Physical Sec
d-----        11/5/2022   9:08 AM                Training

```

Теперь, когда у нас есть структура каталогов. Пора приступать к заполнению необходимых файлов. Господин Танака попросил, чтобы в каждой папке был файл в формате Markdown, например такой:

- `SOPs` > ReadMe.md
    - `Physical Sec` > черновик Physical-Sec.md
    - `Cyber Sec` > черновик Cyber-Sec.md
    - `Training` > черновик документа «Обучение сотрудников».

В каждом файле он запрашивал этот заголовок в верхней части:

- Название: Введите название документа
- Дата: x/x/202x
- Автор: MTanaka
- Версия: 0.1 (черновик)

Мы сможем быстро справиться с этой задачей, используя командлеты `New-Item` и `Add-Content` .

#### Создание файлов

  Работа с файлами и каталогами — PowerShell

```powershell-session
PS C:\htb> PS C:\Users\MTanaka\Documents\SOPs> new-Item "Readme.md" -ItemType File

    Directory: C:\Users\MTanaka\Documents\SOPs

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/10/2022   9:12 AM              0 Readme.md

PS C:\Users\MTanaka\Documents\SOPs> cd '.\Physical Sec\'
PS C:\Users\MTanaka\Documents\SOPs\Physical Sec> ls
PS C:\Users\MTanaka\Documents\SOPs\Physical Sec> new-Item "Physical-Sec-draft.md" -ItemType File

    Directory: C:\Users\MTanaka\Documents\SOPs\Physical Sec

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/10/2022   9:14 AM              0 Physical-Sec-draft.md

PS C:\Users\MTanaka\Documents\SOPs\Physical Sec> cd ..
PS C:\Users\MTanaka\Documents\SOPs> cd '.\Cyber Sec\'

PS C:\Users\MTanaka\Documents\SOPs\Cyber Sec> new-Item "Cyber-Sec-draft.md" -ItemType File

    Directory: C:\Users\MTanaka\Documents\SOPs\Cyber Sec

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/10/2022   9:14 AM              0 Cyber-Sec-draft.md

PS C:\Users\MTanaka\Documents\SOPs\Cyber Sec> cd ..
PS C:\Users\MTanaka\Documents\SOPs> cd .\Training\
PS C:\Users\MTanaka\Documents\SOPs\Training> ls
PS C:\Users\MTanaka\Documents\SOPs\Training> new-Item "Employee-Training-draft.md" -ItemType File

    Directory: C:\Users\MTanaka\Documents\SOPs\Training

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/10/2022   9:15 AM              0 Employee-Training-draft.md

PS C:\Users\MTanaka\Documents\SOPs\Training> cd ..
PS C:\Users\MTanaka\Documents\SOPs> tree /F
Folder PATH listing
Volume serial number is F684-763E
C:.
│   Readme.md
│
├───Cyber Sec
│       Cyber-Sec-draft.md
│
├───Physical Sec
│       Physical-Sec-draft.md
│
└───Training
        Employee-Training-draft.md
```

Теперь, когда у нас есть файлы, нам нужно добавить в них содержимое. Это можно сделать с помощью командлета `Add-Content` .

#### Добавление контента

  Работа с файлами и каталогами — PowerShell

```powershell-session
PS C:\htb> Add-Content .\Readme.md "Title: Insert Document Title Here
>> Date: x/x/202x
>> Author: MTanaka
>> Version: 0.1 (Draft)"  
  
PS C:\Users\MTanaka\Documents\SOPs> cat .\Readme.md
Title: Insert Document Title Here
Date: x/x/202x
Author: MTanaka
Version: 0.1 (Draft)
```

Затем мы бы повторили тот же процесс, что и для `Readme.md` в каждом другом файле, который мы создали для мистера Танаки. Этот сценарий кажется немного утомительным, не так ли? Создавать файлы вручную снова и снова может быть утомительно. Здесь на помощь приходят автоматизация и написание сценариев. Сейчас это немного выходит за рамки наших возможностей, но в следующем разделе этого модуля мы обсудим, как создать быстрый модуль PowerShell, используя переменные и написание сценариев для упрощения работы.

**Продолжение сценария: г-н Танака попросил нас изменить название файла Cyber-Sec-draft.md на Infosec-SOP-draft.md.**

Мы можем быстро справиться с этой задачей с помощью командлета `Rename-Item` . Давайте попробуем:

#### Переименование объекта

  Работа с файлами и каталогами — PowerShell

```powershell-session
PS C:\Users\MTanaka\Documents\SOPs\Cyber Sec> ls

    Directory: C:\Users\MTanaka\Documents\SOPs\Cyber Sec

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/10/2022   9:14 AM              0 Cyber-Sec-draft.md

PS C:\Users\MTanaka\Documents\SOPs\Cyber Sec> Rename-Item .\Cyber-Sec-draft.md -NewName Infosec-SOP-draft.md
PS C:\Users\MTanaka\Documents\SOPs\Cyber Sec> ls

    Directory: C:\Users\MTanaka\Documents\SOPs\Cyber Sec

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/10/2022   9:14 AM              0 Infosec-SOP-draft.md
```

Всё, что нам нужно было сделать, — это вызвать командлет `Rename-Item`, указать исходное имя файла, которое мы хотим изменить (`Cyber-Sec-draft.md`), а затем указать новое имя с помощью параметра `-NewName` (`Infosec-SOP-draft.md`). Кажется, всё просто, не так ли? Мы можем пойти дальше и переименовать все файлы в каталоге, изменить тип файла или выполнить несколько других действий. В нашем примере ниже мы изменим имена всех текстовых файлов на рабочем столе мистера Танаки с `file.txt` на `file.md`.

#### Файлы 1–5.txt находятся на рабочем столе MTanaka

  Работа с файлами и каталогами — PowerShell

```powershell-session
PS C:\Users\MTanaka\Desktop> ls

    Directory: C:\Users\MTanaka\Desktop

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        10/13/2022   1:05 PM              0 file-1.txt
-a----        10/13/2022   1:05 PM              0 file-2.txt
-a----        10/13/2022   1:06 PM              0 file-3.txt
-a----        10/13/2022   1:06 PM              0 file-4.txt
-a----        10/13/2022   1:06 PM              0 file-5.txt

PS C:\Users\MTanaka\Desktop> get-childitem -Path *.txt | rename-item -NewName {$_.name -replace ".txt",".md"}
PS C:\Users\MTanaka\Desktop> ls

    Directory: C:\Users\MTanaka\Desktop

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        10/13/2022   1:05 PM              0 file-1.md
-a----        10/13/2022   1:05 PM              0 file-2.md
-a----        10/13/2022   1:06 PM              0 file-3.md
-a----        10/13/2022   1:06 PM              0 file-4.md
-a----        10/13/2022   1:06 PM              0 file-5.md
```

Как мы можем видеть выше, у нас на рабочем столе было пять текстовых файлов. Мы изменили их на `.md` файлы, используя `get-childitem -Path *.txt` для выбора объектов, и использовали `|` для отправки этих объектов `rename-item -NewName {$_.name -replace ".txt",".md"}` командлету, который переименовывает все из его первоначального имени ($_.name) и заменяет `.txt` from name на `.md`. Это гораздо более быстрый способ взаимодействия с файлами и выполнения массовых действий. Теперь, когда мы выполнили все запросы мистера Танакаса, давайте на секунду обсудим права доступа к файлам и каталогам.

---

## Что такое права доступа к файлам и каталогам

Если говорить упрощённо, то разрешения — это способ, с помощью которого наш хост определяет, у кого есть доступ к определённому объекту и что они могут с ним делать. Эти разрешения позволяют нам применять детальный контроль безопасности для наших объектов, чтобы поддерживать надлежащий уровень безопасности. В таких организациях, как крупные компании с несколькими отделами (например, отдел кадров, ИТ-отдел, отдел продаж и т. д.), важно обеспечить доступ к информации только тем, кому она необходима. Это гарантирует, что посторонние не смогут повредить данные или использовать их не по назначению. В файловой системе Windows есть множество базовых и расширенных разрешений. Вот некоторые из основных типов разрешений:

#### Объясненные Типы Разрешений

- `Full Control`Полный контроль позволяет указанному пользователю или группе взаимодействовать с файлом по своему усмотрению. Это включает в себя все, что описано ниже, а также изменение разрешений и получение права собственности на файл.
- `Modify`: позволяет читать, записывать и удалять файлы и папки.
- `List Folder Contents`Это позволяет просматривать и перечислять папки и подпапки, а также запускать файлы. Это применимо только к `folders`.
- `Read and Execute`: позволяет пользователям просматривать содержимое файлов и запускать исполняемые файлы (.ps1, .exe, .bat и т. д.)
- `Write`Функция «Запись» позволяет пользователю создавать новые файлы и подпапки, а также добавлять содержимое в файлы.
- `Read`Позволяет просматривать и перечислять папки и подпапки, а также просматривать содержимое файла.
- `Traverse Folder`Функция Traverse позволяет предоставить пользователю доступ к файлам или подпапкам в дереве, но не к содержимому папок более высокого уровня. Это способ обеспечить выборочный доступ с точки зрения безопасности.

Windows (в целом NTFS) позволяет нам устанавливать разрешения для родительского каталога, и эти разрешения будут распространяться на каждый файл и папку, расположенные в этом каталоге. Это экономит нам массу времени по сравнению с ручной настройкой разрешений для каждого содержащегося в каталоге объекта. При необходимости это наследование можно отключить для отдельных файлов, папок и подпапок. В этом случае нам придётся вручную устанавливать нужные разрешения для затронутых файлов. Работа с разрешениями может быть сложной задачей, и её не всегда можно выполнить только с помощью интерфейса командной строки, поэтому мы оставим работу с разрешениями для `Windows Fundamentals Module`.

---

Работать с файлами и каталогами несложно, хотя иногда это может быть немного утомительно. Далее мы добавим ещё один уровень в нашу основу для командной строки и рассмотрим, как можно `find` и `filter` содержимое файлов на хосте.