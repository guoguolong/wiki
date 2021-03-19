JOIN

INNER JOIN

CROSS JOIN

> CROSS JOIN在Mysql中等同于INNER JOIN

OUTER:

- LEFT JOIN
- RIGHT JOIN
- FULL JOIN:

```sql
SELECT * FROM user
LEFT JOIN goods ON user.user_id = goods.user_id
UNION ALL
SELECT * FROM user
RIGHT JOIN goods ON user.user_id = goods.user_id;
```

## 实验例子SQL

```sql
create table user (
    user_id int,
    username varchar(50),
    age int,
    primary key(user_id)
);

drop table goods;
create table goods (
    goods_id int,
    name varchar(50),
    price float,
    user_id int,
    primary key(goods_id)
);

INSERT INTO user VALUES (10, 'Allen', 30);
INSERT INTO user VALUES (11, 'Lumen', 12);
INSERT INTO user VALUES (12, 'Koda', 87);
INSERT INTO user VALUES (13, 'LeoPard', 9);

DELETE FROM goods;
INSERT INTO goods VALUES (110, 'MP3', 130, 10);
INSERT INTO goods VALUES (111, 'iPod', 1120, 11);
INSERT INTO goods VALUES (112, 'MacOs', 6790, 12);
INSERT INTO goods VALUES (113, 'iOs', 4710, 12);
INSERT INTO goods VALUES (115, 'TeaBot', 47, null);

SELECT * FROM user JOIN goods ON user.user_id = goods.user_id;
SELECT * FROM user NATURE JOIN goods ON user.user_id = goods.user_id;
SELECT * FROM user INNER JOIN goods ON user.user_id = goods.user_id;
SELECT * FROM user CROSS JOIN goods ON user.user_id = goods.user_id;
SELECT * FROM user LEFT JOIN goods ON user.user_id = goods.user_id;
SELECT * FROM user LEFT OUTER JOIN goods ON user.user_id = goods.user_id;
SELECT * FROM user RIGHT JOIN goods ON user.user_id = goods.user_id;

SELECT * FROM user
LEFT JOIN goods ON user.user_id = goods.user_id
UNION ALL
SELECT * FROM user
RIGHT JOIN goods ON user.user_id = goods.user_id;
```