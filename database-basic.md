# 数据库基础 

### sql  索引
创建索引， sql查询处理器可使用索引直接找到所需元组，而不用读取整个关系

```
create index studentID_index on student(ID); 
```

### sql 创建模式

```
# primary key
# foreign key
# not null
# unique
create table department
    (dept_name varchar(20),
    building archer(15),
    budget numeric(12, 2),
    primary key (dept_name));
```
### sql 查询基本结构

```
select 属性1， 属性2, 或者基本聚集函数，或distinct声明
from 关系表1， 关系表2
where 作用在from上的谓词
group by 满足where谓词的元组通过group by形成分组
having 应用到每个分组上，不满足having子句的谓词将被抛弃
```


### 聚集

```
基本聚集
  总和／平均值：sum／avg，必须是数字集，注意重复
  最小值／最大值：min／max
  计数：count
分组聚集
  group by：属性上取值相同的原组被分在一个组中
```

```
# 查找 Person 表中所有重复的电子邮箱
select Email
from Person
group by Email
having count(Email) >= 2
```

```
# 组合两个表
INNER JOIN（内连接,或等值连接）：获取两个表中字段匹配关系的记录。
    select FirstName, LastName, City, State
    from Person， Address
    where Person.PersonId = Address.PersonId

LEFT JOIN（左连接）：获取左表所有记录，即使右表没有对应匹配的记录。
    select FirstName, LastName, City, State
    from Person left join Address on Person.PersonId = Address.PersonId

RIGHT JOIN（右连接）： 与 LEFT JOIN 相反，用于获取右表所有记录，即使左表没有对应匹配的记录。
    select FirstName, LastName, City, State
    from Person right join Address on Person.PersonId = Address.PersonId

NATURAL JOIN (自然链接)
    select FirstName, LastName, City, State
    from Person natural join Address
```

```
sql中表达不等关系 <> 、 !=
```

```
# 查询同一个关系表之间的相互关系，使用别名，当作两个关系表进行操作
select e1.Name as Employee
from Employee e1, Employee e2
where e1.ManagerId = e2.Id and e1.Salary > e2.Salary

```

```
# 嵌套子查询，集合成员资格
# 某网站包含两个表，Customers 表和 Orders 表。编写一个 SQL 查询，找出所有从不订购任何东西的客户。
select Name as Customers
from Customers
where Id not in (
    select CustomerId
    from Orders
)

```

### sql 更新

```
# 有时候两条update 语句的执行顺序不同会造成结果的不同
# 这时候就需要在一条update 语句中执行两种更新

# 将salay表中的性别颠倒
update salary
set sex = case
        when sex = 'f' then 'm'
        else 'f'
    end
```

### 视图

```
#视图， 出于安全考虑，可能需要向用户隐藏特定的数据

# 视图定义
create view v as <query expression>  # v 为合法视图名，<query expression>为合法查询表达式
```

### 事务

```
commit work 提交当前事务
rollback work 回滚当前事务

默认，每条sql语句自成一个事务，且一执行完就提交
如果一个事务要执行多条sql语句，就必须关闭单独 sql语句的自动提交 # begin atomic … end ,关键词之间构成单独的一个事务
```

### 授权
```
对数据的授权包含；读数据、插数据、更新数据、删除数据

# 权限授予
grant <权限列表>
on <关系名或视图名>
to <用户／角色列表>
```
### 收回权限
```
revoke <权限列表>
on <关系名或视图名>
from <用户／角色列表>
```

### 角色
```
辨析 用户／角色
角色是对权限集合的一种抽象，当系统新增一个用户的时候，不必为新用户授予一个个权限，只需为用户授予角色

create role instructor； # 创建一个角色

grant select
on task
to instructor； # 授予角色权限

grant instructor
to dean； # 授予用户角色
```