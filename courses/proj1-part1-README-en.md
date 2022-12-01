# SQL Syntax

## Overview

In this section, we'll look at using SQL to logically represent and process data. You can use [A Tour of TiDB](https://tour.pingcap.com/) to manipulate the database by hand while reading.

## SQL Syntax

For databases, the key question may be how to represent the data and how to process it. In relational databases, data exists in the form of "tables". For example, in [A Tour of TiDB](https://tour.pingcap.com/), there is a person table:

|number|name|region|birthday|
|------:|----:|------:|--------:|
|1|tom|north|2019-10-27|
|2|bob|west|2018-10-27|
|3|jay|north|2018-10-25|
|4|jerry|north|2018-10-23|

The table has a total of 4 rows and 4 columns of information in each row, so each row becomes a minimal unit of complete information. Using one or more of these tables, we can perform various operations on it to get the information we want.

The easiest operation on a table is to directly output all rows, for example:

```sql
TiDB> select * from person;
+--------+-------+--------+------------+
| number | name  | region | birthday   |
+--------+-------+--------+------------+
| 1      | tom   | north  | 2019-10-27 |
| 2      | bob   | west   | 2018-10-27 |
| 3      | jay   | north  | 2018-10-25 |
| 4      | jerry | north  | 2018-10-23 |
+--------+-------+--------+------------+
4 row in set (0.00 sec)
```

You can also specify to output only the columns you need, for example:

```sql
TiDB> select number,name from person;
+--------+-------+
| number | name  |
+--------+-------+
| 1      | tom   |
| 2      | bob   |
| 3      | jay   |
| 4      | jerry |
+--------+-------+
4 row in set (0.01 sec)
```

Of course, there are times when we may only be interested in rows that satisfy certain conditions, such as when we may only care about people located in the North:

```sql
TiDB> select name, birthday from person where region = 'north';
+-------+------------+
| name  | birthday   | 
+-------+------------+
| tom   | 2019-10-27 | 
| jay   | 2018-10-25 | 
| jerry | 2018-10-23 | 
+-------+------------+
3 row in set (0.01 sec)
```

Through a combination of where statements and various conditions, we can only get rows that satisfy certain information.

Sometimes, we also need some general data, such as how many rows are there in the table that meet a certain condition. At this time, we need an aggregate function to count the summarized information:


```sql
TiDB> select count(*) from person where region = 'north';
+----------+
| count(*) | 
+----------+
| 3        | 
+----------+
1 row in set (0.01 sec)
```

Common aggregations include max, min, sum, and count. The above statement just outputs the number of lines that satisfy region = 'north', what if we also wanted to know the total number of people in all other regions? This is where the `group by` comes in handy:



```sql
TiDB> select region, count(*) from person group by region;
+--------+----------+
| region | count(*) |
+--------+----------+
| north  | 3        |
| west   | 1        |
+--------+----------+
2 row in set (0.01 sec)
```

Of course, we may still need to filter some rows for the aggregated results, but at this point we can't use the where statement introduced earlier, because the filter conditions after where are in effect before group by, and filtering after group by requires using having:

```
TiDB> select region, count(*) from person group by region having count(*) > 1;
+--------+----------+
| region | count(*) | 
+--------+----------+
| north  | 3        | 
+--------+----------+
1 row in set (0.02 sec)
```

Also, there are some common operations such as order by, limit, etc., which will not be described here.
In addition to operations on a single table, we often need to combine information from multiple tables. For example, there is another address table:

```sql
TiDB> create table address(number int, address varchar(50));
Execute success (0.05 sec)
TiDB> insert into address values (1, 'a'),(2, 'b'),(3, 'c'), (4, 'd');
Execute success (0.02 sec)
```

The easiest way to combine the information from the two tables is to take any row from the two tables and combine them separately, so we will have a total of 4*4 possible combinations, that is, we will get 16 rows:

```sql
TiDB> select name, address from person inner join address;
+-------+---------+
| name  | address |
+-------+---------+
| tom   | a       |
| tom   | b       |
| tom   | c       |
| tom   | d       |
| bob   | a       |
| bob   | b       |
| bob   | c       |
| bob   | d       |
| jay   | a       |
| jay   | b       |
| jay   | c       |
| jay   | d       |
| jerry | a       |
| jerry | b       |
| jerry | c       |
| jerry | d       |
+-------+---------+
16 row in set (0.02 sec)
```

However, the information explosion generated by this kind of information is often unnecessary for us. Fortunately, we can specify a strategy that combines any line. For example, if we want to know someone's address and name at the same time, then we only need to take people with the same number value in the two tables and join them together, so it will only produce 4 rows of results:

```sql
TiDB> select name, address from person inner join address on person.number = address.number;
+-------+---------+
| name  | address |
+-------+---------+
| tom   | a       |
| bob   | b       |
| jay   | c       |
| jerry | d       |
+-------+---------+
4 row in set (0.02 sec)
```

Note that the join here is an inner join. In addition to that, there are also outer joins, so I won't go into details here.


## Job Description

Complete the following Hackerrank questions

- [Revising the Select Query I](https://www.hackerrank.com/challenges/revising-the-select-query/problem) (10%)
- [Revising the Select Query II](https://www.hackerrank.com/challenges/revising-the-select-query-2/problem) (10%)
- [Revising Aggregations The Count Function](https://www.hackerrank.com/challenges/revising-aggregations-the-count-function/problem) (10%)
- [Revising Aggregations The Sum Function](https://www.hackerrank.com/challenges/revising-aggregations-sum/problem) (10%)
- [Revising Aggregations Revision](https://www.hackerrank.com/challenges/revising-aggregations-the-average-function/problem) (10%)
- [African Cities](https://www.hackerrank.com/challenges/african-cities/problem) (10%)
- [Average Population of Each Population](https://www.hackerrank.com/challenges/average-population-of-each-continent/problem) (10%)
- [Binary Tree Nodes](https://www.hackerrank.com/challenges/binary-search-tree-1/problem) (30%)

## Rating

This assignment is currently considered an after-class assignment and will not be included in the grading for the time being.