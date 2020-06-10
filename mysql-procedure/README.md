# MySql存储过程语法

## 1.语法结构 

​    存储过程是数据库的一个重要对象，可以封装SQL语句集，可以用来完成一些较复杂的业务逻辑。  

​    创建时会预先编译后保存，用户后续调用不需要再次编译。

```mssql
-- 参考手册
https://dev.mysql.com/doc/refman/5.7/en/sql-statements.html
-- 中文版参考手册
https://www.docs4dev.com/docs/zh/mysql/5.7/reference/sql-syntax.html
```



### 1.1 声明结束符

```mssql
--声明结束符,因为MySQL默认使用‘;’作为结束符，而在存储过程中，会使用‘;’作为一段语句的结束，导致‘;’使用冲突，需要自定义结束符
delimiter $$ 
```

### 1.2语法结构

```mysql
CREATE
    [DEFINER = user]
	PROCEDURE sp_name ([proc_parameter[,...]])
    [characteristic ...] routine_body
    
-- proc_parameter参数部分，可以如下书写：
	[ IN | OUT | INOUT ] param_name type
	-- type类型可以是MySQL支持的所有类型
	
-- routine_body(程序体)部分，可以书写合法的SQL语句 BEGIN ... END
```

简单演示：

```mysql
-- 声明结束符
delimiter $$
-- 创建存储过程
CREATE PROCEDURE hello_procedure ()
BEGIN
	SELECT 'hello procedure';
END $$

-- 调用存储过程
call hello_procedure();
```



## 2.变量与赋值

常用的两种变量类型：**局部变量、用户变量**

##### **局部变量：**

用户自定义，在begin/end块中使用，需要提前声明

```sql
语法：
声明变量 declare var_name type [default var_value];
举例：declare nickname varchar(32);
```

```sql
-- set赋值
delimiter $$
create procedure sp_var01()
begin
	declare nickname varchar(32) default 'unkown';
	set nickname = 'ZS';
	-- set nickname := 'SF';
	select nickname;
end$$
```

```sql
-- into赋值
delimiter $$
create procedure sp_var_into()
begin
	declare emp_name varchar(32) default 'unkown' ;
	declare emp_no int default 0;
	select e.empno,e.ename into emp_no,emp_name from emp e where e.empno = 7839;
	select emp_no,emp_name;
end$$
```

##### 用户变量：

用户自定义，当前会话（连接）有效，不需要提前声明

```sql
语法： 
@var_name
不需要提前声明，使用即声明
```

```sql
-- 赋值
delimiter $$
create procedure sp_var02()
begin
	set @nickname = 'ZS';
end$$
call sp_var02() $$
select @nickname$$  --可以看到结果
```

##### 会话变量：

由系统提供，当前会话（连接）有效

```
语法：
@@session.var_name
```

```sql
show session variables; -- 查看会话变量
select @@session.unique_checks; -- 查看某会话变量
set @@session.unique_checks = 0; --修改会话变量
```

##### 全局变量：

由系统提供，整个mysql服务器有效

```
语法：
@@global.var_name
```

举例

```sql
-- 查看全局变量中变量名有char的记录
show global variables like '%char%'; 

-- 查看全局变量character_set_client的值
select @@global.character_set_client; 
```



## 3.入参出参

```sql
-- 语法
in | out | inout param_name type
```

##### IN类型：

```sql
-- IN类型演示
delimiter $$
create procedure sp_param01(in age int)
begin
	set @user_age = age;
end$$
call sp_param01(10) $$
select @user_age$$
```

##### OUT类型：

```sql
-- OUT类型，只负责输出！
-- 需求：输出传入的地址字符串对应的部门编号。
delimiter $$

create procedure sp_param02(in loc varchar(64),out dept_no int(11))
begin
	select d.deptno into dept_no from dept d where d.loc = loc;
	--此处强调，要么表起别名，要么入参名不与字段名一致
end$$
delimiter ;

--测试
set @dept_no = 100;
call sp_param01('DALLAS',@dept_no);
select @dept_no;
```

##### INOUT类型 ：

```sql
-- INOUT类型 
delimiter $$
create procedure sp_param03(inout name varchar)
begin
	set name = concat('hello' ,name);
end$$
delimiter ;

set @user_name = '小明';
call sp_param03(@user_name);
select @user_name;
```



## 4.判断

##### if

```sql
-- 语法
IF search_condition THEN statement_list
    [ELSEIF search_condition THEN statement_list] ...
    [ELSE statement_list]
END IF
```



##### case

## 5.循环

## 6.退出、继续循环

## 7.游标

## 8.handler