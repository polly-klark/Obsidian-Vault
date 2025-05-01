#linux 

Команда **lsattr** в Linux используется для отображения расширенных атрибутов файлов и директорий в файловых системах, таких как Ext4, Btrfs и XFS. Эти атрибуты регулируют особенности работы с файлами, например, их неизменяемость, возможность только добавления данных и другие.

## Основные атрибуты файлов (буквенные обозначения в выводе lsattr)

- **a** - только добавление (append only)
    
- **A** - не обновлять время последнего доступа (no atime updates)
    
- **c** - сжатый файл (compressed)
    
- **C** - без копирования при записи (copy-on-write отключен)
    
- **d** - не включать в дамп (no dump)
    
- **D** - синхронные обновления каталогов (synchronous directory updates)
    
- **e** - расширенный формат (extent format)
    
- **i** - неизменяемый файл (immutable)
    
- **j** - журналирование данных (data journaling)
    
- **P** - иерархия проектов (project hierarchy)
    
- **s** - безопасное удаление (secure deletion)
    
- **S** - синхронные обновления (synchronous updates)
    
- **t** - без объединения (no tail-merging)
    
- **T** - корневой каталог (top of directory hierarchy)
    
- **u** - неудаляемый (undeletable)
    

Есть также атрибуты только для чтения, которые отображаются, но не могут быть изменены через chattr:

- **E** - зашифрованный файл
    
- **I** - индексированный каталог
    
- **N** - встроенные данные
    

## Основные параметры (опции) команды lsattr

- **-R** - рекурсивно выводить атрибуты файлов и директорий во всех вложенных каталогах
    
- **-a** - выводить атрибуты всех файлов, включая скрытые (начинающиеся с точки) и специальные каталоги `.` и `..`
    
- **-d** - выводить атрибуты самих директорий, а не содержимого в них
    
- **-l** - выводить параметры в длинном формате (именами, а не символами)
    
- **-p** - выводить номер проекта файла (project ID)
    
- **-v** - выводить номер версии/генерации файла
    
- **-V** - выводить версию программы lsattr
    

## Примеры использования

- Вывести атрибуты файлов в текущей директории:
    
    text
    
    `lsattr`
    
- Вывести атрибуты всех файлов, включая скрытые:
    
    text
    
    `lsattr -a`
    
- Рекурсивно вывести атрибуты всех файлов и директорий:
    
    text
    
    `lsattr -R`
    
- Вывести атрибуты директории, а не её содержимого:
    
    text
    
    `lsattr -d /path/to/directory`
    

Таким образом, команда **lsattr** позволяет просматривать специальные атрибуты файлов и директорий, которые влияют на поведение файловой системы, и использоваться вместе с командой **chattr** для управления этими атрибутами.

### Citations:

1. [https://linux-faq.ru/page/komanda-lsattr](https://linux-faq.ru/page/komanda-lsattr)
2. [https://labex.io/ru/tutorials/linux-linux-lsattr-command-with-practical-examples-422777](https://labex.io/ru/tutorials/linux-linux-lsattr-command-with-practical-examples-422777)
3. [https://linuxcookbook.ru/articles/komanda-linux-lsattr](https://linuxcookbook.ru/articles/komanda-linux-lsattr)
4. [http://rus-linux.net/MyLDP/consol/Linux_lsattr_command.html](http://rus-linux.net/MyLDP/consol/Linux_lsattr_command.html)
5. [https://900913.ru/tldr/linux/ru/lsattr/](https://900913.ru/tldr/linux/ru/lsattr/)
6. [https://docs.altlinux.org/ru-RU/alt-server-v/10.2/html/alt-server-v/ch81s06.html](https://docs.altlinux.org/ru-RU/alt-server-v/10.2/html/alt-server-v/ch81s06.html)
7. [https://www.opennet.ru/man.shtml?topic=lsattr&category=1&russian=0](https://www.opennet.ru/man.shtml?topic=lsattr&category=1&russian=0)
8. [https://docs.altlinux.org/ru-RU/alt-server/11.0/html/alt-server/ch65s06.html](https://docs.altlinux.org/ru-RU/alt-server/11.0/html/alt-server/ch65s06.html)

---

Answer from Perplexity: [https://www.perplexity.ai/search/rasskazhi-kitaiskuiu-teoremu-o-2FTak_.9T5OrqnQ9SjVB3Q?utm_source=copy_output](https://www.perplexity.ai/search/rasskazhi-kitaiskuiu-teoremu-o-2FTak_.9T5OrqnQ9SjVB3Q?utm_source=copy_output)