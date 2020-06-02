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



## 3.入参出参

## 4.判断

## 5.循环

## 6.退出、继续循环

## 7.游标

## 8.handler