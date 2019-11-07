## 基本命令

### 创建数据库

1、创建一个数据库(直接创建一个数据库，没有设置任何特殊选项，都是使用默认的。)

```sql
create datebase DormitoryDatabase;
```

2、创建数据库的时候设置一些参数选项

```sql
create database DormitoryDatabase
on primary  --配置主数据文件的选项
(
name='MyDatabase2',        --主数据文件的逻辑名称
filename='F:\DATABASE1\MyDatabase2.mdf',     --主数据文件的实际保存路径
size=5MB,         --主文件的初始大小
maxsize=150MB,      --最大容量
filegrowth=20%         --以20%扩容
)
log on      --配置日志文件的选项
(
name='MyDatabase2_log',         --日志文件的逻辑名称
filename=' F:\DATABASE1\MyDatabase2_log.ldf',          --日志文件的实际保存路径
size=5mb,       --日志文件的初始大小
filegrowth=5mb         --超过默认值后自动再扩容5mb
)
```

### 删除数据库

```sql
drop database DormitoryDatabase;
```

### 添加登录用户

```sql
USE DormitoryDatabase;

create login DMSAdmin with password = 'admin2019', default_database = DormitoryDatabase;
create user DMSAdmin for login DMSAdmin with default_schema = dbo;
exec sp_addrolemember 'db_owner', 'DMSAdmin';
```

### 为数据库创建表格

创建一个员工表
<员工表>：员工Id,身份证号，姓名，性别，入职日期，年龄，地址，电话，所属部门、Email

```sql
use StaffDatabase
create table Employees
(
    EmpID int identity(1,1) primary key,	--自增  主键
    EmpIDCard varchar(18) not null,
    EmpName nvarchar(50) null,		-- 可变长度，每个字符占用两个字节 最多50个字节
    EmpGender bit not null,
    EmpJoinDate datetime,
    EmpAge int,
    EmpAddress nvarchar(300),
    EmpPhone varchar(100),
    DeptID int not null,
    EmpEmail varchar(100)
)
```

### 删除数据库下的表格

```sql
use DormitoryDatabase
drop table Employees
```

