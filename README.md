# SQLAlchemy 事务

## 环境配置
1. 创建数据库

```bash
➜  ~ psql
psql (9.5.4)
Type "help" for help.

banxi=# create user bankapp with password '2017bank';
CREATE ROLE
banxi=# create database bank owner bankapp;
CREATE DATABASE
banxi=# grant all privileges on database bank to bankapp;
GRANT
banxi=#
```

2. 创建初始表

```sql
CREATE TABLE "public"."account" (
    "name" text,
    "amount" numeric(19,2) DEFAULT '0',
    PRIMARY KEY ("name")
);


```

3. 插入初始测试数据

```sql
INSERT INTO "public"."account"("name", "amount") VALUES('A', 500) RETURNING "name", "amount";
INSERT INTO "public"."account"("name", "amount") VALUES('B', 500) RETURNING "name", "amount";
```

上面的 步骤 2,3 在测试中会自动处理.


## 经典事务例子
如上插入的数据:
 
>账号 A: 余额 500 元
 账号 B: 余额 500 元
 
现在需要从账号A 向 账号 B 转 100元. 需要六个步骤, 伪代码表示如下:
 
```sql
BEGIN 
1. read(A)
2. A:= A - 100
3. write(A)
4. read(B)
5. B:= B + 100
6. write(B) 
COMMIT 
```
 
 
1. 直接操作不用事务, 也没有异常. 见代码 `step1`
 
2. 在 步骤 4 时出错, 直接模拟抛出一个异常, 执行测试之后. 
  `A:400`, `B:500` 也就是 A 的余额减少了,但是由于操作错误, B 中的余额没有增加.
  