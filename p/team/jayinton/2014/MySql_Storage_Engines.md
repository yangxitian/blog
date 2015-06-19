MySQL 存储引擎对比
===
>前几日，在修改sql文件的时候发现振炼的mysql数据库不是跟我一样使用InnoDB 存储引擎,而是MyISAM！  
>感觉可能会掉坑，所以搜下资料  
> By Jayin Ton
> 2014.11.14

以下讲到MySQL数据库主要指5.5版本,因为本人在应用中。

### 首先，存储引擎是啥东西？
按我个人的理解就是，存储引擎就是一层驱动，介乎于上层的接口与底层的存储文件之间。什么？有点抽象？
不妨拿常见的Java的MVC架构来说明下他们的关系

![MVC](__IMG__/jayinton/java_mvc.png)

神马？还不懂?来个更通俗易懂的比喻，那就是电脑上的内存条:主板就按照一定标准，制定了内存条的接口规范，至于不同牌子的内存的性能，频率是如何，这就看内存条制造厂商的实现了。所以，这些厂商，可以比喻成众多的存储引擎，对规范的接口的一种实现。说白了就是如何存储数据、如何为存储的数据建立索引和如何更新、查询数据等技术的实现方法。


### MySQL有哪些存储引擎？
主要有:  
* InnoDB 
* The MyISAM Storage Engine    
* The MEMORY Storage Engine   
* The CSV Storage Engine         
* The ARCHIVE Storage Engine   
* The BLACKHOLE Storage Engine  
* The MERGE Storage Engine      
* The FEDERATED Storage Engine     

**那么问题来了,MySql数据库存储引擎哪家强？**
这么绝对的问题我肯定不敢回答你，只能说：没有最强，只有最适合。
PS: MySQL 5.5默认的存贮引擎是`InnoDB` 


### 存储引擎比较

* **MyISAM**表还支持3中不同的存储格式： 静态表，动态表，压缩表 

    静态表是默认的存储格式，静态表中的字段都是非变长的字段，优点是：存储非常迅速，容易缓存，出现故障容易恢复；缺点是：占用的空间通常比动态表多。（注意：在存储时，列的宽度不足时，用空格补足，当时在访问的时候并不会得到这些空格） 
动态表的字段是变长的，优点是：占用的空间相对较少，但是频繁地更新删除记录会产生碎片，需要定期改善性能，并且出现故障的时候恢复相对比较困难。 

    压缩表占用磁盘空间小，每个记录是被单独压缩的，所以只有非常小的访问开支。 


* **MEMORY**存储引擎使用存在内存中的内容来创建表。每个MEMORY表只实际对应一个磁盘文件。MEMORY类型的表访问非常得快，因为它的数据是放在内存中的，并且默认使用HASH索引。但是一旦服务关闭，表中的数据就会丢失掉。 

* **MERGE**存储引擎是一组MyISAM表的组合，这些MyISAM表必须结构完全相同。MERGE表本身没有数据，对MERGE类型的表进行查询、更新、删除的操作，就是对内部的MyISAM表进行的。


* **InnoDB**存储引擎提供了具有提交、回滚和崩溃恢复能力的事务安全。但是比起MyISAM存储引擎，InnoDB写的处理效率差一些并且会占用更多的磁盘空间以保留数据和索引。 
    
    一般来说，如果需要事务支持，并且有较高的并发读写频率，InnoDB是不错的选择。


* **Archive**存储引擎,正如其名称所示，Archive非常适合存储归档数据，如日志信息。它只支持INSERT和SELECT操作，其设计的主要目的是提供高速的插入和压缩功能


* **Federated**存储引擎不存放数据，它至少指向一台远程MySQL数据库服务器上的表，非常类似于Oracle的透明网关。


* 其他呢？ 自行百度，google,目前比较普及的存储引擎是MyISAM和InnoDB

来张图比较下(主要看MyISAM & InnoDB)
![存储引擎对比](__IMG__/jayinton/mysql_storage _engine_compare.jpeg)


### 那么掉坑了吗？
那么回到我搜索资料初衷，振炼使用了而是MyISAM作为存储引擎合适吗？  
不合适，因为不支持事务。万一哪一天在代码里调用了事务管理,那么。。。。


参考
===
http://dev.mysql.com/doc/refman/5.5/en/index.html 
http://www.cnblogs.com/gbyukg/archive/2011/11/09/2242271.html  
http://www.zhangyongjun.com/blog/article.asp?id=13960  
http://www.open-open.com/lib/view/open1370959041320.html






