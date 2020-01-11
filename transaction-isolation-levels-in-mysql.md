#MySQL事务的隔离级别

### 前言

说道事务，你肯定不陌生，和数据库打交道免不了要用事务。事务最常见的应用就是转账，比如公司发工资，如果当公司的账户转账给员工的瞬间服务器坏了，公司的账户钱变少了，而员工的账户的钱并没有相应的增多，这就很尴尬了。事务的出现就是用来解决这种问题的。

我们知道MySQL是支持不同的数据库引擎的，在MySQL5.5.5之前，MySQL的默认引擎都是MyISAM，到了5.5.5之后MySQL默认的数据库引擎改为InnoDB，这两个数据库引擎有个最大的区别就是MyISAM引擎是不支持事务的，这就使得MyISAM引擎很难支撑复杂的业务场景，包括金融交易等。

简单来说事务就是保证MySQL的一组操作，要么全部成功，要么全部失败。想下，如果上面转账的例子，引入事务的概念，保证公司账户转出钱和员工账户转入钱要么全部成功，要么全部失败，就可以完美解决问题了。

提到事务，大家肯定会想到ACID（Atomicity、Consistency、Isolation、Durability即原子性、一致性、隔离性、持久性），下面我们就来对其中之一的隔离性（Isolation）做一些介绍。


### 隔离级别（ISOLATION LEVEL）

隔离性其实比想象要复杂。在SQL中定义了四种隔离的级别，每一种隔离级别都规定了一个事务中的修改，哪些是在事务内和事务间是可见的，哪些是不可见的。较低级别的隔离通常来说能承受更高的并发，系统的开销也会更小。

下面简单的介绍一下这四种事务的隔离级别，并添加一些实践。
#### READ UNCOMMITTED（未提交读）
在READ UNCOMMITTED级别，事务的修改，即使没有提交，对其他事务也都是可见的。事务可以读取未提交的数据，这也被称为脏读（Dirty Read）。这个级别的隔离会导致很多问题，虽然在性能方面是最优的，但是缺乏其他级别的很多好处，所以这种隔离的级别很少在实际中应用。
* CREATE TABLE

  ```sql
  CREATE TABLE `t` (
    `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
    `point` int(11) DEFAULT NULL,
    PRIMARY KEY (`id`)
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
  ```
  
* INSERT INTO

  ```sql
  INSERT INTO t(point) VALUES(90);
  ```
  
* READ UNCOMMITTED实践
  开启两个MySQL SESSION，并将MySQL的默认隔离级别设置为READ UNCOMMITTED
  ```sql
  SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
  ```
  ![READ UNCOMMITTED PNG](https://user-gold-cdn.xitu.io/2019/3/24/169af28aead94fbc?w=1146&h=120&f=png&s=26732)
  

  ![READ UNCOMMITTED GIF](https://user-gold-cdn.xitu.io/2019/3/24/169af28aecdf58af?w=700&h=350&f=gif&s=3543646)
  以上的一段GIF图显示了具体的实践过程，左边部分是SESSION A，右边是SESSION B从上面的实践中我们可以看到，当隔离级别设置为READ COMMITTED：
  * **SESSION A第一次查询point的值为90**
  * **SESSION B更新point为100**
  * **此时SESSION B的更改并未提交**
  * **SESSION A第二次查询point的值为100**。
  

#### READ COMMITTED（读已提交）
大多数数据库系统默认的隔离级别都是READ COMMITTED(**但MySQL不是**)，"读已提交"简单的定义：一个事务只能看见已经提交的事务的修改结果。换句话说，一个事务从开启事务到提交事务之前，对其他事务都是不可见的，因此在同一个事务中的两次相同查询结果可能不一样。故这种隔离级别有时候也叫不可重复读（NONREPEATABLE READ）。
* READ COMMITTED 实践
  ![READ COMMITTED PNG](https://user-gold-cdn.xitu.io/2019/3/24/169af28aed1c0f5c?w=1370&h=116&f=png&s=26212)

  ![READ COMMMITED GIF](https://user-gold-cdn.xitu.io/2019/3/24/169af28aecfb4ab5?w=700&h=380&f=gif&s=4267718)
  以上的一段GIF图显示了"读已提交"隔离级别下的实践。我们将隔离级别设置为READ COMMITTED，从执行过程我们可以看到：
  * 第一次**SESSION A查询point为90**
  * **SESSION B更新point为100**
  * 第二次**SESSION A查询point为90**
  * **SESSION B提交事务**
  * 第三次**SESSION A查询point为100**
  
  从实践中我们可以看到当SESSION B的事务提交后，SESSION A就能读取到SESSION B修改的数据。
  
#### REPEATABLE READ（可重复读）
  "可重复读"是MySQL的默认事务隔离级别。REPEATABLE READ解决了脏读的问题，**该级别保证了在同一次事务中多次查询相同的语句结果是一致的**。但是"可重复读"隔离级别无法避免产生幻行（Phantom Row）的问题，MySQL的InnoDB引擎通过多版本并发控制（MVCC，Multiversion Concurrency Controller）解决了幻读的问题。
  * REPEATABLE READ 产生幻行的实践
    ![Phantom_rows](https://user-gold-cdn.xitu.io/2019/3/24/169af28aecd45aba?w=700&h=380&f=gif&s=5942106)
    从上面GIF图显示的过程我们可以看到，最后SESSION A查询语句的结果只有一条id为1的数据，但是我们在插入id=2的数据的时候产生了报错

    ```sql
    ERROR 1062 (23000): Duplicate entry '2' for key 'PRIMARY'
    ```
    从报错中很容易就能看到是因为id=2的行已经存在了，前面读取行数据的结果就是幻读。
  * REPEATABLE READ 实践
    ![REPEATABLE READ PNG](https://user-gold-cdn.xitu.io/2019/3/24/169af28aeea6f6be?w=1402&h=116&f=png&s=26751)

    ![REPEATABLE READ GIF](https://user-gold-cdn.xitu.io/2019/3/24/169af28b17fd8de7?w=700&h=379&f=gif&s=3854609)
    上面的图片显示了在"可重复读"隔离界别下的实践，我们将隔离界别设置为REPEATABLE READ，我们可以看到：
    * 第一次**SESSION A查询point为90**
    * **SESSION B更新point为100**
    * 第二次**SESSION A查询point为90**
    * **SESSION B提交事务**
    * 第三次**SESSION A查询point为90**
    
    我们从实践中可以看到无论SESSION B怎么改变，SESSION A在事务开启后同一查询语句查询的结果都是一致的。
    
#### SERIALIZABLE（可串行化）
  SERIALIZABLE是最高的隔离级别，它通常通过强制事务串行，避免了前面说的幻读问题。简单来说，"可串行化"会在读取的每一行数据上都加锁，所以可能会导致大量的锁等待和超时问题，所以在实际的生产环境中也很少会用到这个隔离级别，只有在非常需要确保数据的一致性切可以接受没有并发的情况下，才会考虑使用这个隔离级别。
  * SERIALIZABLE实践
    ![REPEATABLE READ PNG](https://user-gold-cdn.xitu.io/2019/3/24/169af28aeea6f6be?w=1402&h=116&f=png&s=26751)

    ![SERIALIZABLE GIF](https://user-gold-cdn.xitu.io/2019/3/24/169af28b1ab2ae59?w=700&h=385&f=gif&s=4627081)
    上面的GIF显示了在"可串行化"隔离级别下的实践。我们将隔离级别设置为SERVILAZABLE，从执行的过程中我们可以看到：
    * 第一次**SESSION A查询结果只有id=1**
    * **SESSION B 插入id=2的数据**，因为SESSION A的事务还未提交，此时锁等待。
    * 第二次**SESSION A查询结果仍然是只有一行id=1**
    * **SESSION A提交事务，在提交事务的瞬间SESSION A释放锁，SESSION B锁等待结束**
    * **SESSION B提交事务**
    * 第三次**SEESION A查询结果出现了id=1和id=2这两条记录**
    

  从上面的过程我们可以看到，"可串行化"是通过对每一行数据都加锁的方式来避免幻行问题，这种方式效率非常的低，很容易造成较长时间的锁等待。


### 总结
这篇文章介绍了事务以及四种隔离级别，隔离级别越高对于锁的要求就越高，性能就会相对越差。每一种事务的隔离级别都有对应的应用场景，这些隔离级别没有好坏之分，只是对应不同的业务场景可能需要用到不同的隔离级别。突然想到最近网上很流行的一句话：**小孩子才分对错，成年人只谈利弊**。


