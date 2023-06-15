<p class="has-line-data" data-line-start="0" data-line-end="5">Задача 1<br>
Используя docker поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.<br>
Подключитесь к БД PostgreSQL используя psql.<br>
Воспользуйтесь командой ? для вывода подсказки по имеющимся в psql управляющим командам.<br>
Найдите и приведите управляющие команды для:</p>
<p class="has-line-data" data-line-start="6" data-line-end="11">вывода списка БД<br>
подключения к БД<br>
вывода списка таблиц<br>
вывода описания содержимого таблиц<br>
выхода из psql</p>
<pre><code>docker pull postgres:13
docker volume create vol_postges
docker image tag postgres:13 hw64
docker run --rm --name hw64 -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -ti -p 5432:5432 -v vol_postges:/var/lib/postgresql/data postgres:13
docker exec -ti hw64 bash
</code></pre>
<p class="has-line-data" data-line-start="17" data-line-end="18">Подключаемся через psql</p>
<pre><code>root@d64b7a55568:/# psql -U postgres
psql (13.4 (Debian 13.4-4.pgdg110+1))
Type &quot;help&quot; for help.
postgres=#
</code></pre>
<p class="has-line-data" data-line-start="23" data-line-end="24">Вывод списка БД:</p>
<pre><code>postgres=# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
(3 rows)
</code></pre>
<p class="has-line-data" data-line-start="35" data-line-end="36">Подключение к БД:</p>
<pre><code>postgres=# \c postgres
You are now connected to database &quot;postgres&quot; as user &quot;postgres&quot;.
</code></pre>
<p class="has-line-data" data-line-start="39" data-line-end="40">Вывода списка таблиц «\dt» в таблицах пусто , если использовать ключ S получим вывод для системных объектов:</p>
<pre><code>postgres=# \dt
Did not find any relations.
postgres=# \dtS
                    List of relations
   Schema   |          Name           | Type  |  Owner   
------------+-------------------------+-------+----------
 pg_catalog | pg_aggregate            | table | postgres
 pg_catalog | pg_am                   | table | postgres
 pg_catalog | pg_amop                 | table | postgres
...
 pg_catalog | pg_user_mapping         | table | postgres
(62 rows)
</code></pre>
<p class="has-line-data" data-line-start="53" data-line-end="54">вывода описания содержимого таблиц \d[S+] NAME</p>
<pre><code>postgres=# \dS+ pg_index
                                      Table &quot;pg_catalog.pg_index&quot;
     Column     |     Type     | Collation | Nullable | Default | Storage  | Stats target | Description 
----------------+--------------+-----------+----------+---------+----------+--------------+-------------
 indexrelid     | oid          |           | not null |         | plain    |              | 
 indrelid       | oid          |           | not null |         | plain    |              | 
 indnatts       | smallint     |           | not null |         | plain    |              | 
 indnkeyatts    | smallint     |           | not null |         | plain    |              | 
...
 indpred        | pg_node_tree | C         |          |         | extended |              | 
Indexes:
    &quot;pg_index_indexrelid_index&quot; UNIQUE, btree (indexrelid)
    &quot;pg_index_indrelid_index&quot; btree (indrelid)
Access method: heap
</code></pre>
<p class="has-line-data" data-line-start="70" data-line-end="78">Задача 2<br>
Используя psql создайте БД test_database.<br>
Изучите бэкап БД.<br>
Восстановите бэкап БД в test_database.<br>
Перейдите в управляющую консоль psql внутри контейнера.<br>
Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.<br>
Используя таблицу pg_stats, найдите столбец таблицы orders с наибольшим средним значением размера элементов в байтах.<br>
Приведите в ответе команду, которую вы использовали для вычисления и полученный результат.</p>
<pre><code>docker run --rm --name hw64 -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -ti -p 5432:5432 -v vol_postges:/var/lib/postgresql/data -v /Users/komar/Docker:/var/lib/postgresql/bkp postgres:13
</code></pre>
<p class="has-line-data" data-line-start="80" data-line-end="81">Создаём БД</p>
<pre><code>root@d64b7a55568:/# ls /var/lib/postgresql/bkp/
amazver1  dockerfile541  gatewaymy.cnf  node543  test_dump.sql  test_pdump.sql  ubunver2
root@d64b7a55568:/# psql -U postgres
psql (13.4 (Debian 13.4-4.pgdg110+1))
Type &quot;help&quot; for help.

postgres=# 
postgres=# create database test_database;
CREATE DATABASE
</code></pre>
<p class="has-line-data" data-line-start="91" data-line-end="101">Востанавливаем БД<br>
root@d64b7a55568:/# psql -U postgres -d test_database &lt; /var/lib/postgresql/bkp/test_pdump.sql<br>
postgres=# \c test_database<br>
You are now connected to database “test_database” as user “postgres”.<br>
test_database=# \dt<br>
List of relations<br>
Schema |  Name  | Type  |  Owner<br>
--------±-------±------±---------<br>
public | orders | table | postgres<br>
(1 row)</p>
<pre><code>test_database=# ANALYZE VERBOSE public.orders;
INFO:  analyzing &quot;public.orders&quot;
INFO:  &quot;orders&quot;: scanned 1 of 1 pages, containing 8 live rows and 0 dead rows; 8 rows in sample, 8 estimated total rows
ANALYZE
test_database=# select avg_width from pg_stats where tablename='orders';
 avg_width 
-----------
         4
        16
         4
(3 rows)
</code></pre>
<p class="has-line-data" data-line-start="114" data-line-end="118">Задача 3<br>
Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и поиск по ней занимает долгое время. Вам, как успешному выпускнику курсов DevOps в нетологии предложили провести разбиение таблицы на 2 (шардировать на orders_1 - price&gt;499 и orders_2 - price&lt;=499).<br>
Предложите SQL-транзакцию для проведения данной операции.<br>
Можно ли было изначально исключить “ручное” разбиение при проектировании таблицы orders?</p>
<p class="has-line-data" data-line-start="119" data-line-end="120">Переименовываем старую таблицу и создаем новую с рабиением</p>
<pre><code>test_database=# alter table orders rename to orders_simple;
ALTER TABLE
test_database=# create table orders (id integer, title varchar(80), price integer) partition by range(price);
CREATE TABLE
test_database=# create table orders_less499 partition of orders for values from (0) to (499);
CREATE TABLE
test_database=# create table orders_more499 partition of orders for values from (499) to (999999999);
CREATE TABLE
test_database=# insert into orders (id, title, price) select * from orders_simple;
INSERT 0 8
test_database=# select * from orders;
 id |        title         | price 
----+----------------------+-------
  1 | War and peace        |   100
  3 | Adventure psql time  |   300
  4 | Server gravity falls |   300
  5 | Log gossips          |   123
  2 | My little database   |   500
  6 | WAL never lies       |   900
  7 | Me and my bash-pet   |   499
  8 | Dbiezdmin            |   501
(8 rows)
</code></pre>
<p class="has-line-data" data-line-start="143" data-line-end="144">Изначально на этапе проектирования таблиц можно было сделать её шардированной, тогда не пришлось бы переименовывать исходную таблицу и переносить данные в новую.</p>
<p class="has-line-data" data-line-start="145" data-line-end="148">Задача 4<br>
Используя утилиту pg_dump создайте бекап БД test_database.<br>
Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца title для таблиц test_database?</p>
<pre><code>root@14905382a36e:/# pg_dump -U postgres -d test_database &gt; /var/lib/postgresql/bkp/test_database.sql
root@14905382a36e:/# ls -la /var/lib/postgresql/bkp/ |grep test
-rw-r--r--  1 root     root     3569 Oct  3 12:56 test_database.sql
-rw-r--r--  1 root     root     2073 Sep 23 19:53 test_dump.sql
-rw-r--r--  1 root     root     2082 Oct  3 12:23 test_pdump.sql
</code></pre>
<p class="has-line-data" data-line-start="154" data-line-end="155">Для уникальности можно добавить индекс: CREATE INDEX ON orders ((lower(title)));</p
