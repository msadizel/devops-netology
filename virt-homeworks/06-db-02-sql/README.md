# Домашнее задание к занятию "6.2. SQL"

## Введение

Перед выполнением задания вы можете ознакомиться с 
[дополнительными материалами](https://github.com/netology-code/virt-homeworks/blob/virt-11/additional/README.md).

## Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose манифест.

```
version: "3"
services:
  postgres:
    container_name: postgres
    image: postgres
    environment:
      - POSTGRES_DB=test_db
      - POSTGRES_USER=admin
      - POSTGRES_PASSWORD=admin
    volumes:
      - ./postgres:/var/lib/postgresql/data:Z
      - ./backup:/backup
    ports:
      - 5432:5432
```

## Задача 2

В БД из задачи 1: 
- создайте пользователя test-admin-user и БД test_db
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db
- создайте пользователя test-simple-user  
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db

Таблица orders:
- id (serial primary key)
- наименование (string)
- цена (integer)

Таблица clients:
- id (serial primary key)
- фамилия (string)
- страна проживания (string, index)
- заказ (foreign key orders)

Приведите:
- итоговый список БД после выполнения пунктов выше,
```
SELECT datname FROM pg_database

postgres
template1
template0
test_db
```
- описание таблиц (describe)
```
SELECT * FROM information_schema.tables
WHERE table_schema NOT IN ('information_schema','pg_catalog');

test_db	public	orders	BASE TABLE						YES	NO	
test_db	public	clients	BASE TABLE						YES	NO	
```
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db
```
SELECT * FROM information_schema.role_table_grants WHERE grantee = 'test-simple-user';

admin	test-simple-user	test_db	public	orders	INSERT	NO	NO
admin	test-simple-user	test_db	public	orders	SELECT	NO	YES
admin	test-simple-user	test_db	public	orders	UPDATE	NO	NO
admin	test-simple-user	test_db	public	orders	DELETE	NO	NO
admin	test-simple-user	test_db	public	clients	INSERT	NO	NO
admin	test-simple-user	test_db	public	clients	SELECT	NO	YES
admin	test-simple-user	test_db	public	clients	UPDATE	NO	NO
admin	test-simple-user	test_db	public	clients	DELETE	NO	NO
```
- список пользователей с правами над таблицами test_db
```
SELECT distinct grantee  FROM information_schema.role_table_grants WHERE table_catalog = 'test_db';

PUBLIC
admin
pg_read_all_stats
test-simple-user
```

## Задача 3

Используя SQL синтаксис - наполните таблицы следующими тестовыми данными:

Таблица orders

|Наименование|цена|
|------------|----|
|Шоколад| 10 |
|Принтер| 3000 |
|Книга| 500 |
|Монитор| 7000|
|Гитара| 4000|

Таблица clients

|ФИО|Страна проживания|
|------------|----|
|Иванов Иван Иванович| USA |
|Петров Петр Петрович| Canada |
|Иоганн Себастьян Бах| Japan |
|Ронни Джеймс Дио| Russia|
|Ritchie Blackmore| Russia|

Используя SQL синтаксис:
- вычислите количество записей для каждой таблицы 
- приведите в ответе:
    - запросы 
    - результаты их выполнения.

```
INSERT INTO orders (name, price) VALUES
('Шоколад', 10),
('Принтер', 3000),
('Книга', 500),
('Монитор', 7000),
('Гитара', 4000);
```
```
SELECT COUNT(*) FROM orders;
5
```
```
INSERT INTO clients (last_name, country) VALUES
    ('Иванов Иван Иванович', 'USA'),
    ('Петров Петр Петрович', 'Canada'),
    ('Иоганн Себастьян Бах', 'Japan'),
    ('Ронни Джеймс Дио', 'Russia'),
    ('Ritchie Blackmore', 'Russia');
```
```
SELECT COUNT(*) FROM clients;
5
```

## Задача 4

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения данных операций.
```
UPDATE clients SET order_id = 3 WHERE id = 1;
UPDATE clients SET order_id = 4 WHERE id = 2;
UPDATE clients SET order_id = 3 WHERE id = 5;
```
Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса.
```
SELECT * FROM clients INNER JOIN orders o on o.id = clients.order_id;

5	Ritchie Blackmore	Russia	3	3	Книга	500
1	Иванов Иван Иванович	USA	3	3	Книга	500
2	Петров Петр Петрович	Canada	4	4	Монитор	7000
```
 
Подсказка - используйте директиву `UPDATE`.

## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).

Приведите получившийся результат и объясните что значат полученные значения.

```
Hash Join  (cost=37.00..57.24 rows=810 width=112)
  Hash Cond: (clients.order_id = o.id)
  ->  Seq Scan on clients  (cost=0.00..18.10 rows=810 width=72)
  ->  Hash  (cost=22.00..22.00 rows=1200 width=40)
        ->  Seq Scan on orders o  (cost=0.00..22.00 rows=1200 width=40)
```
Cost - оценочная стоимость.

Rows - число записей, обработанных для получения выходных данных.

Width - среднее количество байт в одной строке.

## Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. Задачу 1).
```
docker exec -it postgres /bin/bash 
cd backup/
pg_dumpall -U admin --roles-only -f roles.sql
pg_dump -U admin test_db > test_db.sql
exit
```
Остановите контейнер с PostgreSQL (но не удаляйте volumes).
```
docker stop postgres
```
Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления. 
```
docker-compose up -d
docker exec -it postgres-2 /bin/bash
cd backup/
psql -d test_db -U admin -f roles.sql
psql -U admin test_db < test_db.sql
```
---

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---