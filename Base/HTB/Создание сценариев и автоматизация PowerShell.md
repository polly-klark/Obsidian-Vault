#htb #windows #powershell 

Каким бы невероятным ни был PowerShell, он хорош ровно настолько, насколько мы умеем его использовать. Большая часть языка и функций PowerShell подходит для автоматизированного использования. Возможность создавать сценарии и модули в PowerShell (независимо от того, насколько они просты или сложны) может облегчить нашу административную нагрузку или избавить нас, пентестеров, от выполнения некоторых простых задач. В этом модуле мы рассмотрим составные части сценария и модуля PowerShell. В конце мы создадим собственный простой в использовании и настраиваемый модуль.

---

## Понимание сценариев PowerShell

PowerShell по своей природе является модульным и предоставляет широкие возможности для управления. При работе со скриптами принято считать, что мы пишем исполняемый файл, который выполняет задачи на языке, на котором он был создан. В случае с PowerShell это по-прежнему верно, за исключением того, что он может обрабатывать ввод с нескольких разных языков и из файлов разных типов, а также работать с множеством различных типов объектов. Мы можем использовать отдельные скрипты обычным способом, вызывая их с помощью `.\script`-синтаксиса и импортируя модули с помощью `Import-Module`-командлета. Теперь давайте немного поговорим о скриптах и модулях.

### Скрипты против модулей

Проще всего представить, что скрипт — это исполняемый текстовый файл, содержащий командлеты и функции PowerShell, а модуль может быть как простым скриптом, так и набором из нескольких файлов скриптов, манифестов и функций, объединённых вместе. Другое важное отличие заключается в их использовании. Обычно скрипт вызывается напрямую, а модуль можно импортировать, чтобы вызывать все связанные с ним скрипты и функции по своему усмотрению. В этом разделе мы будем использовать один и тот же термин для их обозначения. Всё, о чём мы говорим в файле модуля, работает и в стандартном скрипте PowerShell. Начнём с `file extensions` и их значения.

### Расширения файлов

Чтобы ознакомиться с некоторыми расширениями файлов, с которыми мы будем сталкиваться при работе со скриптами и модулями PowerShell, мы составили небольшую таблицу с расширениями и их описаниями.

#### Расширения PowerShell

|**Расширение**|**Описание**|
|---|---|
|ps1|Файлы с расширением `*.ps1` представляют собой исполняемые сценарии PowerShell.|
|psm1|Расширение `*.psm1` указывает на файл модуля PowerShell. Оно определяет, что представляет собой модуль и что в нём содержится.|
|psd1|`*.psd1` — это файл данных PowerShell, в котором подробно описано содержимое модуля PowerShell в виде таблицы с парами «ключ-значение».|

Это основные расширения, которые нас сейчас интересуют. На самом деле модули PowerShell могут содержать множество различных сопутствующих файлов с разными расширениями, но для того, что мы пытаемся сделать, они не нужны. Если вы хотите подробнее изучить файлы сценариев PowerShell и файлы справки, прочтите этот [пост](https://learn.microsoft.com/en-us/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7.2)

---

## Создание модуля

Итак, давайте приступим. Далее мы рассмотрим компоненты модуля PowerShell, их назначение и способы создания. Этот процесс прост. Он требует лишь небольшого предварительного планирования. Рассмотрим следующий сценарий:

**Сценарий**: при администрировании хостов мы постоянно выполняем одни и те же проверки. Поэтому, чтобы ускорить выполнение задач, мы создадим модуль PowerShell, который будет выполнять проверки за нас, а затем выводить запрашиваемую нами информацию. При использовании наш модуль должен выводить данные о хосте `computer name`, `IP address`, а также базовые `domain information` и предоставлять нам данные из каталога `C:\Users\`, чтобы мы могли видеть, какие пользователи входили в систему на этом хосте.

Теперь, когда мы знаем, что входит в состав нашего модуля, пора приступать к его созданию.

---

## Компоненты модуля

Модуль состоит из `four` основных компонентов:

1. `directory` содержит все необходимые файлы и содержимое, сохранённые где-то в `$env:PSModulePath`.

- Это сделано для того, чтобы при попытке импортировать его в сеанс PowerShell или профиль его можно было найти автоматически, без необходимости указывать его местоположение.

2. Файл `manifest` со списком всех файлов и соответствующей информацией о модуле и его функциях.

- Сюда могут входить связанные скрипты, зависимости, информация об авторе, примеры использования и т. д.

3. Файл с кодом — обычно это скрипт PowerShell (`.ps1`) или файл модуля (`.psm1`), содержащий функции нашего скрипта и другую информацию.
    
4. Другие ресурсы, необходимые модулю, например файлы справки, скрипты и другие вспомогательные документы.
    

Такая настройка является стандартной, но не обязательной. Мы могли бы сделать так, чтобы наш модуль представлял собой просто `*.psm1` файл, содержащий наши скрипты и контекст, без манифеста и других вспомогательных файлов. PowerShell смог бы интерпретировать и понять, что нужно делать в обоих случаях. Для соблюдения правил мы будем работать над созданием стандартного модуля PowerShell, включая файл манифеста и некоторые встроенные функции справки.

### Создание каталога для нашего модуля

Создать каталог очень просто, как уже говорилось в предыдущих разделах. Прежде чем двигаться дальше, нам нужно создать каталог для нашего модуля. Этот каталог должен находиться по одному из путей в `$env:PSModulePath`. Если вы не знаете, что это за пути, вы можете вызвать переменную, чтобы узнать, где лучше всего его разместить. Итак, мы создадим папку с именем `quick-recon`.

#### Mkdir

  Создание сценариев и автоматизация PowerShell

```powershell-session
PS C:\htb> mkdir quick-recon  

    Directory: C:\Users\MTanaka\Documents\WindowsPowerShell\Modules


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        10/31/2022   7:38 AM                quick-recon
```

Теперь, когда у нас есть каталог, мы можем создать модуль. Давайте на секунду поговорим о файле `module manifest`

### Манифест модуля

Манифест модуля — это простой `.psd1` файл, содержащий хэш-таблицу. Ключи и значения в хэш-таблице выполняют следующие функции:

- Опишите `contents` и `attributes` модуля.
- Определите `prerequisites`. (конкретные модули, не входящие в состав модуля, переменные, функции и т. д.)
- Определите, как `components` соотносятся с `processed`.

Если вы добавляете файл манифеста в папку модуля, вы можете ссылаться на несколько файлов как на единое целое, ссылаясь на манифест. В `manifest` описывается следующая информация:

- `Metadata` о модуле, например номер версии модуля, имя автора и описание.
- `Prerequisites` Для импорта модуля необходимы такие данные, как версия Windows PowerShell, версия среды выполнения общего языка (CLR) и требуемые модули.
- `Processing` директивы, такие как сценарии, форматы и типы обработки.
- `Restrictions` о том, какие элементы модуля следует экспортировать, например псевдонимы, функции, переменные и командлеты.

Мы можем быстро создать файл манифеста, используя `New-ModuleManifest` и указав место, куда мы хотим его поместить.

#### New-ModuleManifest

  Создание сценариев и автоматизация PowerShell

```powershell-session
PS C:\htb> New-ModuleManifest -Path C:\Users\MTanaka\Documents\WindowsPowerShell\Modules\quick-recon\quick-recon.psd1 -PassThru

# Module manifest for module 'quick-recon'
#
# Generated by: MTanaka
#
# Generated on: 10/31/2022
#

@{

# Script module or binary module file associated with this manifest.
# RootModule = ''

# Version number of this module.
ModuleVersion = '1.0'

<SNIP>
```

Выполнив приведённую выше команду, мы подготовили файл манифеста `new`, заполненный данными по умолчанию. Модификатор `-PassThru` позволяет нам видеть, что выводится в файл и на консоль. Теперь мы можем заполнить нужные разделы соответствующей информацией. Помните, что все строки в файлах манифеста необязательны, кроме строки `ModuleVersion` . Проще всего редактировать манифест в графическом интерфейсе, где можно использовать текстовый редактор или IDE, например VSCode. Если бы мы сейчас заполнили файл манифеста для этого модуля, он выглядел бы примерно так:

#### Примерный Манифест

Код: powershell

```powershell
# Module manifest for module 'quick-recon'
#
# Generated by: MTanaka
#
# Generated on: 10/31/2022
#

@{

# Script module or binary module file associated with this manifest.
# RootModule = 'C:\Users\MTanaka\WindowsPowerShell\Modules\quick-recon\quick-recon.psm1'

# Version number of this module.
ModuleVersion = '1.0'

# ID used to uniquely identify this module
GUID = '0a062bb1-8a1b-4bdb-86ed-5adbe1071d2f'

# Author of this module
Author = 'MTanaka'

# Company or vendor of this module
CompanyName = 'Greenhorn.Corp.'

# Copyright statement for this module
Copyright = '(c) 2022 Greenhorn.Corp. All rights reserved.'

# Description of the functionality provided by this module
Description = 'This module will perform several quick checks against the host for Reconnaissance of key information.'

# Functions to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no functions to export.
FunctionsToExport = @()

# Cmdlets to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no cmdlets to export.
CmdletsToExport = @()

# Variables to export from this module
VariablesToExport = '*'

# Aliases to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no aliases to export.
AliasesToExport = @()

# List of all modules packaged with this module
# ModuleList = @()

# List of all files packaged with this module
# FileList = @()  
}
```

Мы можем вернуться к манифесту позже и добавить `functions, cmdlets, and variables` для экспорта. Сначала нам нужно собрать и завершить работу над скриптом.

### Создайте Наш файл скрипта

Для создания файла мы можем использовать командлет `New-Item` (ni).

#### New-Item

  Создание сценариев и автоматизация PowerShell

```powershell-session
PS C:\htb>  ni quick-recon.psm1 -ItemType File


    Directory: C:\Users\MTanaka\Documents\WindowsPowerShell\Modules\quick-recon


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        10/31/2022   9:07 AM              0 quick-recon.psm1
```

Довольно просто, не так ли? Теперь заполним этого монстра.

### Импорт необходимых вам модулей

Если для корректной работы нашего нового PowerShell требуются другие модули или командлеты из них, мы поместим строку `Import-Module` в начало файла сценария. Использование Import-Module таким образом работает почти так же, как если бы мы вызывали его из оболочки: он вызывает и загружает нужные нам модули перед выполнением сценария. Для достижения целей, которые мы ставим перед этим модулем, многие командлеты и функции уже встроены в PowerShell. Однако нам нужен один командлет из модуля ActiveDirectory PowerShell. Итак, давайте добавим строку импорта для модуля `ActiveDirectory`.

#### Импорт В Наш модуль

Код: powershell

```powershell
Import-Module ActiveDirectory 
```

Довольно просто, не так ли? Теперь у нас есть файл сценария модуля `quick-recon.psm1`, и мы добавили в него оператор `import-module`. Теперь мы можем перейти к основной части файла — нашему `functions`.

### Функции и работа с Powershell

С помощью этого модуля нам нужно сделать четыре основные вещи:

- Извлеките имя хост-компьютера
- Извлеките конфигурацию IP-адреса хостов
- Получение базовой информации о домене
- Получите выходные данные из каталога «C:\Users»

Для начала давайте сосредоточимся на выводе ComputerName. Это можно сделать разными способами с помощью различных командлетов, модулей и команд DOS. Наш скрипт будет использовать переменную среды (`$env:ComputerName`) для получения имени хоста в выводе. Чтобы упростить чтение вывода, мы будем использовать другую переменную с именем `$hostname` для хранения вывода из переменной среды. Чтобы получить IP-адрес активных адаптеров хоста, мы будем использовать `IPConfig` и сохранять эту информацию в переменной `$IP`. Для получения базовой информации о домене мы будем использовать `Get-ADDomain` и сохранять вывод в `$Domain`. Наконец, мы получим список пользовательских папок в C:\Users\ с помощью `Get-ChildItem` и сохраним его в `$Users`. Чтобы создать переменные, нужно сначала указать имя (`$Hostname`), добавить символ «=», а затем указать действие или значения, которые мы хотим сохранить. Например, первая переменная, которая нам нужна, `$Hostname`, будет выглядеть так: (`$Hostname = $env:ComputerName`). Теперь давайте создадим остальные переменные для использования.

#### Переменные

Код: powershell

```powershell
Import-Module ActiveDirectory 

$Hostname = $env:ComputerName
$IP = ipconfig 
$Domain = Get-ADDomain  
$Users = Get-ChildItem C:\Users\ 
  
```

Теперь наши переменные настроены на выполнение отдельных команд или функций с получением необходимого результата. Теперь давайте отформатируем эти данные и получим более приятный результат. Мы можем сделать это, записав результат в `file` с помощью `New-Item` и `Add-Content`. Чтобы упростить задачу, мы превратим этот процесс вывода в вызываемую функцию под названием `Get-Recon`.

#### Вывод Нашей Информации

Код: powershell

```powershell
Import-Module ActiveDirectory

function Get-Recon {  

    $Hostname = $env:ComputerName  

    $IP = ipconfig

    $Domain = Get-ADDomain 

    $Users = Get-ChildItem C:\Users\

    new-Item ~\Desktop\recon.txt -ItemType File 

    $Vars = "***---Hostname info---***", $Hostname, "***---Domain Info---***", $Domain, "***---IP INFO---***",  $IP, "***---USERS---***", $Users

    Add-Content ~\Desktop\recon.txt $Vars
  } 
```

`New-Item` Сначала он создает для нас выходной файл, а затем обратите внимание, как мы использовали еще одну переменную (`$Vars`) для форматирования вывода. Мы вызываем каждую переменную и вставляем между ними описательную строку. Наконец, командлет `Add-Content` добавляет собранные нами данные в файл recon.txt, записывая результаты $Vars. Наша функция готова. Далее нам нужно добавить в файл несколько комментариев, чтобы другие могли понять, чего мы пытаемся добиться и почему мы сделали это именно так.

### Комментарии внутри скрипта

(`#`) сообщает PowerShell, что строка содержит комментарий в файле скрипта или модуля. Если комментарий занимает несколько строк, можно использовать `<#` и `#>` для объединения нескольких строк в один большой комментарий, как показано ниже:

#### Блоки комментариев

Код: powershell

```powershell

# This is a single-line comment.  

<# This line and the following lines are all wrapped in the Comment specifier. 
Nothing with this window will be ready by the script as part of a function.
This text exists purely for the creator and us to convey pertinent information.

#>  
```

#### Добавлены комментарии

Код: powershell

```powershell
Import-Module ActiveDirectory

function Get-Recon {  
    # Collect the hostname of our PC.
    $Hostname = $env:ComputerName  
    # Collect the IP configuration.
    $IP = ipconfig
    # Collect basic domain information.
    $Domain = Get-ADDomain 
    # Output the users who have logged in and built out a basic directory structure in "C:\Users\".
    $Users = Get-ChildItem C:\Users\
    # Create a new file to place our recon results in.
    new-Item ~\Desktop\recon.txt -ItemType File 
    # A variable to hold the results of our other variables. 
    $Vars = "***---Hostname info---***", $Hostname, "***---Domain Info---***", $Domain, "***---IP INFO---***",  $IP, "***---USERS---***", $Users
    # It does the thing 
    Add-Content ~\Desktop\recon.txt $Vars
  } 
```

Всё очень просто. Никаких безумных комментариев. Теперь нам нужно добавить немного `help` синтаксиса, чтобы другие могли понять, как использовать наш модуль.

### Добавляем Помощь

В PowerShell используется конструкция `Comment-based help` для встраивания всего необходимого для скрипта или модуля. Мы можем использовать `comment blocks` так же, как мы обсуждали выше, а также распознаваемые `keywords` для создания раздела справки и последующего вызова с помощью `Get-Help` . Что касается размещения, у нас есть `two` варианты. Мы можем разместить справку внутри самой функции или за её пределами в скрипте. Если мы хотим разместить его внутри функции, он должен находиться в начале функции, сразу после строки, открывающей функцию, или в конце функции, через одну строку после последнего действия функции. Если мы размещаем его в скрипте, но вне самой функции, он должен находиться над функцией, и между ним и функцией должно быть не более одной строки. Чтобы подробнее узнать о справке в PowerShell, прочтите эту [статью](https://learn.microsoft.com/en-us/powershell/scripting/developer/help/writing-help-for-windows-powershell-scripts-and-functions?view=powershell-7.2). Теперь давайте определим раздел справки. Пока что мы разместим его вне функции в верхней части скрипта.

#### Справка по модулю

Код: powershell

```powershell
Import-Module ActiveDirectory

<# 
.Description  
This function performs some simple recon tasks for the user. We import the module and issue the 'Get-Recon' command to retrieve our output. Each variable and line within the function and script are commented for our understanding. Right now, this module will only work on the local host from which you run it, and the output will be sent to a file named 'recon.txt' on the Desktop of the user who opened the shell. Remote Recon functions are coming soon!  

.Example  
After importing the module run "Get-Recon"
'Get-Recon


    Directory: C:\Users\MTanaka\Desktop


Mode                 LastWriteTime         Length Name                                                                                                                                        
----                 -------------         ------ ----                                                                                                                                        
-a----         11/3/2022  12:46 PM              0 recon.txt '

.Notes  
Remote Recon functions coming soon! This script serves as our initial introduction to writing functions and scripts and making PowerShell modules.  

#>

function Get-Recon {  
<SNIP>  
```

Обратите внимание на использование `keywords`. Чтобы указать ключевое слово в блоке комментариев, мы используем синтаксис `.<keyword>` и размещаем под ним текст с описанием. Мы указали только `Description, Example, and Notes`, но в блоке справки можно разместить ещё несколько ключевых слов. Чтобы увидеть все доступные ключевые слова, обратитесь к этой статье о [ключевых словах справки на основе комментариев](https://learn.microsoft.com/en-us/powershell/scripting/developer/help/comment-based-help-keywords?view=powershell-7.2). Прежде чем собрать всё в красивый файл модуля PowerShell, давайте обсудим экспорт и защиту функций.

### Защитные функции

Мы можем добавлять в наши скрипты функции, к которым мы не хотим предоставлять доступ, которые мы не хотим экспортировать или использовать в других скриптах или процессах в PowerShell. Чтобы защитить функцию от экспорта или явно указать, что она должна быть экспортирована, используется `Export-ModuleMember` — командлет для задания. Содержимое можно экспортировать, если не указывать это в модулях наших скриптов. Если мы укажем это в файле, но оставим поле пустым, получится следующее:

#### Исключить из экспорта

Код: powershell

```powershell
Export-ModuleMember  
```

Это гарантирует, что переменные, псевдонимы и функции модуля не могут быть `exported`. Если мы хотим указать, что нужно экспортировать, мы можем добавить это в командную строку следующим образом:

#### Экспортируйте определенные функции и переменные

Код: powershell

```powershell
Export-ModuleMember -Function Get-Recon -Variable Hostname 
```

В качестве альтернативы, если вы хотите экспортировать только все функции и, например, определённую переменную, вы можете указать `*` после -Function, а затем явно указать переменные для экспорта. Итак, давайте добавим в наш скрипт командлет `Export-ModuleMember` и укажем, что мы хотим разрешить экспорт нашей функции `Get-Recon` и нашей переменной `Hostname`.

#### Добавление строки экспорта

Код: powershell

```powershell
<SNIP>  
function Get-Recon {  
    # Collect the hostname of our PC
    $Hostname = $env:ComputerName  
    # Collect the IP configuration
    $IP = ipconfig
    # Collect basic domain information
    $Domain = Get-ADDomain 
    # Output the users who have logged in and built out a basic directory structure in "C:\Users"
    $Users = Get-ChildItem C:\Users\
    # Create a new file to place our recon results in
    new-Item ~\Desktop\recon.txt -ItemType File 
    # A variable to hold the results of our other variables 
    $Vars = "***---Hostname info---***", $Hostname, "***---Domain Info---***", $Domain, "***---IP INFO---***",  $IP, "***---USERS---***", $Users
    # It does the thing 
    Add-Content ~\Desktop\recon.txt $Vars
  } 

Export-ModuleMember -Function Get-Recon -Variable Hostname  
```

### Область применения

При работе со скриптами, сеансом PowerShell и распознаванием данных в командной строке в игру вступает понятие области видимости. Область видимости — это, по сути, способ, с помощью которого PowerShell распознаёт объекты в сеансе и защищает их от несанкционированного доступа или изменения. В настоящее время PowerShell использует `three` различные уровни области видимости:

#### Уровни охвата

|**Область применения**|**Описание**|
|---|---|
|Глобальный|Это уровень области видимости по умолчанию для PowerShell. Он влияет на все объекты, которые существуют на момент запуска PowerShell или открытия нового сеанса. Любые переменные, псевдонимы, функции и всё остальное, что вы указываете в своём профиле PowerShell, будет создано в глобальной области видимости.|
|Местные новости|Это текущая область, в которой вы работаете. Это может быть любая из областей по умолчанию или созданных дочерних областей.|
|Сценарий|Это временная область видимости, которая применяется ко всем запускаемым скриптам. Она применяется только к скрипту и его содержимому. Другие скрипты и всё, что находится за её пределами, не будут знать о её существовании. Для скрипта область видимости является локальной.|

Это важно для нас, если мы не хотим, чтобы что-то за пределами области видимости, в которой выполняется скрипт, имело доступ к его содержимому. Кроме того, в основных областях видимости могут создаваться дочерние области видимости. Например, при запуске скрипта создаётся область видимости скрипта, а затем любая вызываемая функция может создать дочернюю область видимости, охватывающую эту функцию и её переменные. Если мы хотим, чтобы содержимое этой конкретной функции не было доступно остальной части скрипта или самому сеансу PowerShell, мы можем изменить область её видимости. Это сложная тема, которая в настоящее время выходит за рамки данного модуля, но мы решили, что об этом стоит упомянуть. Подробнее о области видимости в PowerShell можно узнать из документации [здесь](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_scopes?view=powershell-7.2).

### Собирая Все Это Воедино

Теперь, когда мы разобрались с фрагментами и частями, давайте посмотрим на всё это вместе.

#### Конечный продукт

Код: powershell

```powershell
import-module ActiveDirectory

<# 
.Description  
This function performs some simple recon tasks for the user. We import the module and then issue the 'Get-Recon' command to retrieve our output. Each variable and line within the function and script are commented for your understanding. Right now, this only works on the local host from which you run it, and the output will be sent to a file named 'recon.txt' on the Desktop of the user who opened the shell. Remote Recon functions coming soon!  

.Example  
After importing the module run "Get-Recon"
'Get-Recon


    Directory: C:\Users\MTanaka\Desktop


Mode                 LastWriteTime         Length Name                                                                                                                                        
----                 -------------         ------ ----                                                                                                                                        
-a----         11/3/2022  12:46 PM              0 recon.txt '

.Notes  
Remote Recon functions coming soon! This script serves as our initial introduction to writing functions and scripts and making PowerShell modules.  

#>
function Get-Recon {  
    # Collect the hostname of our PC
    $Hostname = $env:ComputerName  
    # Collect the IP configuration
    $IP = ipconfig
    # Collect basic domain information
    $Domain = Get-ADDomain 
    # Output the users who have logged in and built out a basic directory structure in "C:\Users"
    $Users = Get-ChildItem C:\Users\
    # Create a new file to place our recon results in
    new-Item ~\Desktop\recon.txt -ItemType File 
    # A variable to hold the results of our other variables 
    $Vars = "***---Hostname info---***", $Hostname, "***---Domain Info---***", $Domain, "***---IP INFO---***",  $IP, "***---USERS---***", $Users
    # It does the thing 
    Add-Content ~\Desktop\recon.txt $Vars
  } 

Export-ModuleMember -Function Get-Recon -Variable Hostname 
```

Вот и всё, у нас есть полный файл модуля. Использование комментариев, функций, переменных и защиты контента позволяет создать динамичный и понятный скрипт. Теперь мы можем сохранить этот файл в созданном нами каталоге модулей и импортировать его в PowerShell для использования.

#### Импорт модуля для использования

  Создание сценариев и автоматизация PowerShell

```powershell-session
PS C:\htb> Import-Module 'C:\Users\MTanaka\Documents\WindowsPowerShell\Modules\quick-recon.psm1`

PS C:\Users\MTanaka\Documents\WindowsPowerShell\Modules\quick-recon> get-module

ModuleType Version    Name                                ExportedCommands
---------- -------    ----                                ----------------
Manifest   3.1.0.0    Microsoft.PowerShell.Management     {Add-Computer, Add-Content, Checkpoint-Computer, Clear-Con...
Script     2.0.0      PSReadline                          {Get-PSReadLineKeyHandler, Get-PSReadLineOption, Remove-PS...
Script     0.0        quick-recon                         Get-Recon
```

Отлично. Мы видим, что наш модуль был импортирован с помощью `Import-Module` командлета, и, чтобы убедиться, что он загружен в нашу сессию, мы запустили `Get-Module` командлет. Он показал нам, что наш модуль `quick-recon` был импортирован и содержит команду `Get-Recon`, которую можно экспортировать. Мы также можем протестировать справку на основе комментариев, попытавшись запустить `Get-Help` для нашего модуля.

#### Проверка справки

  Создание сценариев и автоматизация PowerShell

```powershell-session
PS C:\htb> get-help get-recon

NAME
    Get-Recon

SYNOPSIS


SYNTAX
    Get-Recon [<CommonParameters>]


DESCRIPTION
    This function performs some simple recon tasks for the user. We simply import the module and then issue the
    'Get-Recon' command to retrieve our output. Each variable and line within the function and script are commented for
    your understanding. Right now, this only works on the local host from which you run it, and the output will be sent
    to a file named 'recon.txt' on the Desktop of the user who opened the shell. Remote Recon functions coming soon!


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-Recon -examples."
    For more information, type: "get-help Get-Recon -detailed."
    For technical information, type: "get-help Get-Recon -full."
```

Наша помощь тоже работает. Теперь у нас есть полностью функционирующий модуль. Мы можем использовать его как основу для всего, что будем создавать в дальнейшем, и даже модифицировать его, чтобы в будущем он выполнял больше функций разведки.

---

Это был простой пример того, что можно сделать с помощью PowerShell с точки зрения автоматизации, но он отлично демонстрирует, как это работает и как это можно использовать. Мы можем использовать создание модулей и написание сценариев в своих интересах и упрощать наши процессы по мере необходимости. Экономия времени в конечном счёте позволяет нам, операторам, делать больше и уделять время другим задачам, требующим нашего внимания. Если вам нужна копия модуля quick-recon, она сохранена в `Resources` этого модуля в правом верхнем углу любой страницы раздела.