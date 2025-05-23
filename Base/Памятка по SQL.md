#SQL

Для более полного погружения в SQL есть руководства по [MySQL](https://metanit.com/sql/mysql/) и [PostgreSQL](https://metanit.com/sql/postgresql/) от Метанита. Они хороши тем, что просты в изучении и позволяют быстро начать работу с названными СУБД.

[Официальная документация по MySQL](https://dev.mysql.com/doc/refman/8.0/en/).  

[Официальная документация по PostreSQL](https://postgrespro.ru/docs/postgresql/13/index) (на русском языке).

[Свежий туториал по SQL](https://www.youtube.com/watch?app=desktop&v=HXV3zeQKqGY) от Codecamp.

[Свежая шпаргалка по SQL в формате PDF](https://websitesetup.org/wp-content/uploads/2020/08/SQL-Cheat-Sheet-Summary-Full.png).

# Что такое SQL?

SQL — это язык структурированных запросов (Structured Query Language), позволяющий хранить, манипулировать и извлекать данные из реляционных баз данных (далее — РБД, БД).

# Почему SQL?

SQL позволяет:

-   получать доступ к данным в системах управления РБД
-   описывать данные (их структуру)
-   определять данные в БД и управлять ими
-   взаимодействовать с другими языками через модули SQL, библиотеки и предваритальные компиляторы
-   создавать и удалять БД и таблицы
-   создавать представления, хранимые процедуры (stored procedures) и функции в БД
-   устанавливать разрешения на доступ к таблицам, процедурам и представлениям

# Процесс SQL

При выполнении любой SQL-команды в любой RDBMS (Relational Database Management System — система управления РБД, СУБД, например, PostgreSQL, MySQL, MSSQL, SQLite и др.) система определяет наилучший способ выполнения запроса, а движок SQL определяет способ интерпретации задачи.

В данном процессе участвует несколького компонентов:

-   диспетчер запросов (Query Dispatcher)
-   движок оптимизации (Optimization Engines)
-   классический движок запросов (Classic Query Engine)
-   движок запросов SQL (SQL Query Engine) и т.д.

Классический движок обрабатывает все не-SQL-запросы, а движок SQL-запросов не обрабатывает логические файлы.

# Команды SQL

Стандартными командами для взаимодействия с РБД являются `CREATE`, `SELECT`, `INSERT`, `UPDATE`, `DELETE` и `DROP`. Эти команды могут быть классифицированы следующим образом:

-   `DDL` — язык определения данных (Data Definition Language)

![[Pasted image 20230108154351.png]]

-   `DML` — язык изменения данных (Data Manipulation Language)

![[Pasted image 20230108154423.png]]

-   `DCL` — язык управления данными (Data Control Language)

![[Pasted image 20230108154506.png]]

_Обратите внимание_: использование верхнего регистра в названиях команд SQL — это всего лишь соглашение, большинство СУБД нечувствительны к регистру. Тем не менее, форма записи инструкций, когда названия команд пишутся большими буквами, а названия таблиц, колонок и др. — маленькими, позволяет быстро определять назначение производимой с данными операции.

# Что такое таблица?

Данные в СУБД хранятся в объектах БД, называемых таблицами (tables). Таблица, как правило, представляет собой коллекцию связанных между собой данных и состоит из определенного количества колонок и строк.

Таблица — это самая распространенная и простая форма хранения данных в РБД. Вот пример таблицы с пользователями (users):

![[Pasted image 20230108154601.png]]

# Что такое поле?

Каждая таблица состоит из небольших частей — полей (fields). Полями в таблице users являются userId, userName, age, city и status. Поле — это колонка таблицы, предназначенная для хранения определенной информации о каждой записи в таблице.

_Обратите внимание_: вместо `userId` и `userName` можно было бы использовать `id` и `name`, соответственно. Но при работе с несколькими объектами, содержащими свойство `id`, бывает сложно понять, какому объекту принадлежит идентификатор, особенно, если вы, как и я, часто прибегаете к деструктуризации. Что касается слова `name`, то оно часто оказывается зарезервизованным, т.е. уже используется в среде, в которой выполняется код, поэтому я стараюсь его не использовать.

# Что такое запись или строка?

Запись или строка (record/row) — это любое единичное вхождение (entry), существующее в таблице. В таблице `users` 5 записей. Проще говоря, запись — это горизонтальное вхождение в таблице.

# Что такое колонка?

Колонка (column) — это вертикальное вхождение в таблице, содержащее всю информацию, связанную с определенным полем. В таблице `users` одной из колонок является `city`, которая содержит названия городов, в которых проживают пользователи.

# Что такое нулевое значение?

Нулевое значение (NULL) — это значение поля, которое является пустым, т.е. нулевое значение — это значение поля, не имеющего значения. Важно понимать, что нулевое значение отличается от значения `0` и от значения поля, содержащего пробелы (`` `). Поле с нулевым значением - это такое поля, которое осталось пустым при создании записи. Также, следует учитывать, что в некоторых СУБД пустая строка ( ``''`) — это`NULL`, а в некоторых — это разные значения.

# Ограничения

Ограничения (constraints) — это правила, применяемые к данным. Они используются для ограничения данных, которые могут быть записаны в таблицу. Это обеспечивает точность и достоверность данных в БД. 

Ограничения могут устанавливаться как на уровне колонки, так и на уровне таблицы.

Среди наиболее распространенных ограничений можно назвать следующие:

-   `NOT NULL` — колонка не может иметь нулевое значение
-   `DEFAULT` — значение колонки по умолчанию
-   `UNIQUE` — все значения колонки должны быть уникальными
-   `PRIMARY KEY` — первичный или основной ключ, уникальный идентификатор записи в текущей таблице
-   `FOREIGN KEY` — внешний ключ, уникальный идентификатор записи в другой таблице (таблице, связанной с текущей)
-   `CHECK` — все значения в колонке должны удовлетворять определенному условию
-   `INDEX` — быстрая запись и извлечение данных

Любое ограничение может быть удалено с помощью команды `ALTER TABLE` и `DROP CONSTRAINT` + название ограничения. Некоторые реализации предоставляют сокращения для удаления ограничений и возможность отключать ограничения вместо их удаления.

# Целостность данных  

В каждой СУБД существуют следующие категории целостности данных:

-   целостность объекта (Entity Integrity) — в таблице не должно быть дубликатов (двух и более строк с одинаковыми значениями)
-   целостность домена (Domain Integrity) — фильтрация значений по типу, формату или диапазону
-   целостность ссылок (Referential integrity) — строки, используемые другими записями (строки, на которые в других записях имеются ссылки), не могут быть удалены
-   целостность, определенная пользователем (User-Defined Integrity) — дополнительные правила

# Нормализация БД

Нормализация — это процесс эффективной организации данных в БД. Существует две главных причины, обуславливающих необходимость нормализации:

-   предотвращение записи в БД лишних данных, например, хранения одинаковых данных в разных таблицах
-   обеспечение "оправданной" связи между данными

Нормализация предполагает соблюдение нескольких форм. Форма — это формат структурирования БД. Существует три главных формы: первая, вторая и, соответственно, третья. Я не буду вдаваться в подробности об этих формах, при желании, вы без труда найдете необходимую информацию.

# Синтаксис SQL

Синтаксис — это уникальный набор правил и рекомендаций. Все инструкции `SQL` должны начинаться с ключевого слова, такого как `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `ALTER`, `DROP`, `CREATE`, `USE`, `SHOW` и т.п. и заканчиваться точкой с запятой (`;`) (точка с запятой не входит в синтаксис `SQL`, но ее наличия, как правило, требуют консольные клиенты СУБД для обозначения окончания ввода команды). `SQL` не чувствителен к регистру, т.е. `SELECT`, `select` и `SeLeCt` являются идентичными инструкицями. Исключением из этого правила является `MySQL`, где учитывается регистр в названии таблицы.

**Примеры синтаксиса**

```
-- выборка 
SELECT col1, col2, ...colN
FROM tableName;

SELECT DISTINCT col1, col2, ...colN
FROM tableName;

SELECT col1, col2, ...colN
FROM tableName
WHERE condition;

SELECT col1, col2, ...colN
FROM tableName
WHERE condition1 AND|OR condition2;

SELECT col2, col2, ...colN
FROM tableName
WHERE colName IN (val1, val2, ...valN);

SELECT col1, col2, ...colN
FROM tableName
WHERE colName BETWEEN val1 AND val2;

SELECT col1, col2, ...colN
FROM tableName
WHERE colName LIKE pattern;

SELECT col1, col2, ...colN
FROM tableName
WHERE condition
ORDER BY colName [ASC|DESC];

SELECT SUM(colName)
FROM tableName
WHERE condition
GROUP BY colName;

SELECT COUNT(colName)
FROM tableName
WHERE condition;

SELECT SUM(colName)
FROM tableName
WHERE condition
GROUP BY colName
HAVING (function condition);

-- создание таблицы
CREATE TABLE tableName (
 col1 datatype, 
 col2 datatype, 
 ...
 colN datatype,
 PRIMARY KEY (одна или более колонка) 
); 
 
-- удаление таблицы
DROP TABLE tableName; 

-- создание индекса
CREATE UNIQUE INDEX indexName
ON tableName (col1, col2, ...colN);

-- удаление индекса
ALTER TABLE tableName
DROP INDEX indexName;

-- получение описания структуры таблицы
DESC tableName;

-- очистка таблицы
TRUNCATE TABLE tableName;

-- добавление/удаление/модификация колонок
ALTER TABLE tableName ADD|DROP|MODIFY colName [datatype];

-- переименование таблицы
ALTER TABLE tableName RENAME TO newTableName; 

-- вставка значений
INSERT INTO tableName (col1, col2, ...colN)
VALUES (val1, val2, ...valN)

-- обновление записей
UPDATE tableName
SET col1 = val1, col2 = val2, ...colN = valN
[WHERE condition];

-- удаление записей
DELETE FROM tableName
WHERE condition;

-- создание БД
CREATE DATABASE [IF NOT EXISTS] dbName;

-- удаление БД
DROP DATABASE [IF EXISTS] dbName;

-- выбор БД
USE dbName;

-- завершения транзакции
COMMIT; 

-- отмена изменений
ROLLBACK;
```

# Типы данных

Каждая колонка, переменная и выражение в `SQL` имеют определенный тип данных (data type). Основные категории типов данных:

**Точные числовые**

![[Pasted image 20230108160255.png]]

**Приблизительные числовые**

![[Pasted image 20230108160331.png]]

**Дата и время**

![[Pasted image 20230108160405.png]]

**Строковые символьные**

![[Pasted image 20230108160433.png]]

**Строковые символьные (юникод)**

![[Pasted image 20230108160510.png]]

**Бинарные**

![[Pasted image 20230108160545.png]]

**Смешанные**

![[Pasted image 20230108160615.png]]

# Операторы

Оператор (operators) — это ключевое слово или символ, которые, в основном, используются в инструкциях `WHERE` для выполнения каких-либо операций. Они используются как для определения условий, так и для объединения нескольких условий в инструкции.

В дальнейших примерах мы будем исходить из предположения, что переменная `a` имеет значение `10`, а `b` — `20`.

**Арифметические**

![[Pasted image 20230108160707.png]]

**Операторы сравнения**

![[Pasted image 20230108160822.png]]

**Логические операторы**

![[Pasted image 20230108160854.png]]

-   [Выражения](https://habr.com/ru/post/564390/#%D0%B2%D1%8B%D1%80%D0%B0%D0%B6%D0%B5%D0%BD%D0%B8%D1%8F)
-   [Создание БД](https://habr.com/ru/post/564390/#%D1%81%D0%BE%D0%B7%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-%D0%B1%D0%B4)
-   [Удаление БД](https://habr.com/ru/post/564390/#%D1%83%D0%B4%D0%B0%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5-%D0%B1%D0%B4)
-   [Выбор БД](https://habr.com/ru/post/564390/#%D0%B2%D1%8B%D0%B1%D0%BE%D1%80-%D0%B1%D0%B4)
-   [Создание таблицы](https://habr.com/ru/post/564390/#%D1%81%D0%BE%D0%B7%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-%D1%82%D0%B0%D0%B1%D0%BB%D0%B8%D1%86%D1%8B)
-   [Удаление таблицы](https://habr.com/ru/post/564390/#%D1%83%D0%B4%D0%B0%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5-%D1%82%D0%B0%D0%B1%D0%BB%D0%B8%D1%86%D1%8B)
-   [Добавление колонок](https://habr.com/ru/post/564390/#%D0%B4%D0%BE%D0%B1%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5-%D0%BA%D0%BE%D0%BB%D0%BE%D0%BD%D0%BE%D0%BA)
-   [Выборка полей](https://habr.com/ru/post/564390/#%D0%B2%D1%8B%D0%B1%D0%BE%D1%80%D0%BA%D0%B0-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B9)
-   [`Предложение WHERE`](https://habr.com/ru/post/564390/#%D0%BF%D1%80%D0%B5%D0%B4%D0%BB%D0%BE%D0%B6%D0%B5%D0%BD%D0%B8%D0%B5-where)
-   [`Операторы AND` и `OR`](https://habr.com/ru/post/564390/#%D0%BA%D0%BE%D0%BD%D1%8A%D1%8E%D0%BA%D1%82%D0%B8%D0%B2%D0%BD%D1%8B%D0%B5-%D0%BE%D0%BF%D0%B5%D1%80%D0%B0%D1%82%D0%BE%D1%80%D1%8B-and-%D0%B8-or)
-   [Обновление полей](https://habr.com/ru/post/564390/#%D0%BE%D0%B1%D0%BD%D0%BE%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B9)
-   [Удаление записей](https://habr.com/ru/post/564390/#%D1%83%D0%B4%D0%B0%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5-%D0%B7%D0%B0%D0%BF%D0%B8%D1%81%D0%B5%D0%B9)
-   [`Предложения LIKE` и `REGEX`](https://habr.com/ru/post/564390/#%D0%BF%D1%80%D0%B5%D0%B4%D0%BB%D0%BE%D0%B6%D0%B5%D0%BD%D0%B8%D1%8F-like-%D0%B8-regexp)
-   [`Предложение TOP`/`LIMIT`/`ROWNUM`](https://habr.com/ru/post/564390/#%D0%BF%D1%80%D0%B5%D0%B4%D0%BB%D0%BE%D0%B6%D0%B5%D0%BD%D0%B8%D0%B5-toplimitrownum)
-   [`Предложения ORDER BY` и `GROUP BY`](https://habr.com/ru/post/564390/#%D0%BF%D1%80%D0%B5%D0%B4%D0%BB%D0%BE%D0%B6%D0%B5%D0%BD%D0%B8%D1%8F-order-by-%D0%B8-group-by)
-   [`Ключевое слово DISTINCT`](https://habr.com/ru/post/564390/#%D0%BA%D0%BB%D1%8E%D1%87%D0%B5%D0%B2%D0%BE%D0%B5-%D1%81%D0%BB%D0%BE%D0%B2%D0%BE-distinct)
-   [Соединения](https://habr.com/ru/post/564390/#%D1%81%D0%BE%D0%B5%D0%B4%D0%B8%D0%BD%D0%B5%D0%BD%D0%B8%D1%8F)
-   [`Предложение UNION`](https://habr.com/ru/post/564390/#%D0%BF%D1%80%D0%B5%D0%B4%D0%BB%D0%BE%D0%B6%D0%B5%D0%BD%D0%B8%D0%B5-union)
-   [`Предложение UNION ALL`](https://habr.com/ru/post/564390/#%D0%BF%D1%80%D0%B5%D0%B4%D0%BB%D0%BE%D0%B6%D0%B5%D0%BD%D0%B8%D0%B5-union-all)
-   [Синонимы](https://habr.com/ru/post/564390/#%D1%81%D0%B8%D0%BD%D0%BE%D0%BD%D0%B8%D0%BC%D1%8B)
-   [Индексы](https://habr.com/ru/post/564390/#%D0%B8%D0%BD%D0%B4%D0%B5%D0%BA%D1%81%D1%8B)
-   [Обновление таблицы](https://habr.com/ru/post/564390/#%D0%BE%D0%B1%D0%BD%D0%BE%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5-%D1%82%D0%B0%D0%B1%D0%BB%D0%B8%D1%86%D1%8B)
-   [Очистка таблицы](https://habr.com/ru/post/564390/#%D0%BE%D1%87%D0%B8%D1%81%D1%82%D0%BA%D0%B0-%D1%82%D0%B0%D0%B1%D0%BB%D0%B8%D1%86%D1%8B)
-   [Представления](https://habr.com/ru/post/564390/#%D0%BF%D1%80%D0%B5%D0%B4%D1%81%D1%82%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D1%8F)
-   [`HAVING`](https://habr.com/ru/post/564390/#having)
-   [Транзакции](https://habr.com/ru/post/564390/#%D1%82%D1%80%D0%B0%D0%BD%D0%B7%D0%B0%D0%BA%D1%86%D0%B8%D0%B8)
-   [Временные таблицы](https://habr.com/ru/post/564390/#%D0%B2%D1%80%D0%B5%D0%BC%D0%B5%D0%BD%D0%BD%D1%8B%D0%B5-%D1%82%D0%B0%D0%B1%D0%BB%D0%B8%D1%86%D1%8B)
-   [Клонирование таблицы](https://habr.com/ru/post/564390/#%D0%BA%D0%BB%D0%BE%D0%BD%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5-%D1%82%D0%B0%D0%B1%D0%BB%D0%B8%D1%86%D1%8B)
-   [Подзапросы](https://habr.com/ru/post/564390/#%D0%BF%D0%BE%D0%B4%D0%B7%D0%B0%D0%BF%D1%80%D0%BE%D1%81%D1%8B)
-   [Последовательности](https://habr.com/ru/post/564390/#%D0%BF%D0%BE%D1%81%D0%BB%D0%B5%D0%B4%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D1%8C%D0%BD%D0%BE%D1%81%D1%82%D0%B8)