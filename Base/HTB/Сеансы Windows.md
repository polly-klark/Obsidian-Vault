#windows #htb 

#### Интерактивный

Интерактивный или локальный сеанс входа в систему инициируется пользователем, который проходит аутентификацию в локальной или доменной системе, вводя свои учётные данные. Интерактивный вход в систему можно инициировать, войдя непосредственно в систему, запросив вторичный сеанс входа в систему с помощью команды `runas` в командной строке или через подключение к удалённому рабочему столу.

#### Неинтерактивный

Неинтерактивные учётные записи в Windows отличаются от стандартных учётных записей пользователей тем, что для входа в них не требуются учётные данные. Существует 3 типа неинтерактивных учётных записей: локальная системная учётная запись, локальная учётная запись службы и учётная запись сетевой службы. Неинтерактивные учётные записи обычно используются операционной системой Windows для автоматического запуска служб и приложений без участия пользователя. Эти учётные записи не имеют пароля и обычно используются для запуска служб при загрузке системы или для выполнения запланированных задач.

Существуют различия между тремя типами учетных записей:

|Учетная запись|Описание|
|---|---|
|Учетная Запись локальной системы|Также известная как учётная запись `NT AUTHORITY\SYSTEM` — это самая мощная учётная запись в системах Windows. Она используется для различных задач, связанных с ОС, например для запуска служб Windows. Эта учётная запись обладает более широкими возможностями, чем учётные записи в группе локальных администраторов.|
|Учетная Запись Локальной службы|Эта учётная запись, известная как `NT AUTHORITY\LocalService` учётная запись, является менее привилегированной версией учётной записи SYSTEM и имеет те же права, что и локальная учётная запись пользователя. Она обладает ограниченными функциями и может запускать некоторые службы.|
|Учетная Запись сетевой службы|Эта учётная запись известна как `NT AUTHORITY\NetworkService` и похожа на стандартную учётную запись пользователя домена. Она обладает теми же правами, что и локальная учётная запись службы на локальном компьютере. Она может устанавливать сеансы аутентификации для определённых сетевых служб.|