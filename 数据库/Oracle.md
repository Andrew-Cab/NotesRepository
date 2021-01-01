# 一.数据库管理

使用时只需开启**OracleServiceORCL**和**OracleOraDb10g_home1TnSListener**服务

> 用户

MySQL操作表的基本单位是**数据库**，而Oracle操作表的基本单位是**用户**

> 实例

Oracle数据库是一个操作系统，可以看作Oracle就只有一个大数据库，而实例相当于是Oracle的进程，一个Oracle可以有多个实例

## 1.数据的增删改

==Oracle事务默认手动提交，执行增删改都需要提交事务==

> **添加一条记录**

**INSERT INTO 表名 (字段名1，字段名2) VALUES (值1，值2);**

**commit;**

```sql
INSERT INTO person (pid,name) VALUES (1,'张三');
commit;
```



> **修改一条记录**

**UPDATE 表名 SET 字段名='值' WHERE 条件;**

**commit;**

```sql
UPDATE person SET pname='李四' WHERE pid = 1;
commit;
```



> **删除**

1. 删除表中全部记录
   **DELETE FROM 表名;**
2. 删除表结构
   **DROP TABLE 表名:**
3. 先删除表，再创建一个结构相同的空表
   **TRUNCATE TABLE 表名:**



> **序列**

==Oracle没有主键，通过序列来达到非空自增长的效果，默认从1开始一次递增，序列不属于任何一张表，但是可以逻辑和表做绑定==

1. 创建序列
   **CREATE SEQUENCE 序列名**
2. 查看序列
   **SELECT 序列名.currval FROM dual**
   ==注：==*currval*是序列的属性，表示序列当前值，*nextval*属性表示序列的下一个值（即序列值加1），**dual**是一个虚表，Oracle查询语句必须有**from**，当查询不需要表但Oracle必须加**from**时，可以使用虚表，虚表没有意义，只是补全语法

```sql
CREATE SEQUENCE p_sequence
INSERT INTO person (pid,pname) VALUES (p_sequence.nextval,'张三');
--如果执行插入语句后没有提交数据而是回滚了，那么序列中的值依然会增加
commit;
```

## 2.数据查询

###2.1单行函数

> **字符函数**

1. upper('字符串')：将字符串转为大写

2. lower('字符串')：将字符串转为小写

   ```sql
   SELECT lower('HELLO WROLD') FROM dual;
   SELECT upper('hello world') FROM dual;
   ```

> **数值函数**

1. round(小数值 , 小数点后保留位数)：四舍五入

2. trunc(小数值 , 小数点后保留位数)：截取出小数点后该位数的小数值

3. mod(数值1 , 数值2)：取余

   ```sql
   SELECT round(26.16 , 1) FROM dual;
   SELECT truc(25.15 , 1)  FROM dual;
   SELECT mod(23 , 2) FROM dual;
   ```

> **日期函数**

1. sysdate：系统当前日期，年/月/日 时:分:秒

2. months_between(日期1 , 日期2)：计算日期1距离日期2多少个月

   ```sql
   SELECT sysdate-e.hiredate FROM employee e; --员工入职距离当前过了多少天
   SELECT sysdate+1 FROM dual; --明天此刻的时间
   SELECT months_between(sysdate , e.hiredate); --员工入职到现在过了几个月
   ```

> **转换函数**

1. nvl(字段名 , 值)：如果字段中有null值，使用该函数的参数值来取代

   ```sql
   SELECT e.salary*12+nvl(e.bonus) FROM employee e;
   ```

###2.2多行函数

1. count(1)
2. sum(字段名)
3. max(字段名)
4. min(字段名)
5. avg(字段名)

### 2.3条件表达式

### 2.5分组查询

```sql
SELECT e.department_id,avg(e.salary)
FROM employee e
GROUP BY e.department_id;
```

### 2.6分页查询

**rownum：rownum**是Oracle数据库的一个关键字。当执行查询语句时，按照sql语句的执行顺序执行完*SELECT*后，会为每一行数据加上一个行号，行号从1开始递增，因为*ORDER BY*语句在*SELECT*语句后执行，而执行完*SELECT*后行号就已经被加上了，所以如果此时再执行*ORDER BY*语句就会按照*ORDER BY*指定的排序规则显示，而不是行号递增的显示。

```sql
SELECT ROWNUM, e.* FROM employee e ORDER BY e.salary DESC;
--此时查询结果按照薪水降序排列

SELECT ROWNUM, t.* FROM(
	SELECT ROWNUM, e.* FROM employee e ORDER BY e.salary DESC;
) t;
--此时查询结果中有两列ROWNUM，内层ROWNUM因为子查询按照salary降序导致行号混乱，而外层ROWNUM是在外层SELECT执行后加入的，所以外层行号是从1开始递增
```

## 3.视图

1. 创建视图：
   **CREATE VIEW 视图名 AS 查询语句**

   ```sql
   CREATE VIEW e_view AS SELECT ename, job FROM employee;
   ```

2. 使用视图：
   **SELECT * FROM 视图名**

   ```sql
   SELECT * FROM e_view;
   ```

3. 修改视图：
   **UPDATE 视图名 SET 字段名= '值' 条件**

   ```sql
   UPDATE e_view SET job= 'cashier' WHERE ename='Jack';
   commit;
   ```

   ==视图不存储表的数据，只存储查询的逻辑语句，如果修改视图，则会修改表中的数据，如果表中数据修改，视图的数据也会修改==

4. 只读视图创建：
   **CREATE VIEW 视图名 AS 查询语句 WITH READ ONLY**

   ```sql
   CREATE VIEW e_view AS SELECT ename, job FROM employee WITH READ ONLY;
   ```

## 4.索引

==索引大幅增加查询效率，但会降低增删改的效率，因为每次增删改都需要重构二叉树==

1. 单列索引
   **CREATE INDEX 索引名称 ON 表名(字段名)**

   ```sql
   CREATE INDEX idx_ename ON employee(ename);
   ```

   **单行索引的触发规则：**只有当条件是索引列的原始值时才会触发，单行函数，模糊查询都会导致索引失效

   ```sql
   SELECT * FROM employee WHERE ename='SCOTT';
   ```

2. 复合索引
   **CREATE INDEX 索引名称 ON 表名(字段名1 , 字段名2)**

   ```sql
   CREATE INDEX idx_ename_job ON employee(ename , job);
   ```

   **复合索引的触发规则：**复合索引中第一列为优先检索列，如果要触发复合索引，查询条件必须包含优先检索列中的原始值

   ```sql
   SELECT * FROM employee WHERE ename='SCOTT' AND job='cashier' --触发复合索引
   SELECT * FROM employee WHERE ename='SCOTT' OR job='cashier' --不触发复合索引，因为OR关键字相当于两个条件，而其中一个条件没有优先检索列的值，所以不触发
   ```

   

