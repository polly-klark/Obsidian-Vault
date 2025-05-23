#linux #ports #TCP #UDP 

С точки зрения программного обеспечения, особенно на уровне операционной системы, порт – это логическая конструкция, которая идентифицирует конкретный процесс / приложение или тип сетевой службы, и каждая сетевая служба, работающая в системе Linux, использует определенный протокол (наиболее распространенным из которых является TCP (**Transmission Control Protocol**) и UDP (**User Datagram Protoco**я)) и номер порта для связи с другими процессами или службами.

В этой короткой статье мы покажем вам, как составлять список и отслеживать или наблюдать за работой портов TCP и UDP в режиме реального времени с помощью сводки сокетов в системе Linux.

# Список всех открытых портов в Linux

Чтобы вывести список всех открытых портов в системе Linux, вы можете использовать команду netstat или утилиту ss следующим образом.

> Также важно упомянуть, что команда netstat устарела, и вместо нее команда ss заняла свое место для более подробной статистики сети.

```
$ sudo netstat -tulpn
```

или

```
$ sudo ss -tulpn
```

![[Pasted image 20230107230446.png]]

Из вывода вышеприведенной команды в столбце «**State**» показано, находится ли порт в состоянии прослушивания (LISTEN) или нет.

В приведенной выше команде флаги:

-   `-t` –включает в себя список портов TCP.
-   `-u` –позволяет выводить список портов UDP.
-   `-l` – выводит только прослушивающие сокеты.
-   `-n` – показывает номер порта
-   `-p` – показывает имя процесса / программы.

# Наблюдайте за открытыми портами TCP и UDP в режиме реального времени

Однако для просмотра портов TCP и UDP в режиме реального времени вы можете запустить утилиту netstat или ss с помощью утилиты watch , как показано на рисунке.

```
$ sudo watch netstat -tulpn
```

или

```
$ sudo watch ss -tulpn
```

![[Pasted image 20230107230541.png]]

Для выхода нажмите Ctrl + C.