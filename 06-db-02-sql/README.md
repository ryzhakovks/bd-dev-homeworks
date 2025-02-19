# Домашнее задание к занятию 2. «SQL»

## Введение

Перед выполнением задания вы можете ознакомиться с 
[дополнительными материалами](https://github.com/netology-code/virt-homeworks/blob/virt-11/additional/README.md).

## Задача 1

Используя Docker, поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose-манифест.

## Решение 
![Безымянный](https://user-images.githubusercontent.com/110088987/236115256-311275d7-ebde-4258-b09e-5c9fb5b058c5.jpg)



## Задача 2

В БД из задачи 1: 

- создайте пользователя test-admin-user и БД test_db;
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже);
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db;
- создайте пользователя test-simple-user;
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE этих таблиц БД test_db.

Таблица orders:

- id (serial primary key);
- наименование (string);
- цена (integer).

Таблица clients:

- id (serial primary key);
- фамилия (string);
- страна проживания (string, index);
- заказ (foreign key orders).

Приведите:

- итоговый список БД после выполнения пунктов выше;
- описание таблиц (describe);
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db;
- список пользователей с правами над таблицами test_db.

## Решение!
[w](https://user-images.githubusercontent.com/110088987/236115517-6026cd9b-0592-43d4-aa33-dd6bdc7a487a.jpg)
![r](https://user-images.githubusercontent.com/110088987/236115653-e825f78e-9535-422f-a689-074eecd95d57.png)



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

## Решение

- наполним таблицы требуемыми тестовыми данными:  
```
test_db=# INSERT INTO orders VALUES (1, 'Шоколад', 10), (2, 'Принтер', 3000), (3, 'Книга', 500), (4, 'Монитор', 7000), (5, 'Гитара', 4000);
INSERT 0 5
test_db=# INSERT INTO clients VALUES (1, 'Иванов Иван Иванович', 'USA'), (2, 'Петров Петр Петрович', 'Canada'), (3, 'Иоганн Себастьян Бах', 'Japan'), (4, 'Ронни Джеймс Дио', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');
INSERT 0 5
```
- SQL-запросы для вычисления количества записей в таблицах:
```
SELECT COUNT (*) FROM orders;
SELECT COUNT (*) FROM clients;
```
- результаты:  
![image](https://user-images.githubusercontent.com/87534423/166145347-7a4af5b6-72ea-47c3-8e6b-36dda9e3e050.png)


## Задача 4

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения данных операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса.
 
Подсказка - используйте директиву `UPDATE`.

## Решение 
- свяжем записи из таблиц следующими запросами:  
```
test_db=# UPDATE clients SET заказ=(select id from orders where наименование='Книга') WHERE фамилия='Иванов Иван Иванович';
UPDATE 1
test_db=# UPDATE clients SET заказ=(select id from orders where наименование='Монитор') WHERE фамилия='Петров Петр Петрович';
UPDATE 1
test_db=# UPDATE clients SET заказ=(select id from orders where наименование='Гитара') WHERE фамилия='Иоганн Себастьян Бах';
UPDATE 1
```
- с помощью запроса ```SELECT* FROM clients WHERE заказ IS NOT NULL;``` выведем пользователей, которые совершили заказ:  
![image](https://user-images.githubusercontent.com/87534423/166145922-29cdb02b-1357-4f54-a2cb-c5cc3206e51b.png)


## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).

Приведите получившийся результат и объясните что значат полученные значения.

## Решение 
![image](https://user-images.githubusercontent.com/87534423/166146110-7847a1b3-802f-4b76-bec9-ce41a3fea35b.png)  
Чтение данных из таблицы clients происходит с использованием метода Seq Scan — последовательного чтения данных. Значение 0.00 — ожидаемые затраты на получение первой строки. Второе — 18.10 — ожидаемые затраты на получение всех строк. rows - ожидаемое число строк, которое должен вывести этот узел плана. При этом так же предполагается, что узел выполняется до конца. width - ожидаемый средний размер строк, выводимых этим узлом плана (в байтах). Каждая запись сравнивается с условием "заказ" IS NOT NULL. Если условие выполняется, запись вводится в результат. Иначе — отбрасывается.

Посмотрим, как обработается запрос в реальных условиях, применим директиву ANALYZE:  
![image](https://user-images.githubusercontent.com/87534423/166147175-4dc4df7c-a1b8-41c5-be30-648527f67749.png)  
Здесь уже видны реальные затраты на обработку первой и всех строк, количество выведенных строк (3), удовлетворяющих фильру "заказ" IS NOT NULL, количество проходов (1), количество строк, которые были удалены из запроса по фильтру (2), планируемое и затраченное время, а также общее количество строк, по которым производилась выборка.

## Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. задачу 1).

Остановите контейнер с PostgreSQL, но не удаляйте volumes.

Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления. 

## Решение  
- Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов:  
```
# pg_dump -U root test_db > /media/postgresql/backup/test_db.backup
```
- Остановим контейнер с PostgreSQL :
```
$docekr stop psql-postgresql-1
```  
![3](https://user-images.githubusercontent.com/110088987/236116662-c8ffc38e-0c26-46c3-819e-a044be32167f.png)


- Поднимем новый пустой контейнер с PostgreSQL:  
```

```
- Восстановите БД test_db в новом контейнере. Для этого скопируем файл дампа из контейнера в контейнер :  
```
$ [root@192 psql]# docker cp ./backup/test_db.backup postgres12-1:/home/
Successfully copied 5.63kB to postgres12-1:/home/
```  
- и восстановим базу:
```
root@c8484fdf7c07:/# psql -U root -d test_db -f /home/test_db.backup

---

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---

