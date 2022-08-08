# MySQL

@[toc]
## 环境说明

mysql5.14社区版免费

navicat15

安装视频教程：https://www.bilibili.com/video/BV1H44y1W7F9?spm_id_from=333.337.search-card.all.click&vd_source=a25fe0c73eb730c360192de341c08931

链接：https://pan.baidu.com/s/1sKIpLTt2Sy-qAmOTdOqncw 
提取码：kksk



## 创建数据库

```mysql
# 演示数据库操作
# 创建一个whm_db01数据库 默认校对规则是utf8_generate_ci
CREATE DATABASE whm_db01;

# 删除数据库指令
DROP DATABASE whm_db01;

# 创建一个使用utf8字符集的whm_db02数据库
CREATE DATABASE whm_db02 CHARACTER SET utf8;

# 创建一个使用utf8字符集，并带校验规则的whm_db03数据库
CREATE DATABASE whm_db03 CHARACTER SET utf8 COLLATE utf8_bin;

# 查表中元素 utf8_bin区分大小写， 默认校对规则是utf8_generate_ci不区分
SELECT * 
	FROM t1 
	WHERE NAME = 'TOM'
```



## 查询数据库

```mysql
# 显示数据库语句
SHOW DATABASES;

# 显示创建数据库创建语句
SHOW CREATE DATABASE `whm_db01`;
# 在创建数据库，表的时候，为了规避关键字，可以使用反引号解决 ` `
# 数据库删除语句
DROP DATABASE `whm_db01`
```



## 备份和恢复数据库

备份数据库（注意在DOS执行）命令行

mysqldump -u 用户名 -p -B 数据库1 数据库2 数据库n > 文件名.sql



恢复数据库（注意进入Mysql命令行再执行）

Source 文件名.sql



第二种恢复方法，直接讲bak.sql直接粘贴到查询编辑器中执行



mysqldump -u 用户名 -p  数据库1 表1...表n > 文件名.sql



## 创建表

CREATE TABLE table_name(

field1 datatype,

field2 datatype,

field3 datatype

)character set字符集 collate校对规则 engine存储引擎

field指定列名 datatype指定列类型

如果varchar不够用，可以使用text 还不够使用mediumtext 还不够使用longtext



## 修改表

增删修改表的列：

ALTER TABLE tablename

ADD (column datatype);

MODIFY (column datatype);

DROP (column);

```mysql
# 修改表的操作练习
DESC employee; # 显示表结构
ALTER TABLE `emp` ADD image VARCHAR(32) NOT NULL DEFAULT '' AFTER RESUME;

ALTER TABLE `emp` MODIFY job VARCHAR(60) NOT NULL DEFAULT '';

ALTER TABLE `emp` DROP sex;

RENAME TABLE `emp` TO employee;

ALTER TABLE employee CHARACTER set utf8;

ALTER TABLE employee CHANGE NAME user_name VARCHAR(32) NOT NULL DEFAULT '';
```



### insert语句

```mysql
# 练习insert语句
CREATE TABLE `goods`(
	id INT,
	goods_name VARCHAR(10),
	price DOUBLE
);

INSERT INTO `goods` (id, goods_name, price)
	VALUE(10, 'huawei', 2000);
	
INSERT INTO `goods` (id, goods_name, price)
	VALUE(10, 'apple', 3000);
	
SELECT * FROM `goods`
```



### update语句

```mysql
-- 演示update语句
-- 如果没有带where条件会修改所有的记录
UPDATE `employee` SET Salary = 5000;
SELECT * FROM `employee`;
UPDATE `employee`
	SET Salary = 3000
	WHERE user_name = 'whm';

INSERT INTO `employee` VALUES (200, 'www', '1990-11-11', '2000-11-11', '后端', 5000, 'java', 'D:\\a.jpg');
DESC employee

UPDATE `employee`
	SET Salary = Salary + 1000, job = '全栈'
	WHERE user_name = 'www';
```



### delete语句

```mysql
-- DELETE 语句练习
DELETE FROM employee
	WHERE user_name = 'www';
	
SELECT * FROM employee;
```



### select语句

```mysql
-- select 语句（重点）
CREATE TABLE student(
	id INT NOT NULL DEFAULT 1,
	NAME VARCHAR(20) NOT NULL DEFAULT '',
	chinese FLOAT NOT NULL DEFAULT 0.0,
	english FLOAT NOT NULL DEFAULT 0.0,
	math FLOAT NOT NULL DEFAULT 0.0
);

INSERT INTO student VALUES(1, 'a', 10, 20, 30);
INSERT INTO student VALUES(2, 'b', 10, 20, 30);
INSERT INTO student VALUES(3, 'c', 10, 20, 30);
INSERT INTO student VALUES(4, 'd', 10, 20, 30);
INSERT INTO student VALUES(5, 'e', 10, 20, 30);
INSERT INTO student VALUES(6, 'f', 10, 20, 30);

SELECT * FROM student;

SELECT `NAME`, english FROM student;

-- 去重
SELECT DISTINCT english FROM student;
```

#### as

```mysql
-- select语句的使用
-- 统计每个学生的总分
SELECT `name`, (chinese+english+math) FROM student;

SELECT `name`, (chinese+english+math+10) FROM student;

SELECT `name` as '名字', (chinese+english+math+10) AS total_score FROM student;
```

#### WHERE

```mysql
-- select语句
SELECT * FROM student
	WHERE `name` = 'a';
	
SELECT * FROM student
	WHERE english >= 20
	
SELECT * FROM student
	WHERE (chinese + math + english) >= 50
	
SELECT * FROM student
	WHERE math >= 20 AND id > 4
	
SELECT * FROM student
	WHERE english >= chinese AND id > 4
	
SELECT * FROM student
	WHERE (chinese + math + english) >= 50 and chinese < english and `name` LIKE	'a%'
	
SELECT * FROM student
	WHERE english BETWEEN 10 AND 20
	
```

#### order by      like

```mysql
-- 演示order by使用
-- 默认升序
SELECT * FROM student
	ORDER BY math;

UPDATE student SET math = 20 WHERE id = 1;

SELECT `name` , (chinese + english + math) AS total_score FROM student
	ORDER BY total_score DESC;
	
SELECT `name` , (chinese + english + math) AS total_score FROM student
	WHERE `name` like 'a%'
	ORDER BY total_score DESC;
	
DESC student;
INSERT INTO student(id, `name`, chinese, english, math) VALUES (7, 'aa', 20, 30, 20);
```

#### COUNT SUM AVG MAX MIN

```mysql
-- mysql统计函数的使用 COUNT SUM AVG MAX MIN
SELECT COUNT(*) FROM student;

SELECT COUNT(*) FROM student
	WHERE math > 20;
	
SELECT COUNT(*) FROM student
	WHERE (chinese + english + chinese) > 20;
	
CREATE TABLE t15(
	`name` VARCHAR(20));
INSERT INTO t15 VALUES('tom');
INSERT INTO t15 VALUES('jack');
INSERT INTO t15 VALUES('mary');
INSERT INTO t15 VALUES(NULL);

SELECT * FROM t15;

SELECT COUNT(*) FROM t15;# 4

SELECT COUNT(`name`) FROM t15;# 3

SELECT SUM(math) FROM student;

SELECT SUM(math) AS math_total_score, SUM(english), SUM(chinese) FROM student;

SELECT SUM(math + english + chinese) FROM student;

SELECT SUM(math) / count(*) FROM student;

SELECT AVG(math) FROM student;

SELECT AVG(math + english + chinese) FROM student;

SELECT MAX(math + english + chinese), MIN(math + english + chinese) FROM student;

SELECT MAX(math) AS math_high_score, MIN(math) AS math_low_score FROM student;
```

#### group by + having

```mysql
-- 演示group by + having
CREATE TABLE dept(
deptno MEDIUMINT UNSIGNED NOT NULL DEFAULT 0,
dname VARCHAR(20) NOT NULL DEFAULT "",
loc VARCHAR(13) NOT NULL DEFAULT "");

INSERT INTO dept VALUES(10, 'ACCOUNTING', 'NEW YORK'), (20, 'RESEARC', 'DALLAS'), (30, 'SALES', 'CHICAGO'), (40, 'OPERATIONS', 'BOSTON');

SELECT * FROM dept;


CREATE TABLE emp
(empno MEDIUMINT UNSIGNED NOT NULL DEFAULT 0,# /编号/
ename VARCHAR(20) NOT NULL DEFAULT "", #/名字/
job VARCHAR(9) NOT NULL DEFAULT "",#/工作/
mgr MEDIUMINT UNSIGNED ,#/上级编号/
hiredate DATE NOT NULL,#/入职时间/
sal DECIMAL(7,2) NOT NULL,#/薪水/
comm DECIMAL(7,2) ,#/红利 奖金/
deptno MEDIUMINT UNSIGNED NOT NULL DEFAULT 0 #/部门编号/);

INSERT INTO emp VALUES(7369, 'SMITH', 'CLERK', 7902, '1990-12-17', 800.00,NULL , 20),
(7499, 'ALLEN', 'SALESMAN', 7698, '1991-2-20', 1600.00, 300.00, 30),
(7521, 'WARD', 'SALESMAN', 7698, '1991-2-22', 1250.00, 500.00, 30),
(7566, 'JONES', 'MANAGER', 7839, '1991-4-2', 2975.00,NULL,20),
(7654, 'MARTIN', 'SALESMAN', 7698, '1991-9-28',1250.00,1400.00,30),
(7698, 'BLAKE','MANAGER', 7839,'1991-5-1', 2850.00,NULL,30),
(7782, 'CLARK','MANAGER', 7839, '1991-6-9',2450.00,NULL,10),
(7788, 'SCOTT','ANALYST',7566, '1997-4-19',3000.00,NULL,20),
(7839, 'KING','PRESIDENT',NULL,'1991-11-17',5000.00,NULL,10),
(7844, 'TURNER', 'SALESMAN',7698, '1991-9-8', 1500.00, NULL,30),
(7900, 'JAMES','CLERK',7698, '1991-12-3',950.00,NULL,30),
(7902, 'FORD', 'ANALYST',7566,'1991-12-3',3000.00, NULL,20),
(7934,'MILLER','CLERK',7782,'1992-1-23', 1300.00, NULL,10);

SELECT * FROM emp;


#工资级别表
 
CREATE TABLE salgrade(
grade MEDIUMINT UNSIGNED NOT NULL DEFAULT 0, /* 工资级别 */
losal DECIMAL(17,2) NOT NULL, /* 该级别的最低工资 */
hisal DECIMAL(17,2) NOT NULL /* 该级别的最高工资*/);
 
INSERT INTO salgrade VALUES (1,700,1200);
INSERT INTO salgrade VALUES (2,1201,1400);
INSERT INTO salgrade VALUES (3,1401,2000);
INSERT INTO salgrade VALUES (4,2001,3000);
INSERT INTO salgrade VALUES (5,3001,9999);

SELECT * FROM salgrade;

SELECT AVG(sal), MAX(sal), deptno FROM emp
	GROUP BY deptno;

SELECT AVG(sal), MIN(sal), deptno, job FROM emp
	GROUP BY deptno, job;

-- 写法1
SELECT AVG(sal), deptno FROM emp
	GROUP BY deptno
	having AVG(sal) < 2000

-- 写法2
SELECT AVG(sal) AS avg_sal, deptno FROM emp
	GROUP BY deptno
	having avg_sal < 2000
```

#### 数学相关函数

```mysql
-- 演示数学相关函数
-- 绝对值
SELECT ABS(-10) FROM DUAL;

SELECT BIN(10) FROM DUAL;

SELECT CEILING(-1.1) FROM DUAL;

SELECT CONV(10,10,2) FROM DUAL;

SELECT CONV(10,16,10) FROM DUAL;

SELECT FLOOR(1.1) FROM DUAL;

SELECT FORMAT(78.12345,4) FROM DUAL;

SELECT LEAST(0.1,-1,4) FROM DUAL;

SELECT MOD(10,2) FROM DUAL;

SELECT RAND(3) FROM DUAL;
```

#### 日期函数

```mysql
-- 创建测试表
CREATE TABLE mes(
 id INT,
 content VARCHAR(30),
 send_time DATETIME);
 
SELECT * FROM mes;

INSERT INTO mes VALUES (1, "北京新闻", CURRENT_TIMESTAMP);

INSERT INTO mes VALUES (2, "上海新闻", now());

INSERT INTO mes VALUES (2, "广州新闻", now());

UPDATE mes SET id = 3 WHERE content = "广州新闻";

SELECT id, content, DATE(send_time) FROM mes;

-- 查询10分钟内发布的新闻
SELECT * FROM mes WHERE DATE_ADD(send_time, INTERVAL 10 MINUTE) >= NOW();

SELECT * FROM mes WHERE DATE_SUB(NOW(), INTERVAL 20 MINUTE) <= send_time;

SELECT DATEDIFF('2011-11-11','1990-1-1') FROM DUAL;

SELECT DATEDIFF(DATE_ADD('1986-11-11',INTERVAL 80 YEAR), NOW()) FROM DUAL;

SELECT TIMEDIFF('10:11:11','06:10:10') FROM DUAL;


SELECT YEAR(NOW()) FROM DUAL;
SELECT MONTH(NOW()) FROM DUAL;
SELECT DAY(NOW()) FROM DUAL;

-- 返回1970-1-1到现在的秒数
SELECT UNIX_TIMESTAMP() FROM DUAL;
-- 意义：在开发中，可以存放一个整数，然后表示时间
SELECT FROM_UNIXTIME(1656850057, '%Y-%m-%d') FROM DUAL;

SELECT FROM_UNIXTIME(1656850057, '%Y-%m-%d %H:%i:%s') FROM DUAL;
```



## 加密函数和系统函数

```mysql
-- 查看当前登录到mysql有哪些用户
SELECT USER() FROM DUAL;

-- 查询当前使用数据库名称
SELECT DATABASE() FROM DUAL;

-- 加密md5
SELECT MD5('Whmhhh1998818!') FROM DUAL;
SELECT LENGTH(MD5('whm!')) FROM DUAL;

DROP TABLE `user`;
CREATE TABLE `user`(
	id INT,
	NAME VARCHAR(32) NOT NULL DEFAULT "",
	pwd CHAR(32) NOT NULL DEFAULT "");
	
INSERT INTO `user` VALUES(100, "whm", MD5('whm'));
SELECT * FROM `user`;

SELECT *
	FROM `user`
	WHERE `name` = 'whm' and pwd = MD5('whm')
	
SELECT PASSWORD('whm') FROM DUAL;

SELECT * FROM mysql.`user`
```



## 流程控制语句

```mysql
SELECT IF(TRUE,"北京","上海") FROM DUAL;

SELECT IFNULL(NULL,"whm") FROM DUAL;

SELECT CASE
	WHEN TRUE THEN
		'Jack'
	WHEN FALSE THEN
		'Tom'
	ELSE
		'mary'
END CASE;

SELECT ename, IF(comm IS NULL, 0.0, comm)
	FROM emp;
	
SELECT ename, IFNULL(comm, 0.0)
	FROM emp;
	
SELECT ename, (SELECT CASE
	WHEN job = 'CLERK' THEN
		'职员'
	WHEN job = 'MANAGER' THEN
		'经理'
	ELSE
		'销售人员'
END) AS 'job'
	FROM emp;
```



## 查询加强

```mysql
SELECT * FROM emp
	WHERE hiredate > "1992-01-01"

-- %表示0到多个任意字符 _表示单个任意字符
SELECT ename, sal FROM emp
	WHERE ename LIKE 'S%'
	
SELECT ename, sal FROM emp
	WHERE ename LIKE '__O%'
	
SELECT * FROM emp
	WHERE mgr IS NULL;
	
DESC emp;

SELECT * FROM emp
	ORDER BY sal
	
SELECT * FROM emp
	ORDER BY deptno ASC, sal DESC
```



## 分页查询

```mysql
SELECT * FROM emp
	ORDER BY empno
	LIMIT 0, 3;

SELECT * FROM emp
	ORDER BY empno
	LIMIT 3, 3;
	
SELECT * FROM emp
	ORDER BY empno
	LIMIT 6, 3;
	
SELECT * FROM emp
	ORDER BY empno
	LIMIT 9, 3;

SELECT * FROM emp
	ORDER BY empno
	LIMIT 12, 3;
```



## 增强group by使用

```mysql
SELECT COUNT(*), AVG(sal), job
	FROM emp
	GROUP BY job;

SELECT COUNT(*), COUNT(comm)
	FROM emp;
	
SELECT COUNT(*), COUNT(IF(comm IS NULL, 1, NULL))
	FROM emp;
	
SELECT COUNT(*), COUNT(*) - COUNT(comm)
	FROM emp;
	
SELECT COUNT(DISTINCT(mgr))
	FROM emp;
	
SELECT MAX(sal) - MIN(sal)
	FROM emp;

# 注意顺序
SELECT deptno, AVG(sal) AS avg_sal
	FROM emp
	GROUP BY deptno
	HAVING avg_sal > 1000
	ORDER BY avg_sal DESC
	LIMIT 0, 2;
```



## 多表查询

```mysql
SELECT * FROM emp;
SELECT * FROM dept;
SELECT * FROM emp, dept;

SELECT ename, sal, dname, dept.deptno FROM emp, dept
	WHERE emp.deptno = dept.deptno;
	
SELECT ename, sal, dname, dept.deptno FROM emp, dept
	WHERE emp.deptno = dept.deptno AND dept.deptno = 10;
	
SELECT ename, sal, grade FROM emp, salgrade
	WHERE emp.sal BETWEEN salgrade.losal AND salgrade.hisal;
```



### 自连接

```mysql
-- 多表查询的自连接
-- 显示公司员工名称和他上级的名字
SELECT worker.ename AS '职工名', boss.ename AS '上级名'
	FROM emp AS worker, emp AS boss
	WHERE worker.mgr = boss.empno;
```



### 子查询演示 IN关键字

```mysql
-- smith同一个部门的
SELECT deptno FROM emp
	WHERE ename = 'SMITH';

SELECT * FROM emp
	WHERE deptno = (SELECT deptno FROM emp
									WHERE ename = 'SMITH');
						
SELECT DISTINCT(job) FROM emp
	WHERE deptno = 10;

SELECT ename, job, sal, deptno FROM emp
	WHERE job in (SELECT DISTINCT(job) FROM emp
								WHERE deptno = 10) AND deptno != 10;
```



### any 和 all关键字

```mysql
SELECT ename, sal, deptno
	FROM emp
	WHERE sal > (SELECT MAX(sal) FROM emp WHERE deptno=30);

SELECT ename, sal, deptno
	FROM emp
	WHERE sal > ALL(SELECT sal FROM emp WHERE deptno=30);

SELECT ename, sal, deptno
	FROM emp
	WHERE sal > ANY(SELECT sal FROM emp WHERE deptno=30);
```





### 多列子查询

```mysql
SELECT deptno, job
	FROM emp
	WHERE ename = "SMITH"

SELECT * FROM emp
	WHERE (deptno, job) = (SELECT deptno, job
												FROM emp
												WHERE ename = "SMITH") AND ename != "SMITH";
												
SELECT * FROM emp
	WHERE deptno = (SELECT deptno
												FROM emp
												WHERE ename = "SMITH" )
			AND job = (SELECT job
												FROM emp
												WHERE ename = "SMITH" );
```



### 子查询练习

```mysql
SELECT deptno, AVG(sal) FROM emp
	GROUP BY deptno;

SELECT ename, sal, temp.avg_sal, emp.deptno FROM emp, (SELECT deptno, AVG(sal) AS avg_sal FROM emp
											GROUP BY deptno) AS temp
				WHERE emp.deptno = temp.deptno AND emp.sal > temp.avg_sal;
				
SELECT ename, sal, temp.max_sal, emp.deptno FROM emp, (SELECT deptno, MAX(sal) AS max_sal FROM emp
											GROUP BY deptno) AS temp
				WHERE emp.deptno = temp.deptno AND emp.sal = temp.max_sal;
				
SELECT dname, temp.deptno, loc, temp.pALL FROM (SELECT deptno, count(*) AS pALL FROM emp GROUP BY deptno) AS temp, dept
	WHERE temp.deptno = dept.deptno;
-- 或
SELECT temp.*, dname, loc FROM (SELECT deptno, count(*) AS pALL FROM emp GROUP BY deptno) AS temp, dept
	WHERE temp.deptno = dept.deptno;
```



### 表的复制

```mysql
CREATE table my_table01(
	id INT,
	`name` VARCHAR(32),
	sal DOUBLE,
	job VARCHAR(32),
	deptno INT);
	
SELECT * FROM my_table01;

INSERT INTO my_table01 (id, `name`, sal, job, deptno) SELECT empno, ename, sal, job, deptno FROM emp;

-- 自我复制 成指数次增长
INSERT INTO my_table01 SELECT * FROM my_table01;

-- 删除一张表重复数据
CREATE TABLE my_table02 LIKE emp;# 这个语句把emp表结构 复制到my_table02

SELECT * FROM my_table02;

INSERT INTO my_table02 SELECT * FROM emp;

CREATE TABLE my_tmp LIKE my_table02;

INSERT INTO my_tmp SELECT DISTINCT * FROM my_table02;

DELETE FROM my_table02;

INSERT INTO my_table02 SELECT * FROM my_tmp;

DROP TABLE my_tmp;

SELECT * FROM my_table02;
```



### 合并查询

```mysql
SELECT ename, sal, job FROM emp WHERE sal > 2500;

SELECT ename, sal, job FROM emp WHERE job = "MANAGER";

-- UNION ALL合并不会去除
SELECT ename, sal, job FROM emp WHERE sal > 2500
UNION ALL
SELECT ename, sal, job FROM emp WHERE job = "MANAGER";

-- UNION合并会去除
SELECT ename, sal, job FROM emp WHERE sal > 2500
UNION
SELECT ename, sal, job FROM emp WHERE job = "MANAGER";

SELECT ename, sal, job FROM emp WHERE sal > 2500 OR job = "MANAGER";
```



### 外连接

```mysql
SELECT dname, ename, job FROM emp, dept
	WHERE emp.deptno = dept.deptno
	ORDER BY dname;
	
CREATE TABLE stu(
id INT,
`name` VARCHAR(32));

INSERT INTO stu VALUES(1, "Jack"),(2, "tom"), (3, "kitty"), (4, "nono");

SELECT * FROM stu;

CREATE TABLE exam(
id INT,
grade INT);

INSERT INTO exam VALUES(1, 56),(2, 76), (11, 8);

SELECT * FROM exam;

SELECT `name`, stu.id, grade FROM stu, exam 
	WHERE stu.id = exam.id;
	
-- 	改成左外链接
	SELECT `name`, stu.id, grade FROM stu LEFT JOIN exam 
		ON stu.id = exam.id;
		
-- 	改成右外链接
	SELECT `name`, stu.id, grade FROM stu RIGHT JOIN exam 
		ON stu.id = exam.id;

SELECT dname, ename FROM dept LEFT JOIN emp
	ON dept.deptno = emp.deptno;
```

### 主键使用

```mysql
CREATE TABLE t17(
	id INT PRIMARY KEY,
	`name` VARCHAR(32),
	email VARCHAR(32));
	
-- 主键不能重复，且不能为空
INSERT INTO t17 VALUES(1, 'jack', "1"),(2, "tom", "2"),(1, "whm", "3");

SELECT * FROM t17;

CREATE TABLE t18(
	id INT PRIMARY KEY,
	`name` VARCHAR(32) PRIMARY KEY, # 错误
	email VARCHAR(32));
	
-- 复合主键
CREATE TABLE t18(
	id INT,
	`name` VARCHAR(32),
	email VARCHAR(32),
	PRIMARY KEY(id, `name`));
	
INSERT INTO t18 VALUES(1, 'jack', "1"),(1, "whm", "3");
INSERT INTO t18 VALUES(1, 'jack', "4");

DESC t18;


```



### unique的使用

```mysql
CREATE TABLE t19(
	id INT UNIQUE, # 表示id列是不可以重复的
	`name` VARCHAR(32),
	email VARCHAR(32));
	
INSERT INTO t19 VALUES(1, 'jack', "1"),(1, "whm", "3");

-- 如果没要指定not null则 unique 字段可以有多个null
INSERT INTO t19 VALUES(NULL, 'jack', "1"),(NULL, "whm", "3"), (NULL, 'jack', "4");

SELECT * FROM t19;

-- 一张表可以有多个unique
-- unique + not null = PRIMARY KEY
```



### 外键演示

```mysql
CREATE TABLE my_class(
	id INT PRIMARY KEY,
	`name` VARCHAR(32) NOT NULL DEFAULT " ");

CREATE TABLE my_stu(
	id INT PRIMARY KEY,
	`name` VARCHAR(32) NOT NULL DEFAULT " ",
	class_id INT,
	FOREIGN KEY (class_id) REFERENCES my_class(id));
	
INSERT INTO my_class VALUES(100, "java"), (200, "web");

INSERT INTO my_stu VALUES(1, "tom", 100), (2, "jack", 200), (3, "whm", 300);

SELECT * FROM my_stu;
```



## 演示check的使用

```mysql
-- mysql15.7还不支持check，只做语法校验，但不会生效
CREATE TABLE t20(
	id INT PRIMARY KEY,
	`name` VARCHAR(32),
	sex VARCHAR(6) CHECK (sex IN('man', 'woman')),
	sal DOUBLE CHECK (sal > 1000 AND sal < 2000));
	
INSERT INTO t20 VALUES(1, "jack", "mid", 1);

SELECT * FROM t20;
```



## 使用约束建表

```mysql
CREATE DATABASE shop_db;

CREATE TABLE goods(
​	goods_id INT PRIMARY KEY,
​	goods_name VARCHAR(64) NOT NULL DEFAULT "",
​	unitprice DECIMAL(10, 2) NOT NULL DEFAULT 0
​		CHECK (unitprice >= 1.0 AND unitprice <= 9999.99),
​	category INT NOT NULL DEFAULT 0,
​	provider VARCHAR(64) NOT NULL DEFAULT "");
​	
CREATE TABLE customer(
​	customer_id CHAR(8) PRIMARY KEY,
​	`name` VARCHAR(64) NOT NULL DEFAULT "",
​	address VARCHAR(64) NOT NULL DEFAULT "",
​	emil VARCHAR(64) NOT NULL DEFAULT "",
​	sex ENUM('男', '女') NOT NULL,
​	card_Id CHAR(18));
​	
CREATE TABLE purchase(
​	order_id INT UNSIGNED PRIMARY KEY,
​	customer_id CHAR(18) NOT NULL DEFAULT "",
​	goods_id INT NOT NULL DEFAULT 0,
​	nums INT NOT NULL DEFAULT 0,
​	FOREIGN KEY (customer_id) REFERENCES customer(customer_id),
​	FOREIGN KEY (goods_id) REFERENCES goods(goods_id));
​	
DESC goods;
DESC customer;
DESC purchase;
```



## 演示自增长的使用

```mysql
-- AUTO_INCREMENT只能修饰PRIMARY KEY或unique值
CREATE TABLE t21(
	id INT PRIMARY KEY AUTO_INCREMENT,
	email VARCHAR(32) NOT NULL DEFAULT " ",
	`name` VARCHAR(32) NOT NULL DEFAULT " ");

DESC t21;

INSERT INTO t21 VALUES(NULL, 'jack', "jack");

INSERT INTO t21 VALUES(NULL, 'tom', "tom");

INSERT INTO t21(email, `name`) VALUES('ketty', "ketty");

SELECT * FROM t21;

CREATE TABLE t22(
	id INT PRIMARY KEY AUTO_INCREMENT,
	email VARCHAR(32) NOT NULL DEFAULT " ",
	`name` VARCHAR(32) NOT NULL DEFAULT " ");

-- 自增长从100开始
ALTER TABLE t22 AUTO_INCREMENT 100;

INSERT INTO t22 VALUES(NULL, 'jack', "jack");

SELECT * FROM t22;
```



## 演示mysql索引的使用

```mysql
CREATE TABLE t23(
	id INT,
	`name` VARCHAR(32));
-- 查询索引
SHOW INDEX FROM t23;
SHOW KEYS FROM t23;
-- 添加唯一索引
CREATE UNIQUE INDEX id_index ON t23(id);
-- 添加普通索引方法一
CREATE INDEX id_index ON t23(id);
-- 添加普通索引方法二
ALTER TABLE t23 ADD INDEX id_index(id);

CREATE TABLE t24(
	id INT,
	`name` VARCHAR(32));
ALTER TABLE t24 ADD PRIMARY KEY id_index(id);

SHOW INDEX FROM t24;

-- 删除索引
DROP INDEX id_index ON t23;

ALTER TABLE t24 DROP PRIMARY KEY;
```



## 事务的概念

```mysql
CREATE TABLE t25(
	id INT,
	`name` VARCHAR(32));
	
-- 	开始事务
START TRANSACTION;
-- 设置保存点
SAVEPOINT a;

INSERT INTO t25 VALUES (100, "tom");

SAVEPOINT b;

INSERT INTO t25 VALUES (200, "jack");

SELECT * FROM t25;

ROLLBACK TO b;

ROLLBACK TO a;

ROLLBACK;

INSERT INTO t25 VALUES(300, 'milan'); # 自动commit

ROLLBACK;

START TRANSACTION;

SET autocommit = off;

INSERT INTO t25 VALUES(400, 'king');

INSERT INTO t25 VALUES(500, 'scott');

ROLLBACK; # 直接回退到事务开始的地方
```



## 演示mysql的事务隔离级别

```mysql
SELECT @@tx_isolation
SELECT @@global.tx_isolation


-- +-----------------+
-- | @@tx_isolation  |
-- +-----------------+
-- | REPEATABLE-READ |
-- +-----------------+

-- 把其中一个控制台的隔离级别设置 Read UNCOMMITTED

SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
SET GLOBAL TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;

CREATE TABLE `account`(
	id INT,
	`name` VARCHAR(32),
	money INT);
	
-- +----------------+
-- | @@tx_isolation |
-- +----------------+
-- | READ-COMMITTED |
-- +----------------+

-- +-----------------+
-- | @@tx_isolation  |
-- +-----------------+
-- | REPEATABLE-READ |
-- +-----------------+

-- +----------------+
-- | @@tx_isolation |
-- +----------------+
-- | SERIALIZABLE   |
-- +----------------+
```



## 表类型和存储引擎

```mysql
-- 查看所有的引擎
SHOW ENGINES;

-- INNODB 支持事务/支持外键/支持行级锁

-- MYISAM 添加速度快 不支持外键和事务 支持表级锁
CREATE TABLE t26(
​	id INT,
​	`name` VARCHAR(32)) ENGINE MYISAM;
​	
START TRANSACTION;

SAVEPOINT t1;
INSERT INTO t26 VALUES(1, 'jack');
SELECT * FROM t26;
ROLLBACK TO t1;

-- memory 数据存储在内存中 执行速度很快 内存中没要io 默认支持索引 hash
CREATE TABLE t27(
​	id INT,
​	`name` VARCHAR(32)) ENGINE MEMORY;
INSERT INTO t27 VALUES(1, 'jack'), (2, 'tom'), (3, 'hsp');
SELECT * FROM t27;

-- 指令修改存储引擎
ALTER TABLE t27 ENGINE = INNODB;
```



## 视图

1.视图是根据基表（可以是多个基表）来创建的视图是虚拟的表

2.视图也有列，数据来自基表

3.通过视图可以修改基表的数据

4.基表的改变，也会影响视图的数据

```mysql
-- 视图的使用
CREATE VIEW emp_view01
	AS
	SELECT empno, ename, job, deptno FROM emp;

DESC emp_view01;

SELECT * FROM emp_view01;

SELECT empno, job FROM emp_view01;

SHOW CREATE VIEW emp_view01;

DROP VIEW emp_view01;

-- 修改视图会影响基表
UPDATE emp_view01
	SET job = 'MANAGER'
	WHERE empno = 7369
	
UPDATE emp
	SET job = 'SALESMAN'
	WHERE empno = 7369
	
CREATE VIEW emp_view02
	AS
	SELECT empno, ename FROM emp_view01;
	
SELECT * FROM emp_view02;
```



## 用户权限管理

```mysql
CREATE USER 'member01'@'localhost' IDENTIFIED BY '123';

CREATE DATABASE testdb;
CREATE TABLE news(
​	id INT,
​	content VARCHAR(32));
INSERT INTO news VALUES (100, '北京新闻');

GRANT SELECT, INSERT
​	ON testdb.news
​	TO 'member01'@'localhost';
​	
GRANT UPDATE
​	ON testdb.news
​	TO 'member01'@'localhost';
​	
SET PASSWORD FOR 'member01'@'localhost' = PASSWORD('abc');
-- 回收所有权限
REVOKE ALL ON testdb.news FROM 'member01'@'localhost';

DROP USER 'member01'@'localhost';-- 用户权限管理
CREATE USER 'member01'@'localhost' IDENTIFIED BY '123';

CREATE DATABASE testdb;
CREATE TABLE news(
​	id INT,
​	content VARCHAR(32));
INSERT INTO news VALUES (100, '北京新闻');

GRANT SELECT, INSERT
​	ON testdb.news
​	TO 'member01'@'localhost';
​	
GRANT UPDATE
​	ON testdb.news
​	TO 'member01'@'localhost';
​	
SET PASSWORD FOR 'member01'@'localhost' = PASSWORD('abc');
-- 回收所有权限
REVOKE ALL ON testdb.news FROM 'member01'@'localhost';

DROP USER 'member01'@'localhost';

CREATE USER 'member01'@'192.168.1.%' IDENTIFIED BY '123';
```
















































