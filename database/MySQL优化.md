## MySQL优化

### 选取最适用的字段属性

MySQL可以很好的支持大数据量的存取，但是一般说来，数据库中的表越小，在它上面执行的查询也就会越快。因此，在创建表的时候，为了获得更好的性能，我们可以将表中字段的宽度设得尽可能小。

例如，在定义邮政编码这个字段时，如果将其设置为CHAR(255),显然给数据库增加了不必要的空间，甚至使用VARCHAR这种类型也是多余的，因为CHAR(6)就可以很好的完成任务了。同样的，如果可以的话，我们应该使用MEDIUMINT而不是BIGIN来定义整型字段。

bigint

从 -2^63 (-9223372036854775808) 到 2^63-1 (9223372036854775807) 的整型数据（所有数字）。存储大小为 8 个字节。

P.S. bigint已经有长度了，在mysql建表中的length，只是用于显示的位数

int

从 -2^31 (-2,147,483,648) 到 2^31 – 1 (2,147,483,647) 的整型数据（所有数字）。存储大小为 4 个字节。int 的 SQL-92 同义字为 integer。

smallint

从 -2^15 (-32,768) 到 2^15 – 1 (32,767) 的整型数据。存储大小为 2 个字节。

tinyint

从 0 到 255 的整型数据。存储大小为 1 字节。

注释
在支持整数值的地方支持 bigint 数据类型。但是，bigint 用于某些特殊的情况，当整数值超过 int 数据类型支持的范围时，就可以采用 bigint。在 SQL Server 中，int 数据类型是主要的整数数据类型。

在数据类型优先次序表中，bigint 位于 smallmoney 和 int 之间。

只有当参数表达式是 bigint 数据类型时，函数才返回 bigint。SQL Server 不会自动将其它整数数据类型（tinyint、smallint 和 int）提升为 bigint。

int(M) 在 integer 数据类型中，M 表示最大显示宽度。在 int(M) 中，M 的值跟 int(M) 所占多少存储空间并无任何关系。和数字位数也无关系 int(3)、int(4)、int(8) 在磁盘上都是占用 4 btyes 的存储空间。

另外一个提高效率的方法是在可能的情况下，应该尽量把字段设置为NOTNULL，这样在将来执行查询的时候，数据库不用去比较NULL值。
对于某些文本字段，例如“省份”或者“性别”，我们可以将它们定义为ENUM类型。因为在MySQL中，ENUM类型被当作数值型数据来处理，而数值型数据被处理起来的速度要比文本类型快得多。

### 使用连接join代替 子查询

MySQL从4.1开始支持SQL的子查询。这个技术可以使用SELECT语句来创建一个单列的查询结果，然后把这个结果作为过滤条件用在另一个查询中。比如要将客户基本信息表中没有任何订单的客户删除掉，就可以利用子查询先从销售信息表中将所有发出订单的客户ID取出来，然后将结果传递给主查询：\
delete * from customerinfo where customerid not in (select customerid from salesinfo)

使用子查询可以一次性的完成很多逻辑上需要多个步骤才能完成的SQL操作，同时也可以避免事务或者表锁死，并且写起来也很容易。但是，有些情况下，子查询可以被更有效率的连接（JOIN）..替代。比如要将所有没有订单记录的用户取出来：\
select * from customerinfo left join salesinfo on customerinfo.customerid=salesinfo.customerid where salesinfo.customerid is null

连接（JOIN）..之所以更有效率一些，是因为MySQL**不需要在内存中创建临时表**来完成这个逻辑上的需要两个步骤的查询工作。

## 使用联合UNION来代替手动创建的临时表

MySQL从4.0的版本开始支持union查询，它可以把多条select查询合并的一个查询中。在客户端的查询会话结束的时候，临时表会被自动删除，从而保证数据库整齐、高效。\
select name,phone from client union\
select name,birth from mauthor union\
select name,supplier from product

### 使用事务

数据库操作更多的时候是需要用到一系列的语句来完成某种工作。但是在这种情况下，当这个语句块中的某一条语句运行出错的时候，整个语句块的操作就会变得不确定起来。事务可以保证数据的一致性和完整性，以BEGIN关键字开始，COMMIT关键字结束，在这之间的一条SQL操作失败，ROLLBACK命令都可以把数据库恢复到BEGIN开始之前的状态。\
BEGIN; INSERT INTO salesinfo SET customerid=14; update inventory set quantity=11 where item='book';COMMIT;

事务的另一个重要作用是当多个用户同时使用相同的数据源时，它可以利用锁定数据库的方法来为用户提供一种安全的访问方式，这样可以保证用户的操作不被其它的用户所干扰。

### 锁定表
由于事务的独占性，有时会影响数据库的性能。尤其是在很大的应用系统中。由于在事务执行的过程中，数据库将会被锁定，因此其它的用户请求只能暂时等待直到该事务结束。假设有成千上万的用户同时访问一个数据库系统，例如访问一个电子商务网站，就会产生比较严重的响应延迟。有些情况下我们可以通过锁定表的方法来获得更好的性能。\
locktable inventory write select quantity from inventory
