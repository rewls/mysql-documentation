# Ch3 Tutorial

## Contents

- Ch3 Tutorial

    - 3.1 Connecting to and Disconnecting from the Server

    - 3.2 Entering Queries

    - 3.3 Creating and Using a Database

        - 3.3.1 Creating and Selecting a Database

        - 3.3.2 Creating a Table

        - 3.3.3 Loading Data into a Table

        - 3.3.4 Retrieving Information from a Table

            - 3.3.4.1 Selecting All Data

            - 3.3.4.2 Selecting Particular Rows

            - 3.3.4.3 Selecting Particular Columns

            - 3.3.4.4 Sorting Rows

            - 3.3.4.5 Date Calculations

            - 3.3.4.6 Working with NULL Values

            - 3.3.4.7 Pattern Matching

            - 3.3.4.8 Counting Rows

            - 3.3.4.9 Using More Than one Table

    - 3.4 Getting Information about Databases and Tables

    - 3.5 Using mysql in Batch Mode

    - 3.6 Examples of Common Queries

## Keywords

### 3.1 Connecting to and Disconnecting from the Server

- `mysql -h <host> -u <user> -p`

- `mysql>`, `quit`, `\q`

### 3.2 Entering Queries

- query, SQL statement, semicolon, keywords are case-insensitive

- calculator

- `\c`, prompt

### 3.3 Creating and Using a Database

- `show databases;`, `use <database>`

#### 3.3.1 Creating and Selecting a Database

- `create database <database>;`, database names are case-sensitive

- `mysql -h <host> -u <user> -p <database>`

- `select database();`

#### 3.3.2 Creating a Table

- `show tables;`

- `create table <table> (...);`, `varchar`, `date`, 

- `alter table`

- `describe <table>;`

#### 3.3.3 Loading Data into a Table

- `\N`, `load data local infile <path> into table <table>;`

    - [Section 6.1.6 Security Considerations for LOAD DATA LOCAL](https://dev.mysql.com/doc/refman/8.0/en/load-data-local-security.html)

- `insert into <table> values (...);`, `NULL`

#### 3.3.4 Retrieving Information from a Table

- `select <what_to_select> from <which_table> where <conditions_to_satisfy>;`

##### 3.3.4.2 Selecting Particular Rows

- string comparisons are case-insensitive

##### 3.3.4.3 Selecting Particular Columns

- `select distinct <columns> from <table>;`

##### 3.3.4.4 Sorting Rows

- `select <columns> from <table> order by <columns>;`

- `select <columns> from <table> order by binary <columns>;`

- `select <columns> from <table> order by <columns> desc;`

##### 3.3.4.5 Date Calculations

- `select name, birth, curdate(), timestampdiff(year, birth, curdate()) as age from pet;`

- `select name, birth, death, timestampdiff(year, birth, death) as age from pet where death is not null order by age;`

- `year()`, `month()`, `dayofmonth()`, `month()`

- `select name, birth from pet where month(birth) = month(date_add(curdate(), interval 1 month));

- `select name, birth from pet where month(birth) = mod(month(curdate()), 12) + 1;

- `show warnings;`

##### 3.3.4.6 Working with NULL Values

- `select 1 = null, 1 <> null, 1 < null, 1 > null;`

##### 3.3.4.7 Pattern Matching

- `_`, `%`, `like`, `not like`

- `regexp_like()`, `.`, `[...]`, `*`, `^`, `$`, `{n}`

- `select * from pet where regexp_like(name, '^b', collate utf8mb4_0900_as_cs);

- `select * from pet where regexp_like(name, binary '^b');

- `select * from pet where regexp_like(name, '^b', 'c');

##### 3.3.4.8 Counting Rows

- `count(*)`

- `select species, sex, count(*) from pet group by species, sex`

##### 3.3.4.9 Using More Than one Table

- `select pet.name, timestampdiff(year, birth, date) as age, remark from pet inner join event on pet.name = event.name where event.type = 'litter';`

### 3.4 Getting Information about Databases and Tables

- `show databases;`, `database()`

- `show tables;` `describe`, `desc`

- `show create table`, `show index from <table>`

### 3.5 Using mysql in Batch Mode

```sh
$ mysql < <file name>
```

```sh
$ mysql -e "source <file name>"
```

- `--force`

- `mysql -t`, `mysql-v`

```sh
mysql> source <file name>
```

```sh
mysql> \. <file name>
```

### 3.6 Examples of Common Queries

```sql
create table if not exists shop(
    article int unsigned default '0000' not null,
    dealer char(20) default '' not null,
    price decimal(16,2) default '0.00' not null,
    primary key(article, dealer));
insert into shop values
    (1,'A',3.45),(1,'B',3.99),(2,'A',10.99),(3,'B',1.45),
    (3,'C',1.69),(3,'D',1.25),(4,'D',19.95);
```

#### 3.6.1 The Maximum Value for a Column

```sql
select max(article) as article from shop;
```

#### 3.6.2 The Row Holding the Maximum of a Certian Column

- subquery

    ```sql
    select article, dealer, price
    from shop
    where price=(select max(price) from shop);
    ```

- left join

    ```sql
    select s1.article, s1.dealer, s1.price
    from shop s1
    left join shop s2 on s1.price < s2.price
    where s2.article is null;
    ```

- sort

    ```sql
    select article, dealer, price
    from shop
    order by price desc
    limit 1;
    ```

#### 3.6.3 Maximum of Column per Group

```sql
select article, max(price) as price
from shop
group by article
order by article;
```

#### 3.6.4 The Rows Holding the Group-wise Maximum of a Certain Column

- subquery

    ```sql
    select article, dealer, price
    from shop s1
    where price=(select max(s2.price)
                 from shop s2
                 where s1.article = s2.article)
    order by article;
    ```

    - see Section 13.2.15.7 Correlated Subqueries

- uncorrelateed subquery

    ```sql
    select s1.article, dealer, s1.price
    from shop s1
    join (
        select article, max(price) as price
        from shop
        group by article) as s2
        on s1.article = s2.article and s1.price = s2.price
    order by article;
    ```

- left join 

    ```sql
    select s1.article, s1dealer, s1.price
    from shop s1
    left join shop s2 on s1.article = 2s.article and s1.price < s2.price
    where s2.article is null
    order by s1.article;
    ```

    - see Section 13.2.13.2 JOIN Clause

- window function

```sql
with s1 as (
    select article, dealer, price,
        rank() over (partition by article
                        order by price desc
                    ) as `Rank`
    from shop
)
select article, dealer, price
    from s1
    where `Rank` = 1
order by article;
```

#### 3.6.5 Using User-Defined Variables

- see  Section 9.4 User-Defined Variables

```sql
select @min_price:=min(price),@max_price:max(price) from shop;
select * from shop where price=@min_price or price=@max_price;
```

- name of a database object, prepared statement

    - see Section 13.5 Prepared Statements

#### 3.6.6 Using Foreign Keys

```sql
create table parent (
    id int not null,
    primary key (id)
) engine=innodb;

create table child (
    id int,
    parent_id int,
    index par_ind (parent_id),
    foreign key (parent_id)
        references parent(id)
) engine=innodb;
```

```sql
insert into parent (id) values row(1);
insert into child (id,parent_id) values row(1,1);
```

- insertion of `parent_id` value that is not present in the parent table, error

```sql
mysql> INSERT INTO child (id,parent_id) VALUES ROW(2,2);
ERROR 1452 (23000): Cannot add or update a child row: a foreign key constraint fails 
(`test`.`child`, CONSTRAINT `child_ibfk_1` FOREIGN KEY (`parent_id`) 
REFERENCES `parent` (`id`))
```

- delete referenced id, error

```sql
mysql> DELETE FROM parent WHERE id = 1;
ERROR 1451 (23000): Cannot delete or update a parent row: a foreign key constraint fails 
(`test`.`child`, CONSTRAINT `child_ibfk_1` FOREIGN KEY (`parent_id`) 
REFERENCES `parent` (`id`))
```

- operation affects a key value in the parent table, referential action, `on update`, `on delete` subclauses of the `foreign key` clause

- ommit `on delete` and `on delete` clauses, `restrict` option, reject operation that affect a key value in the parent table

- `cascade`, automatically update child table

```sql
drop table child;

create table child (
    id int,
    parent_id int,
    index par_ind (parent_id),
    foreign key (parent_id)
        references parent(id)
        on update cascade
        on delete cascade
) engine=innodb;
```

- see Section 13.1.20.5 FOREIGN KEY Constraints”

#### 3.6.7 Searching on Two Keys

- `or`

    ```sql
    select field1_index, field2_index from test_table
    where field1_index = '1' or field2_index = '1'
    ```

    - see Section 8.2.1.3 Index Merge Optimization

- `union`

    - see Section 13.2.18 UNION Clause

```sql
select field1_index, field2_index
    from test_table where field1_index = '1'
union
select field1_index, field2_index
    from test_table where field2_index = '1';
```

#### 3.6.8 Calculating Visits Per Day

- bit group functions

```sql
create table t1 (year year, month int unsigned
    day int unsigned);
insert into t1 values(2000,1,1),(2000,1,20),(2000,1,30),(2000,2,2),(2000,2,23),(2000,2,23);
```

```sql
select year, month, bit_count(bit_or(1<<day)) as days from t1
    group by year,month;
```

#### 3.6.9 Using AUTO_INCREMENT

```sql
create table animals (
    id mediumint not null auto_increment,
    name char(30) not null,
    primary key (id)
);

insert into animals (name) values
    ('dog'),('cat'),('penguin'),
    ('lax'),(whale'),('ostrich');

select * from animals;
```

- 0, `NO_AUTO_VALUE_ON_ZERO` SQL mode disable

- null, `not null`

- other value, reset

- most recent automatically generated `auto_increment` value, `last_insert_id()` SQL function, `mysql_insert_id()` C API function, connection-specific

    - multiple-row insert, first row

- `unsigned`, `tinyint` 

    - see Section 11.1.2 Integer Types (Exact Value) - INTEGER, INT, SMALLINT, TINYINT, MEDIUMINT, BIGINT

```sql
alter table tb1 auto_increment = 100;
`

##### InnoDB Notes

- see Section 15.6.1.6 AUTO_INCREMENT Handling in InnoDB

##### MyISAM Notes

- auto_increment`, secondary column, multiple-column index

```sql
create table animals (
grp enum('fish','mammal','bird') not null,
id mediumint not null auto_increment,
name char(30) not null,
primary key (grp, id)
) engin=MyISAM;

insert into animals (grp,name) values
    ('mammal','dog'),('mammal','cat'),
    ('bird','penguin'),('fish','lax'),('mammal','whale'),
    ('bird','ostrich');

select * from animals order by grp,id;
```

- reuse deleted biggest auto_increment value

##### Further Reading

- assign, see Section 13.1.20 CREATE TABLE Statement and Section 13.1.9 ALTER TABLE Statement

- `NO_AUTO_VALUE_ON_ZERO` SQL mode, see Section 5.1.11 Server SQL Modes

- `LAST_INSERT_ID()`, see Section 12.15 Information Functions

- setting, Section 5.1.8 Server System Variables

- see Section 15.6.1.6 AUTO_INCREMENT Handling in InnoDB

- replication, Section 17.5.1.1 Replication and AUTO_INCREMENT

- server-system variables, `auto_increment_increment`, auto_increment_offset`, Section 5.1.8 Server System Variables

### 3.7 Using MySQL with Apache

- Apache logging format, easily readable by MySQL, Apache configuration file

```
LogFormat \
        "\"%h\",%{%Y%m%d%H%M%S}t,%>s,\"%b\",\"%{Content-Type}o\",  \
        \"%U\",\"%{Referer}i\",\"%{User-Agent}i\""
```

- load

```sql
load data infile '/local/access_log' into table tb1_name
fields terminated by ',' optionally enclosed by '"' escaped by '\\'
```
