---
layout: post
title:  "So.. I tried to sum"
date:   2018-02-13 00:30:00 +0200
categories: oh-my postgresql
---

So.. I tried to sum some amounts in a postgresql table and create a snapshot of the total.<br/>
My snapshots include the total and the id of last record up to which the amounts where summed.

```sql
CREATE TABLE sales(id serial PRIMARY KEY, amount integer);
-- and a query like
SELECT MAX(id) AS last_id, SUM(amount) AS total FROM sales;
```

None of the snapshots were correct.

## ...

Of course this happens because some transaction was committed on a later stage.

```sql
-- connection 1
BEGIN;
INSERT INTO sales values(DEFAULT, 1000);
```
```sql
-- connection 2
INSERT INTO sales values(DEFAULT, 1000);
SELECT MAX(id) AS last_id, SUM(amount) AS total FROM sales;
```
```sql
-- connection 1
END;
```

## 📝 notes

The `serial` is just a `integer` column with `NOT NULL` and a function call to get the next value
from a sequence e.g. `nextval('sales_id_seq')`. The generated sequence is owned by the column in the table.
If you **alter** the type, the default or whatever of the column the sequence will stay. If you **rename** the column, the owner will change to the new column.
If you **drop** the column the sequence will be also removed.

```
 \d+ sales_id_seq
                    Sequence "public.sales_id_seq"
  Type   | Start | Minimum |  Maximum   | Increment | Cycles? | Cache
---------+-------+---------+------------+-----------+---------+-------
 integer |     1 |       1 | 2147483647 |         1 | no      |     1
Owned by: public.sales.id
```

* `currval('sales_id_seq'); -- 9` - current value
* `nextval('sales_id_seq'); -- 10` - generates the next value

When you change the `Cache` to `10` you'll get:

```sql
-- connection 1
SELECT * FROM nextval('sales_id_seq'); -- 9
```
```sql
-- connection 2
SELECT * FROM nextval('sales_id_seq'); -- 19
```

If you want to upgrade from `integer` to `bigint`, you have to update also the sequence:

```sql
ALTER TABLE sales ALTER COLUMN id TYPE bigint;
ALTER SEQUENCE sales_id_seq AS bigint;
```

In **postgresql 10** there is now `identity` option for columns

```
CREATE TABLE payments(id integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY);

                                            Table "public.payments"
 Column |  Type   | Collation | Nullable |           Default            | Storage | Stats target | Description
--------+---------+-----------+----------+------------------------------+---------+--------------+-------------
 id     | integer |           | not null | generated always as identity | plain   |              |
Indexes:
   "payments_pkey" PRIMARY KEY, btree (id)

                  Sequence "public.payments_id_seq"
  Type   | Start | Minimum |  Maximum   | Increment | Cycles? | Cache
---------+-------+---------+------------+-----------+---------+-------
 integer |     1 |       1 | 2147483647 |         1 | no      |     1
Sequence for identity column: public.payments.id
```

It is overall the same thing as serial but it updates automatically 🎉   <br/>
An upgrade to `bigint` will be just `ALTER TABLE sales ALTER COLUMN id TYPE bigint;`

## ...

So.. How did I made the snapshots? -- I'll not include the latest records in them and sum ~1k-5k on the fly.

Note: Everything was caught in review

To write a test for this in `ruby` & `rails` you can do something like:

```ruby
#
# Sale - ApplicationRecord for the sales
# SnapshotSales - the thing that creates the snapshots and may behave weirdly
#
# In the specs

class SaleTestDoubleWithASeparateDbConnection << Sale
  establish_connection Rails.env # second connection to the database
end

SaleTestDoubleWithASeparateDbConnection.transaction do # connection 2
  SaleTestDoubleWithASeparateDbConnection.create!(amount: 200) # connection 2

  Sale.create!(amount: 100) # connection 1

  SnapshotSales.call # connection 1
end # connection 2 commit

SnapshotSales.call # connection 1
```

## Reference:

* [https://www.postgresql.org/docs/current/static/datatype-numeric.html#DATATYPE-SERIAL](https://www.postgresql.org/docs/current/static/datatype-numeric.html#DATATYPE-SERIAL)
* [https://www.postgresql.org/docs/current/static/sql-createsequence.html](https://www.postgresql.org/docs/current/static/sql-createsequence.html)
* [https://www.postgresql.org/docs/current/static/sql-createtable.html](https://www.postgresql.org/docs/current/static/sql-createtable.html)
* [https://blog.2ndquadrant.com/postgresql-10-identity-columns/](https://blog.2ndquadrant.com/postgresql-10-identity-columns/)
