---
title: MySQL 面试常见增删改查与高频知识点总结
description: 汇总常见 MySQL CRUD 面试题及核心知识点，覆盖索引、事务、锁、子查询等内容
slug: mysql-crud-interview
date: 2025-12-03 00:00:00+0000
categories:
    - 数据库
    - MySQL
---

### MySQL 面试常见增删改查与知识点总结

在面试中，MySQL 是后端岗位最常考的基础能力之一。本文不仅总结了常用 CRUD 语句，还包含常见索引、事务、锁机制等核心知识点，适用于面试快速复习。

## 🟢 1. 创建表（CREATE TABLE）

CREATE TABLE users (
id INT PRIMARY KEY AUTO_INCREMENT,
name VARCHAR(50) NOT NULL,
age INT CHECK (age >= 0),
email VARCHAR(100) UNIQUE,
status TINYINT DEFAULT 1,
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

## 🟡 2. 插入数据（INSERT）

INSERT INTO users (name, age, email)
VALUES ('Alice', 25, 'alice@example.com');

INSERT INTO users (name, age, email)
VALUES
('Bob', 30, 'bob@example.com'),
('Charlie', 28, 'charlie@example.com');

INSERT IGNORE INTO users (email) VALUES ('alice@example.com');

REPLACE INTO users(id, name, email)
VALUES (1, 'Tom', 'tom@example.com');


## 🔵 3. 查询数据（SELECT）

SELECT * FROM users WHERE age > 20;

SELECT status, COUNT(*) AS total
FROM users
GROUP BY status
HAVING total > 1;

SELECT * FROM users ORDER BY created_at DESC;

SELECT * FROM users LIMIT 20 OFFSET 0;

SELECT * FROM users
WHERE age > (SELECT AVG(age) FROM users);

## 🟠 4. 更新数据（UPDATE）

UPDATE users
SET age = age + 1
WHERE id = 1;

UPDATE users
SET age = 26, status = 0
WHERE email = 'alice@example.com';


## 🔴 5. 删除数据（DELETE）

DELETE FROM users WHERE id = 3;

DELETE FROM users;

TRUNCATE TABLE users;


## 🧩 6. 外键（FOREIGN KEY）

CREATE TABLE orders (
id INT PRIMARY KEY AUTO_INCREMENT,
user_id INT,
amount DECIMAL(10,2),
FOREIGN KEY(user_id) REFERENCES users(id)
ON DELETE CASCADE
ON UPDATE CASCADE
);
## 🧱 7. 索引（INDEX）

CREATE INDEX idx_age ON users(age);

CREATE INDEX idx_name_email ON users(name, email);

-- 索引重点：
-- 1. 最左前缀原则
-- 2. 覆盖索引
-- 3. 前缀模糊查询索引失效（LIKE '%abc'）
-- 4. 对索引字段使用函数会导致索引失效


## 🔐 8. 事务（Transaction）

START TRANSACTION;

UPDATE users SET age = age + 1 WHERE id = 1;
UPDATE accounts SET money = money - 100 WHERE user_id = 1;

COMMIT;

-- 回滚示例
ROLLBACK;

-- ACID
-- A：原子性
-- C：一致性
-- I：隔离性
-- D：持久性

## 🔒 9. 锁机制（InnoDB）

-- 行锁（排他锁）
SELECT * FROM users WHERE id = 1 FOR UPDATE;

-- 共享锁（读锁）
SELECT * FROM users WHERE id = 1 LOCK IN SHARE MODE;

-- 间隙锁（Next-Key Lock）通常出现在范围查询中
SELECT * FROM users WHERE age > 20 FOR UPDATE;


## 📄 10. 视图（VIEW）

CREATE VIEW active_users AS
SELECT id, name FROM users WHERE status = 1;

UPDATE active_users SET name = 'NewName' WHERE id = 1;


## ⚙️ 11. 存储过程（Stored Procedure）

DELIMITER $$
CREATE PROCEDURE GetUserCount()
BEGIN
SELECT COUNT(*) FROM users;
END$$
DELIMITER ;

CALL GetUserCount();


## 🚀 12. SQL 优化技巧（重点面试题）

-- 尽量避免 SELECT *
-- 使用覆盖索引减少回表
-- 对高频过滤字段建索引
-- WHERE 中不要使用函数，如 DATE(created_at)
-- 深分页 LIMIT 100000,10 性能差，推荐用 “延迟关联”
-- 使用 EXPLAIN 分析执行计划
-- 合理使用复合索引提升查询效率
