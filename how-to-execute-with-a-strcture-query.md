#  01 | 基础架构：一条SQL查询语句是如何执行的？

### MySQL基本架构图

> 其实一条SQL的查询语句的执行并不是很复杂！

我们就从最简单的一条查询语句来入手分析这个问题。
比如一条`SELECT * FROM T WHERE ID=10;`这样的语句它的整个执行的流程是怎么样的呢？

![img](https://blog-1252574663.cos.ap-shanghai.myqcloud.com/mysql%E7%BB%93%E6%9E%84%E5%9B%BE.png)

上图就是MySQL的结构图，从结构上我们能看到MySQL的结构主要分为两层：

- server层
  主要包括：连接器、查询缓存、分析器、优化器、执行器等
- 引擎层
  引擎层主要是负责数据的存储和读取

### 连接器

连接器主要是负责连接MySQL客户端并获取权限以及维持连接状态。我们想要执行MySQL语句之前先要连接MySQL客户端，执行语句:

```
mysql -h $hostname -P $port -u $user -p
```

执行上面语句的时候会出现以下两种情况：

- 如果用户名或者密码不正确则会出现报错`ERROR 1045 (28000): Access denied for user 'test'@'localhost' (using password: NO)`，相信这个报错大多数用过MySQL的人都遇到过，从字面上也很好理解，访问拒绝对于test这个用户，然后程序结束。
- 如果用户名和密码正确则会进入MySQL客户端界面。在进入之前，MySQL会根据用户名查询出该用户拥有的权限，这个连接中的所有权限校验都是依赖于之前查询的权限。

### 查询缓存

> **NOTE**
> The query cache is deprecated as of MySQL 5.7.20, and is removed in MySQL 8.0

查询缓存是指输入查询语句，MySQL会优先执行查询缓存，如果命中缓存则直接返回查询结果。在MySQL中对于每次查询的结果会有缓存，每次有对于数据库表结构或者数据的修改，缓存就会失效。如果没有命中缓存就会继续往下执行。
所以在大多数情况下缓存的命中率是很低的，除非是一张更新频率非常低的静态表，否则缓存的命中率会非常的低。MySQL官方也是在MySQL8.0中放弃了对于查询缓存的支持。所以在大多数情况下是不建议使用查询缓存的。
关于查询缓存的配置请参考官方文档操作：[Query Cache Configuration](https://dev.mysql.com/doc/refman/5.7/en/query-cache-configuration.html)

### 分析器

没有命中缓存，就要开始执行真正的执行语句了。分析器的工作主要是对输入的SQL语句做解析。
首先会做"词法分析"，当你输入一窜SQL语句的时候，系统要先能识别T是一个表名，ID是字段名，WHERE和SELECT是一个MySQL的关键字...等。
做完了这些之后就会进行"语法分析"，语法分析就是判断这些关键字是否合法，如果你输入的SQL语句语法不正确就是有提示，比如WHERE关键字少个W

```
SELECT * FROM T HERE ID = 10;

ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'ID = 10' at line 1
```

通过以上的输出，我们能看到语法的检测是在分析器阶段执行的，如果遇到syntax error，只需要关注`use near`关键字后面的部分。

### 优化器

优化器顾名思义就是对SQL语句做优化的步骤，那就有同学有问题了，SQL语句都写好了，MySQL系统还能对其做优化？答案是：当然能。

```
SELECT * FROM t1 JOIN t2 ON `id` WHERE t1.a = 10 AND t2.b = 20;
```

上面是一条简单的SQL语句，系统的执行顺序可以如下：

- 可以先查询t1表中a字段等于10的数据,然后根据查询结果通过ID关联t2表，判断t2表中的b字段是否等于20。
- 也可以先查询t2表中b字段等于20的数据，然后根据插叙结果通过ID关联到t1表，判断t1表a字段是否等于10。

还有一些是关于数据库索引的优化，这个部分比较复杂，后面会单独讲到。

### 执行器

当前面几个步骤完成的时候就开始进入到执行器阶段，执行器首先会做权限校验，判断当前用户是否有该SQL语句的操作权限，如果没有则会报错。

```
DELETE FROM t;

ERROR 1142 (42000): DELETE command denied to user 'test'@'localhost' for table 't'
```

如果权限校验也通过就会真正的执行查询，查询是通过调用引擎提供的API。

### 引擎

引擎层是通过插件的形式存在的，存储引擎负责MySQL中数据的储存和提取。服务器通过API与储存引擎行进通信，这些API屏蔽了不同引擎之间的差异，使得引擎的差异对服务层没有影响。不同的数据存储引擎之间也是不通信的，互相之间不会影响。