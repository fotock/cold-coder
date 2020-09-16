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

## 创建存储过程，各插入10k记录

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

## 附录 my.cnf

```conf
[mysqld]
skip_name_resolve = 1
skip-log-bin

innodb_flush_log_at_trx_commit = 2
```
