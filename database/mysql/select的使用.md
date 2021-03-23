# select的使用
主要是记录select的一些特殊字段的用法

## order by排序
order by class_id asc, score desc
使用order by进行排序，asc为升序， desc为降序

## distinct的作用
distinct的作用用于去重只会输出一条
```text
表test
+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
| 4  | 300    |
+----+--------+
```

比如在上面的表中id2和id4是重复的，这种情况下载排序的时候会出现错误，比如要去输出第二高的工资时，这时候会输出的是300，而我们的预期是200
```sql
select distinct salary from test orber by salary desc limit 1,1
```

## as的作用
as用于重命名列名或者表名
LeetCode上的题
```sql
select(select distinct salary from test orber by salary desc limit 1,1) as SecondHighestSalary
第二种
解决NULL的还可以使用IFNULL函数
select IFNULL((select distinct salary from test orber by salary desc limit 1 OFFSET 1),NULL) as SecondHighestSalary
```

## IFNULL
IFNULL函数有两个参数IFNULL("hello", ")















