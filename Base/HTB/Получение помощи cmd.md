#windows #cmd #htb 

В предыдущем разделе мы познакомились с общими понятиями о командной строке и о том, как мы можем получить к ней доступ. Этот раздел расширит предыдущий, представив функциональность `help` в командной строке, примеры вывода и некоторые дополнительные ресурсы и концепции.

В командную строку встроена функция `help`, которая может предоставить нам подробную информацию о доступных командах в нашей системе и о том, как использовать эти функции. В этом разделе мы рассмотрим следующее более подробно:

- Как мы используем функциональность справки в командной строке?
- Почему важно использовать функциональность справки?
- Где мы можем найти дополнительные внешние ресурсы для получения справки?
- Как использовать дополнительные советы и рекомендации в командной строке?

---

## Как получить помощь

При первом знакомстве с интерфейсом командной строки может показаться, что она пуста. Могут возникнуть некоторые первоначальные вопросы, например:

- К каким командам у меня есть доступ?
- Как я могу использовать эти команды?

Давайте сначала ответим на первоначальный вопрос. При использовании командной строки найти справку так же просто, как набрать `help`. Без каких-либо дополнительных параметров эта команда предоставляет список встроенных команд и основную информацию об использовании каждой отображаемой команды. Давайте рассмотрим это ниже.

#### Использование справки по умолчанию

Получение помощи

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

<snip>
```

Из этого вывода мы можем видеть, что он выводит список системных команд (`built-ins`) и предоставляет базовое описание своей функциональности. Это важно, потому что мы можем быстро и эффективно проанализировать список встроенных функций, предоставляемых командной строкой, чтобы найти функцию, которая соответствует нашим потребностям. Отсюда мы можем перейти к ответу на второй вопрос о том, как используются эти команды. Чтобы распечатать подробную информацию о конкретной команде, мы можем ввести следующее: `help <command name>`.

#### Справка по командам

Получение помощи

```cmd-session
C:\htb> help time

Displays or sets the system time.

TIME [/T | time]

Type TIME with no parameters to display the current time setting and a prompt
for a new one. Press ENTER to keep the same time.

If Command Extensions are enabled, the TIME command supports
the /T switch which tells the command to just output the
current time, without prompting for a new time.
```

Как мы можем видеть из приведенного выше вывода, когда мы вводили команду `help time`, она выводила сведения о справке по time. Это будет работать для любой встроенной системной команды, но не для каждой команды, доступной в системе. Для некоторых команд нет страницы справки. Однако они перенаправят вас на выполнение соответствующей команды для получения нужной информации. Например, запуск `help ipconfig` приведет к следующему результату.

#### Подробный вывод

Получение помощи

```cmd-session
C:\htb> help ipconfig

This command is not supported by the help utility. Try "ipconfig /?".
```

В предыдущем примере функция "Справка" сообщила нам, что не может предоставить дополнительную информацию, поскольку утилита "Справка" напрямую не поддерживает ее. Однако использование предложенной команды "ipconfig /?" предоставит нам информацию, необходимую для правильного использования команды. Имейте в виду, что некоторые команды используют модификатор `/?` как взаимозаменяемый с help.

---

## Зачем нам нужна утилита "Справка"?

В предыдущем разделе мы обсудили основные аспекты использования функции "справка" из командной строки и интерпретацию некоторых ее выходных данных. Хотя понимание технических деталей использования функции `help` важно, другая фундаментальная концепция заключается в следующем:

`Why does the help utility exist, and what use does it serve today when access to the Internet is so prevalent?`

Этот вопрос многогранен, поэтому давайте разберем его по частям. Чтобы лучше ответить на этот вопрос и дать более полное объяснение, давайте начнем со следующего сценария:

**Пример:** Представьте, что вам поручено помочь `GreenHorn` вашей компании во внутреннем взаимодействии на месте. Вы сразу же попадаете в сеанс командной строки на компьютере, подключенном к внутренней сети, и вам поручено выполнить проверку системы. Согласно правилам взаимодействия, у вас отобрали все имеющиеся при себе устройства и сообщили, что брандмауэр блокирует весь исходящий сетевой трафик. Вы начинаете перечисление в системе, но вам нужна помощь, чтобы вспомнить синтаксис конкретной команды, которую вы имеете в виду. Вы понимаете, что никак не можете выйти в Интернет. `Where can you find it?`

Хотя этот сценарий может показаться несколько преувеличенным, возможны сценарии, подобные этому, в качестве `attacker`, когда наш доступ к сети будет сильно ограничен, контролироваться или строго недоступен. Иногда мы запоминаем не все команды, а также не все параметры и синтаксис; однако от нас все равно ожидают, что мы будем работать даже при таких ограничениях. В тех случаях, когда от нас ожидают результатов, нам понадобятся альтернативные способы сбора необходимой информации, вместо того чтобы полагаться на Интернет в качестве быстрого решения наших проблем. Теперь, когда у нас есть наш сценарий, давайте оглянемся назад и разберем наш первоначальный вопрос:

`Why does the help utility exist?`

Утилита `help` служит в качестве `offline` руководства для команд операционной системы Windows, совместимых с `CMD` и `DOS`. `Offline` означает, что эту утилиту можно использовать в системе без доступа к сети. Для тех, кто знаком с [Основами Linux](https://academy.hackthebox.com/module/18/section/67 ), эта утилита очень похожа на справочные страницы в системах на базе Linux. Теперь, когда мы понимаем, для чего существует справочная утилита, мы можем ответить на вторую часть первоначального вопроса:

`What use does it serve today when access to the Internet is so prevalent?`

Как показано в нашем сценарии, иногда у нас может не быть прямого доступа к Интернету. Утилита `help` предназначена для устранения этого пробела, когда нам нужна помощь с командами или конкретным синтаксисом для этих команд в нашей системе, и у нас может не быть внешних ресурсов, доступных для обращения за помощью. Это не означает, что `Internet` не является ценным инструментом для проведения деловых встреч. Однако, если мы не можем позволить себе роскошь поиска ответов на наши вопросы, нам нужен какой-то способ получения указанной информации.

---

## Где Вы можете получить Дополнительную Помощь?

В предыдущем разделе мы обсуждали важность использования справочной системы, встроенной в командную строку, особенно в среде, где внешний сетевой трафик отсутствует или ограничен. Однако, если предположить, что у нас есть доступ к Интернету, в нашем распоряжении есть десятки `online resources` для получения дополнительной информации о командной строке. Как указывалось ранее, Интернет является чрезвычайно ценным инструментом, и его следует использовать в полной мере, особенно при наличии неограниченного доступа. Чтобы помочь нам лучше понять CMD и сократить время, затрачиваемое на поиск материала, вот несколько ссылок на команды `CMD.exe`, из которых мы можем узнать больше о том, что можно сделать с помощью нашей командной оболочки.

[Microsoft Documentation](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands) содержит полный список команд, которые можно использовать в интерпретаторе командной строки, а также подробные описания того, как их использовать. Рассматривайте это как онлайн-версию справочных страниц.

[ss64](https://ss64.com/nt/) Это удобный краткий справочник по всему, что связано с командной строкой, включая cmd, PowerShell, Bash и многое другое.

Это неполный список ресурсов, однако они должны обеспечить хорошую основу для работы с командной строкой.

---

## Основные советы и рекомендации

Теперь, когда у нас есть общее представление о том, как мы можем получить помощь от внешних ресурсов, давайте закончим с некоторыми важными советами и рекомендациями по взаимодействию с командной строкой.

#### Очистите экран

Во время нашего взаимодействия с `command prompt` бывают моменты, когда объем `output`, предоставляемых нам с помощью множества команд, переполняет экран и становится непригодной для использования информацией. В этом случае нам нужен какой-то способ `clear` экран и предоставить нам пустую строку запроса. Мы можем использовать команду `cls`, чтобы очистить окно нашего терминала от предыдущих результатов. Это удобно, когда нам нужно обновить экран и мы хотим избежать проблем с чтением терминала и выяснением, где начинается наш текущий вывод и заканчивается старый ввод.

![GIF showcasing the usage of the 'cls' command in a command prompt terminal.](https://academy.hackthebox.com/storage/modules/167/clear_screen.gif)

Из приведенного выше рисунка видно, что наш терминал был загружен, и мы запустили команду `cls`, предоставив нам `blank slate`.

#### История

Ранее мы расширили возможности очистки выходных данных из сеанса командной строки с помощью `cls`. Несмотря на то, что эта информация была удалена из вывода на экран, мы все равно можем восстановить команды, которые выполнялись до этого момента. Это происходит благодаря отличной функции, встроенной в командную строку, известной как `Command History`.

История команд динамична. Она позволяет нам `view previously ran commands` в `current active session` нашей командной строки. Для этого CMD предоставляет нам несколько различных методов взаимодействия с нашей историей команд. Например, мы можем использовать клавиши со стрелками для перемещения вверх и вниз по нашей истории, клавиши `page up` и `page down`, а при работе на физическом компьютере Windows вы можете использовать `function` клавиши для взаимодействия с историей ваших сеансов. Последний способ, которым мы можем просмотреть нашу историю, - это использовать команду `doskey /history`. [Doskey](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/doskey) - это утилита для MS-DOS, которая хранит историю выполненных команд и позволяет повторно обращаться к ним.

#### doskey /history

Получение помощи

```cmd-session
C:\htb> doskey /history

systeminfo
ipconfig /all
cls
ipconfig /all
systeminfo
cls
history
help
doskey /history
ping 8.8.8.8
doskey /history

```

Из приведенных выше выходных данных мы можем просмотреть список `commands`, которые были запущены до нашей первоначальной команды. Это важно и невероятно полезно, особенно если вы постоянно очищаете экран и вам нужно повторно запустить предыдущую команду, чтобы получить ее `output`. Взаимодействие и просмотр всех ранее выполненных команд сэкономит вам дополнительное время, энергию и душевные страдания.

#### Полезные ключи и команды для просмотра истории терминала

Было бы полезно запомнить некоторые ключевые функции, предоставляемые нашей историей терминала. Имея это в виду, в таблице ниже представлен список некоторых наиболее полезных функций и команд, которые могут быть запущены для взаимодействия с нашей историей сеансов. Этот список не является исчерпывающим. Например, все функциональные клавиши F1 - F9 служат определенной цели при работе с историей.

| **Key/Command** | **Description**                                                                                                            |
| :-------------: | -------------------------------------------------------------------------------------------------------------------------- |
| doskey /history | doskey /history распечатает историю команд сеанса в терминале или выведет ее в файл, если указано иное.                    |
|     page up     | Помещает в приглашение первую команду в истории наших сеансов.                                                             |
|    page down    | Помещает в командную строку последнюю команду в истории.                                                                   |
|        ⇧        | Позволяет нам прокручивать историю наших команд вверх, чтобы просмотреть ранее выполненные команды.                        |
|        ⇩        | Позволяет нам прокрутить вниз до самых последних запущенных команд.                                                        |
|        ⇨        | Вводит предыдущую команду, чтобы выводить запрос по одному символу за раз.                                                 |
|        ⇦        | N/A                                                                                                                        |
|       F3        | Перепишет всю предыдущую запись в нашей строке.                                                                            |
|       F5        | Многократное нажатие клавиши F5 позволит вам выполнить циклический переход к предыдущим командам.                          |
|       F7        | Открывает интерактивный список предыдущих команд.                                                                          |
|       F9        | Вводит команду в нашу командную строку, основываясь на указанном номере. Номер соответствует месту команд в нашей истории. |

Следует помнить, что в отличие от Bash или других оболочек, CMD не ведет постоянную запись команд, которые вы выполняете во время сеансов. Поэтому, как только вы закрываете этот экземпляр, эта история исчезает. Чтобы сохранить копию наших команд, мы можем снова использовать `doskey`, чтобы вывести историю в файл, отобразить ее на экране и затем скопировать.

#### Завершить запущенный процесс

На определенном этапе нашей работы с `Command Prompt` будут моменты, когда нам понадобится возможность `interrupt` активно запущенный процесс, фактически останавливая его. Это может быть вызвано множеством различных факторов. Однако в большинстве случаев мы можем получить необходимую информацию из запущенной в данный момент команды или столкнуться с неожиданной блокировкой приложения. Таким образом, нам нужен какой-то способ прервать нашу текущую сессию и любой запущенный в ней процесс. Возьмем в качестве примера следующее:

Получение помощи

```cmd-session
C:\htb> ping 8.8.8.8

Pinging 8.8.8.8 with 32 bytes of data:
Reply from 8.8.8.8: bytes=32 time=22ms TTL=114
Reply from 8.8.8.8: bytes=32 time=25ms TTL=114

Ping statistics for 8.8.8.8:
    Packets: Sent = 2, Received = 2, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 22ms, Maximum = 25ms, Average = 23ms
Control-C
^C

```

При запуске команды или процесса, который мы хотим прервать, мы можем сделать это, нажав комбинацию клавиш `ctrl+c`. Как указывалось ранее, это полезно для остановки текущего процесса, который может не отвечать на запросы, или просто для того, что мы хотим завершить немедленно. Помните, что все, что было запущено, будет незавершенным, и может потребоваться больше времени, чтобы завершить работу должным образом, поэтому всегда будьте осторожны с тем, что вы прерываете.

Теперь, когда мы поняли, как использовать командную строку и ее основные функции справки, давайте продолжим двигаться вперед и рассмотрим, как мы можем начать навигацию по нашей системе с помощью командной строки.