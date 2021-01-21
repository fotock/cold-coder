# MySQL 8.0 之 Insert 插入速度优化

## 表结构

```sql
CREATE TABLE `bench_insert` (
  `id` int NOT NULL AUTO_INCREMENT,
  `i1` tinyint not null,
  `i2` smallint not null,
  `i3` int not null,
  `t1` varchar(50) DEFAULT NULL,
  `t2` varchar(50) DEFAULT NULL,
  `d` date DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB;

```

## 创建存储过程，各插入100k记录

```sql
delimiter $$
DROP PROCEDURE IF EXISTS test_insert;
CREATE PROCEDURE test_insert()
BEGIN
    DECLARE n int DEFAULT 1;
        -- START TRANSACTION;
        loopname:LOOP
            INSERT INTO bench_insert (`i1`, `i2`, `i3`, `t1`, `t2`, `d`) 
            VALUES (8, 88, 88888, 'aaaaaaaaaa', 'bbbbbbbbb', '2020-04-05');
            SET n=n+1;
        IF n=100000 THEN
            LEAVE loopname;
        END IF;
        END LOOP loopname;
        -- COMMIT;
END;

DROP PROCEDURE IF EXISTS test_insert_trans;
CREATE PROCEDURE test_insert_trans()
BEGIN
    DECLARE n int DEFAULT 1;
        START TRANSACTION;
        loopname1:LOOP
            INSERT INTO bench_insert (`i1`, `i2`, `i3`, `t1`, `t2`, `d`) 
            VALUES (8, 88, 88888, 'aaaaaaaaaa', 'bbbbbbbbb', '2020-04-05');
            SET n=n+1;
        IF n=100000 THEN
            LEAVE loopname1;
        END IF;
        END LOOP loopname1;
        COMMIT;
END;

DROP PROCEDURE IF EXISTS test_insert_trans1;
CREATE PROCEDURE test_insert_trans1()
BEGIN
    DECLARE n int DEFAULT 1;
        loopname1:LOOP
            START TRANSACTION;
            INSERT INTO bench_insert (`i1`, `i2`, `i3`, `t1`, `t2`, `d`) 
            VALUES (8, 88, 88888, 'aaaaaaaaaa', 'bbbbbbbbb', '2020-04-05');
            COMMIT;
            SET n=n+1;
        IF n=100000 THEN
            LEAVE loopname1;
        END IF;
        END LOOP loopname1;
END;
$$
delimiter ;
```

## 测试

```sql
CALL test_insert();
CALL test_insert_trans();

-- 更接近实际使用情形
CALL test_insert_trans1();
```

## 结果

### 未启用 skip-log-bin 时

不使用事务 206s，使用事务 2.22s.

#### 启用 skip-log-bin 后

1. test_insert_trans 比 test_insert 平均提升约1倍 (1.37s vs 2.66s);
2. test_insert_trans1 比 test_insert 降低约17% (2.94s vs 2.51s);

## 附录1 my.cnf

```conf
[mysqld]
skip_name_resolve = 1
skip-log-bin

innodb_flush_log_at_trx_commit = 2
```

## 附录2 更新单个字段

```sql
delimiter $$
DROP PROCEDURE IF EXISTS test_update;
CREATE PROCEDURE test_update()
BEGIN
    DECLARE n int DEFAULT 1;
        loopname:LOOP
            update bench_insert set `i3`= FLOOR(RAND()*2500) where id=1;
            SET n=n+1;
        IF n=100000 THEN
            LEAVE loopname;
        END IF;
        END LOOP loopname;
END;
$$
delimiter ;

call test_update();
```

>> 10万次更新，耗时 3.44 秒。


## memory vs innodb 引擎对比插入速度

CPU 8核/3.2GHz。6个字段，5万条记录。

### memory (0.18s)

```sql
CREATE TABLE `tbmemory`
(
  `id`         bigint(20)       DEFAULT NULL,
  `datetime`   timestamp        DEFAULT CURRENT_TIMESTAMP,
  `channel`    int(11)          DEFAULT NULL,
  `value`      bigint(20)       DEFAULT NULL,
  `tiny`     tinyint unsigned DEFAULT NULL,
  `str` varchar(30) default null
) ENGINE = MEMORY;

INSERT INTO `tbmemory` (`id`, `datetime`,`value`,`channel`, `tiny`, `str`)
WITH recursive 
cte AS (select 1 i
        union all
        select i+1 from cte where i <= 50000)
select i, 
       FROM_UNIXTIME(UNIX_TIMESTAMP('2014-01-01 01:00:00')+FLOOR(RAND()*31536000)),
       ROUND(RAND()*100,2),
       i,
       i%200,
       '123456789123456789123456789'
FROM cte;
```

### innodb (0.55s)

```sql
CREATE TABLE `tbinnodb`
(
  `id`         bigint(20)       DEFAULT NULL,
  `datetime`   timestamp        DEFAULT CURRENT_TIMESTAMP,
  `channel`    int(11)          DEFAULT NULL,
  `value`      bigint(20)       DEFAULT NULL,
  `tiny`       tinyint unsigned DEFAULT NULL,
  `str` varchar(30) default null
) ENGINE = innodb;

INSERT INTO `tbinnodb` (`id`, `datetime`,`value`,`channel`, `tiny`, `str`)
WITH recursive 
cte AS (select 1 i
        union all
        select i+1 from cte where i <= 50000)
select i, 
       FROM_UNIXTIME(UNIX_TIMESTAMP('2014-01-01 01:00:00')+FLOOR(RAND()*31536000)),
       ROUND(RAND()*100,2),
       i,
       i%200,
       '123456789123456789123456789'
FROM cte;
```
