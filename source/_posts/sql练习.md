---
title: sql练习
date: 2022-03-05 19:00:45
tags: 技术
categories: 存储
---

## sql引擎解析顺序

> 建议写sql的时候也遵循这个思路去写

![](https://note.youdao.com/yws/api/personal/file/WEB03bfd5ded39d2d70a2fff731150d7430?method=download&shareKey=994056bb5d88d3fec98f15950079cf13)

## 题库

> (sql练习题)[https://blog.csdn.net/baidu_36695217/article/details/79790813] 

```

-- 20、统计列各科成绩,各分数段人数:课程ID,课程名称,[100-85],[85-70],[70-60],[ <60]
select c.cno,c.cname,
sum(case when sc.score BETWEEN 85 and 100 then 1 else 0 end ) as '[100-85]',
sum(case when sc.score BETWEEN 70 and 85 then 1 else 0 end ) as '[85-70]',
sum(case when sc.score BETWEEN 60 and 70 then 1 else 0 end ) as '[70-60]',
sum(case when sc.score BETWEEN 0 and 60 then 1 else 0 end ) as '[60-0]'
from sc 
inner join course c on sc.cno = c.cno
GROUP BY c.cno,c.cname

-- 2. 查询平均成绩大于60 分的同学的学号和平均成绩
select sno,avg(score) from sc group by sno having avg(score)>60;

-- 1、查询“c001”课程比“c002”课程成绩高的所有学生的学号

select a.sno
from 
(select sno,score from sc where cno = 'c001') a
inner join 
(select sno,score from sc where cno = 'c002') b
on a.sno = b.sno and a.score>b.score

select a.sno
from sc a where a.cno = 'c001'
and exists(select * from sc b 
where b.cno = 'c002'  and a.score>b.score and a.sno = b.sno )

```

## 问题汇总

```java
1. drop truncate delete区别

drop table 直接将表空间删除 (删除表结构和数据)
truncate table 清空表并将索引所占的空间恢复初始大小,不可在事务日志中回滚 (只删除数据)
delete 不会减少索引的占用空间,且可被触发器触发,每执行一次都会写到undo log中,可被事务回滚, (只删除数据)

2. exists 和 in (https://www.jianshu.com/p/f212527d76ff)
select * from A where id in (select id from B); -- in是在内存里遍历比较
select * from A where exists (select 1 from B where A.id=B.id); -- exists需要查询数据库

in 相当于两个for循环,对内存需求高,适合外表大而内表小
List resultSet={};
Array A=(select * from A);
Array B=(select id from B);

for(int i=0;i<A.length;i++) {
  for(int j=0;j<B.length;j++) {
      if(A[i].id==B[j].id) {
        resultSet.add(A[i]);
        break;
      }
  }
}
return resultSet;

exsitst 一个for循环,但有io查询影响性能,适合内表大,外包小,且in查询一般不走索引

List resultSet={};
Array A=(select * from A);
for(int i=0;i<A.length;i++) {
   if(exists(A[i].id) {  //执行select 1 from B where B.id=A.id是否有记录返回
       resultSet.add(A[i]);
   }
}
return resultSet;
```

## 附录

### 数据脚本

```
# 插入脚本
CREATE TABLE `course` (
  `cno` varchar(255) NOT NULL,
  `cname` varchar(255) DEFAULT NULL,
  `tno` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`cno`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `sc` (
  `sno` varchar(255) DEFAULT NULL,
  `cno` varchar(255) DEFAULT NULL,
  `score` varchar(255) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `student` (
  `sno` varchar(255) NOT NULL,
  `sname` varchar(255) DEFAULT NULL,
  `sage` varchar(255) DEFAULT NULL,
  `ssex` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`sno`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `teacher` (
  `tno` varchar(255) NOT NULL,
  `tname` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`tno`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

insert into sc values ('s001','c001',78.9);
insert into sc values ('s002','c001',80.9);
insert into sc values ('s003','c001',81.9);
insert into sc values ('s004','c001',60.9);
insert into sc values ('s001','c002',82.9);
insert into sc values ('s002','c002',72.9);
insert into sc values ('s003','c002',81.9);
insert into sc values ('s001','c003',59);
insert into course values ('c001','J2SE','t002');
insert into course values ('c002',' Java  Web','t002');
insert into course values ('c003','SSH','t001');
insert into course values ('c004',' Oracle ','t001');
insert into course values ('c005','SQL SERVER 2005','t003');
insert into course values ('c006','C#','t003');
insert into course values ('c007',' JavaScript ','t002');
insert into course values ('c008','DIV+ CSS ','t001');
insert into course values ('c009',' PHP ','t003');
insert into course values ('c010','EJB3.0','t002');
insert into teacher values ('t001', '刘阳');
insert into teacher values ('t002', '谌燕');
insert into teacher values ('t003', '胡明星');
insert into student values ('s001','张三',23,'男');
insert into student values ('s002','李四',23,'男');
insert into student values ('s003','吴鹏',25,'男');
insert into student values ('s004','琴沁',20,'女');
insert into student values ('s005','王丽',20,'女');
insert into student values ('s006','李波',21,'男');
insert into student values ('s007','刘玉',21,'男');
insert into student values ('s008','萧蓉',21,'女');
insert into student values ('s009','陈萧晓',23,'女');
insert into student values ('s010','陈美',22,'女');
```

#### 基础命令

```sql
# DML:数据操作语句
INSERT INTO mytable(col1, col2) VALUES(val1, val2);
INSERT INTO mytable1(col1, col2) SELECT col1, col2 FROM mytable2;
CREATE TABLE newtable AS SELECT * FROM mytable;
	
UPDATE mytable SET col = val,col2=val WHERE id = 1;

truncate TABLE mytable; #以清空表，也就是删除所有行

# DQL:数据查询语句
SELECT DISTINCT col1, col2 
   FROM mytable  
   WHERE col LIKE 'AB%' 
   ORDER BY col1 DESC, col2 ASC 
   LIMIT a, b;# 返回int (a+1) ~ (a+b)行

# GROUP BY
# (除汇总字段外)select的每个字段都需在group by中给出
# NULL 的行会单独分为一组,GROUP BY不支持可变长度的数据类型

SELECT col, COUNT(1) AS num 
FROM mytable 
WHERE col > 2 
GROUP BY col HAVING num >= 2 
ORDER BY num desc;


# 时间函数
select now();
## 最后一栏可填SECONDS,MINUTES HOURS DAYS
DATE_SUB(date,INTERVAL '1:30' MINUTE_SECOND)  #表示减掉1分30秒
DATE_ADD(date,INTERVAL 60 SECOND)  #表示加上60秒

# 条件函数
SELECT IF(1 > 0,'正确','错误')--->正确

SELECT CASE 
            　　WHEN 1 > 0
            　　THEN '1 > 0'
            　　WHEN 2 > 0
            　　THEN '2 > 0'
            　　ELSE '3 > 0'
            　　END   --->1 > 0
# 字符串函数
CONCAT(s1,s2,......)合并字符串函数
GROUP_CONCAT()，将部分字符按照分组链接起来
TRIM(S)  清除空格
SYBSTRING(S,N,LEN),截取字符串函数

# exists() --> return Boolean
将外查询表的每一行，代入exists内查询作为检验,若存在则展示结果
在子查询中使用 NULL 仍然返回结果集


## 内连接:等值连接,无条件语句下返回笛卡尔积
select * from t1 inner join t2 on t1.C=t2.C;
	+---+---+---+---+---+---+
	| A | B | C | C | D | E |
	+---+---+---+---+---+---+
	| 1 | 2 | 3 | 3 | 4 | 5 |
	+---+---+---+---+---+---+
	
## 外连接:返回左边所有的记录和右表中连接字段相等的记录
select t1.A,t1.B,t1.C,t2.D,t2.E from t1 left join t2 on t1.C=t2.C;
	+---+---+---+------+------+
	| A | B | C | D    | E    |
	+---+---+---+------+------+
	| 1 | 2 | 3 |    4 |    5 |
	| 5 | 6 | 7 | NULL | NULL |
	+---+---+---+------+------+
```