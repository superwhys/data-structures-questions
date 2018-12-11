### 数据库

数据库这块我们先从在开发中常用的数据库Mysql开始，MySQL是一个关系型数据库管理系统，由瑞典MySQL AB 公司开发，目前属于 Oracle 旗下公司。MySQL 最流行的关系型数据库管理系统，在 WEB 应用方面MySQL是最好的 RDBMS (Relational Database Management System，关系数据库管理系统) 应用软件之一。

MySQL基础:

* [MySQL的多存储引擎架构](#MySQL的多存储引擎架构)
* [数据库的事务](#数据库的事务)
* [数据库ACID](#数据库ACID)
* [数据库中的范式](#数据库中的范式)

#### MySQL的多存储引擎架构
<p align="center">
<img width="500" align="center" src="../images/2.jpg" />
</p>

MySQL作为一个大型的网络程序、数据管理系统，架构非常复杂。

#### 数据库的事务
<p align="center">
<img width="500" align="center" src="../images/3.jpg" />
</p>

数据库的事务指的是满足数据库的 ACID 特性的一组操作，可以通过 Commit 提交一个事务，也可以使用 Rollback 进行回滚。

MySQL 中默认采用自动提交(AUTOCOMMIT)模式。如果不显式使用 START TRANSACTION 语句来开始一个事务，那么每个查询都会被当做一个事务自动提交。

#### 数据库ACID

1. 原子性（Atomicity）

原子性是指事务是一个不可分割的工作单位，事务中的操作要么全部成功，要么全部失败。比如在同一个事务中的SQL语句，要么全部执行成功，要么全部执行失败。
回滚可以用日志来实现，日志记录着事务所执行的修改操作，在回滚时反向执行这些修改操作即可。

2. 一致性（Consistency）

事务必须使数据库从一个一致性状态变换到另外一个一致性状态。以转账为例子，A向B转账，假设转账之前这两个用户的钱加起来总共是100，那么A向B转账之后，不管这两个账户怎么转，A用户的钱和B用户的钱加起来的总额还是100，这个就是事务的一致性。

3. 隔离性（Isolation）

隔离性是当多个用户并发访问数据库时，比如操作同一张表时，数据库为每一个用户开启的事务，不能被其他事务的操作所干扰，多个并发事务之间要相互隔离。

即要达到这么一种效果：对于任意两个并发的事务 T1 和 T2，在事务 T1 看来，T2 要么在 T1 开始之前就已经结束，要么在 T1 结束之后才开始，这样每个事务都感觉不到有其他事务在并发地执行。

4. 持久性（Durability）

一旦事务提交，则其所做的修改将会永远保存到数据库中。即使系统发生崩溃，事务执行的结果也不能丢失。

可以通过数据库备份和恢复来实现，在系统发生奔溃时，使用备份的数据库进行数据恢复。

事务的 ACID 特性概念简单，但不是很好理解，主要是因为这几个特性不是一种平级关系：
* 只有满足一致性，事务的执行结果才是正确的。
* 在无并发的情况下，事务串行执行，隔离性一定能够满足。此时要只要能满足原子性，就一定能满足一致性。
* 在并发的情况下，多个事务并发执行，事务不仅要满足原子性，还需要满足隔离性，才能满足一致性。
* 事务满足持久化是为了能应对数据库奔溃的情况。

<p align="center">
<img width="500" align="center" src="../images/4.jpg" />
</p>

#### 数据库中的范式
满足最低要求的范式是第一范式（1NF）。在第一范式的基础上进一步满足更多规范要求的称为第二范式（2NF），其余范式以次类推。一般说来，数据库只需满足第三范式 (3NF）就可以了。范式的包含关系。一个数据库设计如果符合第二范式，一定也符合第一范式。如果符合第三范式，一定也符合第二范式…

* 1NF：属性不可分
* 2NF：属性完全依赖于主键 [消除部分子函数依赖]
* 3NF：属性不依赖于其它非主属性 [消除传递依赖]
* BCNF（巴斯-科德范式）：在1NF基础上，任何非主属性不能对主键子集依赖[在3NF基础上消除对主码子集的依赖]
* 4NF：要求把同一表内的多对多关系删除。
* 5NF（完美范式）：从最终结构重新建立原始结构。

范式理论是为了解决以上提到四种异常。高级别范式的依赖于低级别的范式，1NF 是最低级别的范式。

<p align="center">
<img width="500" align="center" src="../images/5.jpg" />
</p>

* 1. 第一范式 (1NF)

属性不可分。

* 2. 第二范式 (2NF)

每个非主属性完全函数依赖于键码。

可以通过分解来满足。

**分解前** 

| Sno  | Sname  | Sdept  | Mname  | Cname  | Grade |
| ---- | ------ | ------ | ------ | ------ | ----- |
| 1    | 学生-1 | 学院-1 | 院长-1 | 课程-1 | 60    |
| 2    | 学生-2 | 学院-2 | 院长-2 | 课程-2 | 90    |
| 2    | 学生-2 | 学院-2 | 院长-2 | 课程-1 | 100   |
| 3    | 学生-3 | 学院-2 | 院长-2 | 课程-2 | 95    |

以上学生课程关系中，{Sno, Cname} 为键码，有如下函数依赖：

- Sno -> Sname, Sdept
- Sdept -> Mname
- Sno, Cname-> Grade

Grade 完全函数依赖于键码，它没有任何冗余数据，每个学生的每门课都有特定的成绩。

Sname, Sdept 和 Mname 都部分依赖于键码，当一个学生选修了多门课时，这些数据就会出现多次，造成大量冗余数据。

**分解后** 

关系-1

| Sno  | Sname  | Sdept  | Mname  |
| ---- | ------ | ------ | ------ |
| 1    | 学生-1 | 学院-1 | 院长-1 |
| 2    | 学生-2 | 学院-2 | 院长-2 |
| 3    | 学生-3 | 学院-2 | 院长-2 |

有以下函数依赖：

- Sno -> Sname, Sdept
- Sdept -> Mname

关系-2

| Sno  | Cname  | Grade |
| ---- | ------ | ----- |
| 1    | 课程-1 | 90    |
| 2    | 课程-2 | 80    |
| 2    | 课程-1 | 100   |
| 3    | 课程-2 | 95    |

有以下函数依赖：

- Sno, Cname -> Grade

* 3. 第三范式 (3NF)

非主属性不传递函数依赖于键码。

上面的 关系-1 中存在以下传递函数依赖：

- Sno -> Sdept -> Mname

可以进行以下分解：

关系-11

| Sno  | Sname  | Sdept  |
| ---- | ------ | ------ |
| 1    | 学生-1 | 学院-1 |
| 2    | 学生-2 | 学院-2 |
| 3    | 学生-3 | 学院-2 |

关系-12

| Sdept  | Mname  |
| ------ | ------ |
| 学院-1 | 院长-1 |
| 学院-2 | 院长-2 |

#### 并发一致性问题 