# SQL行-列转换



## 基础

* [Oracle / PLSQL: PIVOT Clause](https://www.techonthenet.com/oracle/pivot.php "")



## 行转列

### [Convert Rows into Columns or Transpose Rows to Columns In Oracle SQL - Alien Coders](http://www.aliencoders.org/content/convert-rows-columns-or-transpose-rows-columns-oracle-sql/ "")

http://sqlfiddle.com/#!4/587db6/8

DDL

```sql
CREATE TABLE yr_table (
  Roll_No NUMBER,
  Subject VARCHAR2(20),
  Marks   NUMBER
);

insert into yr_table VALUES (1212324,'MTH',90);
insert into yr_table VALUES (1212324,'PHY',72);
insert into yr_table VALUES (1212324,'CHE',85);
insert into yr_table VALUES (1212324,'BIO',78);
insert into yr_table VALUES (1212334,'MTH',85);
insert into yr_table VALUES (1212334,'PHY',65);
insert into yr_table VALUES (1212334,'CHE',74);
insert into yr_table VALUES (1212672,'MTH',88);
insert into yr_table VALUES (1212672,'PHY',42);
insert into yr_table VALUES (1212672,'BIO',12);
COMMIT;
```

```sql
-- Method 1 DECODE
SELECT ROLL_NO,
       MAX( DECODE( SUBJECT , 'MTH' , MARKS ) ) AS MTH,
       MAX( DECODE( SUBJECT , 'PHY' , MARKS ) ) AS PHY,
       MAX( DECODE( SUBJECT , 'CHE' , MARKS ) ) AS CHE,
       MAX( DECODE( SUBJECT , 'BIO' , MARKS ) ) AS BIO
  FROM yr_table
 GROUP BY ROLL_NO ORDER BY 1;
 
-- intermediate
SELECT ROLL_NO,
       DECODE( SUBJECT , 'MTH' , MARKS )AS MTH,
       DECODE( SUBJECT , 'PHY' , MARKS )AS PHY,
       DECODE( SUBJECT , 'CHE' , MARKS )AS CHE,
       DECODE( SUBJECT , 'BIO' , MARKS )AS BIO
  FROM yr_table;

-- Method 2 Case When (like DECODE)

SELECT ROLL_NO,
       MAX( CASE WHEN SUBJECT = 'MTH' THEN MARKS END) MTH ,
       MAX( CASE WHEN SUBJECT = 'PHY' THEN MARKS END) PHY ,
       MAX( CASE WHEN SUBJECT = 'CHE' THEN MARKS END) CHE ,
       MAX( CASE WHEN SUBJECT = 'BIO' THEN MARKS END) BIO
  FROM yr_table
 GROUP BY ROLL_NO ORDER BY 1 ;
```



## 列转行





##  矩阵转置

* [    How can I make a Matrix Transposition in SQL ?](https://social.msdn.microsoft.com/Forums/sqlserver/en-US/e6c57f76-9a91-4579-bb41-af4e51b4a548/how-can-i-make-a-matrix-transposition-in-sql-?forum=transactsql "")

在线测试：http://sqlfiddle.com/#!4/be15f/3

```text
From:
1 2 3 
4 5 6 
7 8 9 

to:
1 4 7 
2 5 8 
3 6 9 
```

测试DDL：

```sql
create table matrix_transpose
(
    pkey    varchar(10) primary key,
    col1    varchar(10),
    col2    varchar(10),
    col3    varchar(10)
);


insert into matrix_transpose (pkey, col1, col2, col3)
select 'a','1','2','3' from dual
union all 
select 'b','4','5','6' from dual
union all
select 'c','7','8','9' from dual;
```

转换SQL：

```sql
SELECT id, 
  MIN(CASE WHEN P.pkey = 'a' THEN col1 END) AS Col1,
  MIN(CASE WHEN P.pkey = 'b' THEN col1 END) AS Col2,
  MIN(CASE WHEN P.pkey = 'c' THEN col1 END) AS Col3
FROM
 (SELECT 'Col 1' as id, pkey, col1 FROM matrix_transpose
  UNION ALL
  SELECT 'Col 2' as id, pkey, col2 FROM matrix_transpose
  UNION ALL
  SELECT 'Col 3' as id, pkey, col3 FROM matrix_transpose) P
GROUP BY id
ORDER BY id;
```



普通矩阵转置：http://sqlfiddle.com/#!4/d78bc/4

```plsql
create table textfields
(
    seq    varchar(10) primary key,
    textfield1    varchar(10),
    textfield2    varchar(10),
    textfield3    varchar(10),
	textfield4    varchar(10),
	textfield5    varchar(10),
	textfield6    varchar(10)
);

insert into textfields (seq, textfield1, textfield2, textfield3, textfield4, textfield5, textfield6)
select 13,'1','2','3', '4', null, '6' from dual
union all 
select 14,'4','5','6', null, '7', '6' from dual
union all
select 15,'7','8','9', null, null, '6' from dual
union all
select 16,'7','8','9', '7', '8', '6' from dual
union all
select 17,'7','8','9', '9', '9', '6' from dual
;

SELECT col_id, 
  MIN(CASE WHEN P.seq = 13 THEN tf_value END) AS Col1,
  MIN(CASE WHEN P.seq = 14 THEN tf_value END) AS Col2,
  MIN(CASE WHEN P.seq = 15 THEN tf_value END) AS Col3,
  MIN(CASE WHEN P.seq = 16 THEN tf_value END) AS Col4,
  MIN(CASE WHEN P.seq = 17 THEN tf_value END) AS Col5
FROM
 (SELECT 'textfield1' as col_id, seq, textfield1 tf_value FROM textfields
  UNION ALL
  SELECT 'textfield2' as col_id, seq, textfield2 tf_value FROM textfields
  UNION ALL
  SELECT 'textfield3' as col_id, seq, textfield3 tf_value FROM textfields
  UNION ALL
  SELECT 'textfield4' as col_id, seq, textfield4 tf_value FROM textfields
  UNION ALL
  SELECT 'textfield5' as col_id, seq, textfield5 tf_value FROM textfields
  UNION ALL
  SELECT 'textfield6' as col_id, seq, textfield6 tf_value FROM textfields) P
GROUP BY col_id
ORDER BY col_id;
```



## SQL矩阵

* [Matrix Math in SQL - Simple Talk](https://www.red-gate.com/simple-talk/sql/t-sql-programming/matrix-math-in-sql/ "")

