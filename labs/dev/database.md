# Системы хранения данных (СУБД)
## Задание «PostgreSQL Authentication»
Вы посмотрите, как настроена по умолчанию аутентификация в Docker образе PostgreSQL v12.

В качестве целевой конфигурации используйте файл <code>docker-compose.yml</code>:
```yaml
version: '3.7'
services:
  postgres:
    image: postgres:12
    ports:
      - 5432:5432
    environment:
      - POSTGRES_DB=db
      - POSTGRES_USER=app
      - POSTGRES_PASSWORD=pass
```
#### Этапы выполнения:
Изучите разделы документации, касающиеся аутентификации пользователей PostgreSQL v12:

1) Файл <code>pg_hba.conf</code>.
2) Аутентификация <code>Trust</code>.
3) Аутентификация <code>Password</code>.

Используйте информацию для запуска контейнера и подключения к нему:

1) Запустите контейнер командой <code>docker-compose up</code>.
2) Для помещения shell (bash) в контейнер используйте команду <code>docker-compose exec postgres /bin/bash</code>.
3) Для получения доступа к psql (оболочке выполнения запросов) используйте команду <code>docker-compose exec postgres psql -U app -d db</code>.От имени пользователя <code>app</code> подключайтесь к базе <code>db</code>.
### Часть 1. Настройки по умолчанию.
Изучите файл <code>/var/lib/postgresql/data/pg_hba.conf</code> и ответьте на вопросы, используя команду получения shell.
![pg_config.png](images%2Fpg_config.png)
#### Вопрос:
Какие методы аутентификации используются для подключения по <code>TCP/IP</code> с адресов <code>127.0.0.1/32</code> и <code>::1/128</code>?
#### Ответ:
Для адресов <code>127.0.0.1/32</code> и <code>::1/128</code> открыт доступ ко всем базам данных.
Используется метод <code>Trust</code> (Вход осуществляется без введения пароля и какой-либо другой аутентификаци).

#### Вопрос:
Какие методы аутентификации используются для подключения по <code>TCP/IP</code> со всех остальных адресов, кроме указанных в предыдущем пункте по протоколу?
Изучите настройки ролей, хранящихся в БД и ответьте на вопросы.
#### Ответ:
Для всех остальных адресов открыт доступ ко всем базам данных.
Используется аутентификация <code>SCRAM-SHA-256</code> или <code>MD5</code> (Вход осуществляется по паролю, который шифруется по вышеупомянутым алгоритмам)

#### Вопрос:
Верно ли утверждение: пароль роли <code>app</code> хранится в виде <code>функция_хеширования password</code> (пароль хранится в поле <code>rolpassword</code>)? Если неверно, то дайте описание алгоритма, который используется для хранения хеша.
#### Ответ:
Да, пароль хранится в поле <code>rolpassword</code>, шифруется по алгоритму <code>md5</code>.

#### Вопрос:
Какое значение имеют поля <code>rolsuper</code>, <code>rolcreaterole</code>, <code>rolcreatedb</code>, <code>rolbypassrls</code> с указанием назначения данных столбцов? <code>t</code> будет означать «да», <code>f</code> — «нет». См. https://postgrespro.ru/docs/postgresql/12/catalog-pg-authid.
#### Ответ:
- <code>rolsuper</code> - Да
- <code>rolcreaterole</code> - Да
- <code>rolcreatedb</code> - Да
- <code>rolbypassrls</code> - Да


### Часть 2. Изменение метода аутентификации.
Используйте команду получения <code>psql</code>, в котором выполните команду <code>select * from pg_authid;</code>:

1) Остановите контейнер, нажав <code>Ctrl + C</code> в консоли, в которой у вас была запущена команда <code>docker-compose up</code>.
2) Изучите документацию на образ Docker и измените метод аутентификации на <code>reject</code>. Вам понадобится отредактировать <code>docker-compose.yml</code>.
3) Удалите существующий контейнер, запустив команду <code>docker-compose rm</code>.
4) Запустите новый контейнер, выполнив команду <code>docker-compose up</code>.
5) Подключитесь с помощью psql и выполните команду <code>select * from pg_authid;</code>.
#### Вопрос: 
Почему значения полей <code>rolcanlogin</code> и <code>rolpassword</code> для роли <code>app</code> не изменились, и вы по-прежнему можете подключиться с помощью <code>psql</code> без указания пароля, хотя в <code>pg_hba.conf</code> для <code>host all all all</code> указано <code>reject</code>?
#### Ответ:
Мы по-прежнему можем подключиться с помощью <code>psql</code> без указания пароля потому, что через <code>docker-compose exec postgres psql -U app -d db</code> мы подключаемся как <code>local</code>, для которого доступ всё еще открыт.

---

## Задание «PostgreSQL ПРД»
Вы изучите механизмы управления пользователями и ПРД, реализованные в СУБД PostgreSQL.

Как целевую конфигурацию используйте файл <code>docker-compose.yml</code>:

```yaml
version: '3.7'
services:
  postgres:
    image: postgres:12
    ports:
      - 5432:5432
    environment:
      - POSTGRES_DB=db
      - POSTGRES_USER=app
      - POSTGRES_PASSWORD=pass
```
### Этапы выполнения
Изучите разделы документации по управлению пользователями и ПРД:
1) Роли.
2) Система прав.

Используйте информацию для запуска контейнера и подключения к нему:
1) Запустите контейнер командой <code>docker-compose up</code>.
2) Для получения shell (bash) в контейнер используйте команду <code>docker-compose exec postgres /bin/bash</code>.
3) Для получения доступа к <code>psql</code> (оболочке выполнения запросов) используйте команду <code>docker-compose exec postgres psql -U app -d db</code>. От имени пользователя <code>app</code> подключайтесь к базе <code>db</code>.
### Часть 1. Подготовка данных.
В оболочке <code>psql</code> выполните команды: 
1. <code>SELECT * FROM pg_roles;</code> — отображает существующие в системе роли.
2. Создайте новую роль <code>reader</code> с правом входа <code>CREATE ROLE reader LOGIN PASSWORD 'secret';</code>.
3. Проверьте существующие базы данных и их владельцев <code>SELECT * FROM pg_database;</code>.
4. Создайте тестовую базу данных <code>test</code> <code>CREATE DATABASE test;</code>.
5. Подключитесь к созданной БД для создания таблиц в ней.
6. Создайте таблицу records в вашей тестовой БД <code>CREATE TABLE records (value TEXT, status TEXT, created TIMESTAMP DEFAULT CURRENT_TIMESTAMP);</code>.
7. Вставьте запись в таблицу <code>INSERT INTO records(value, status) VALUES ('transfer money from 55** **** 0001 to 42** **** 0002', 'success');</code>.
8. Дайте роли reader права на чтение содержимого таблицы <code>records</code> <code>GRANT SELECT ON records TO reader</code>.
9. Посмотрите предоставленные права (команда <code>\dp</code>).
### Часть 2. Проверка ПРД.
Откройте новую консоль, то есть окно терминала, в каталоге с файлом <code>docker-compose.yml</code> и все команды выполняйте там:

1. Получите <code>psql</code> для пользователя <code>reader</code>: <code>docker-compose exec postgres psql -U reader -d test</code>. Флаг <code>-p</code> означает, что подключаемся с паролем.

2. Выполните запрос на чтение.
3. Проверьте, что остальные действия запрещены.
4. Заберите у роли <code>reader</code> права на чтение. Нужно выполнять в первой консоли от имени пользователя <code>app</code>.
5. Выполните запрос на чтение аналогично п. 2 от имени пользователя <code>reader</code>.
### Результаты выполнения задания
Отправьте сообщение, которое отображается вместо <code>???</code> в результате выполнения запросов с недостаточными правами доступа: <code>ERROR:  ???</code>.
![select_records.png](images%2Fselect_records.png)