mysql索引知识点汇总
一.索引基础知识

1.什么叫数据库索引？

答：索引是对数据库中一列或者多列的值进行排序的一种数据结构。重点：对列的值进行排序的数据结构。

使用索引可以快速访问数据库中的记录

2.索引的主要用途是什么？

答：执行select语句时候会使用索引，索引主要用来提高查询性能。由于索引是经过某种算法优化过的，因而查找次数要少的多。

索引另一个主要用途是用在排序上。

3.索引是怎么执行的？

答：数据库也是一种程序，需要在内存中运行，需要cpu和内存资源。索引被数据库程序放到内存中，所以索引不能太大，cpu进行计算查询，然后产生中间结果集。

索引是被数据库程序装载到内存中的，索引是被数据库程序装载到内存中的，索引是被数据库程序装载到内存中的，

索引文件会产生一个中间结果集，然后根据中间结果集，在表中查询具体的记录。

由于索引文件以B-树格式保存，MySQL能够立即转到合适的firstname，然后再转到合适的lastname，最后转到合适的age。在没有扫描数据文件任何一个记录的情况下，MySQL就正确地找出了搜索的目标记录！ 

4.不使用索引情况下，sql查询语句是怎么执行的？

答：如果没有索引，必须遍历整个表。例如这样一个查询：select * from table1 where id=10000。如果没有索引，必须遍历整个表，直到ID等于10000的这一行被找到为止；有了索引之后(必须是在ID这一列上建立的索引)，即可在索引中查找。由于索引是经过某种算法优化过的，因而查找次数要少的多。可见，索引是用来定位的。

5.索引中记录的顺序与表中物理记录的顺序一致么？

答：当然不一致，因为索引会对字段值进行排序，且索引采用特殊的优化算法存储。但聚集索引是不同的，聚集索引中记录的顺序与物理表中记录的顺序是一致的。与非聚集索引相比，聚集索引能提供更快的数据访问速度。

但要注意：一个表中只能有一个聚集索引。

6.索引是怎么存储的？

答：索引是与数据库中的表一起存储的.存储在数据库中。且索引采用二叉树算法存储的，方便查询速很快。

7.索引有哪些种类？

答：可以在数据库中添加三种索引，主键索引，唯一索引，聚集索引。

1)唯一索引 唯一索引是不允许其中任何两行具有相同索引值的索引。当现有数据中存在重复的键值时，大多数数据库不允许将新创建的唯一索引与表一起保存。数据库还可能防止添加将在表中创建重复键值的新数据。例如，如果在employee表中职员的姓(lname)上创建了唯一索引，则任何两个员工都不能同姓。

2)主键索引 数据库表经常有一列或多列组合，其值唯一标识表中的每一行。该列称为表的主键。（多列也可以作为主键），主键索引是唯一索引的特定类型。因为唯一索引要求索引列的值不能相同，而主键也都不相同，主键用来区分不同记录，当然不能重复。故主键索引是唯一索引的特殊形式。

　　　　主键索引是数据库自动建立的，只要指定了主键，数据库就会自动创建主键索引。

3)聚集索引中 表中行的物理顺序与键值的逻辑（索引）顺序相同。一个表只能包含一个聚集索引。如果某索引不是聚集索引，则表中行的物理顺序与键值的逻辑顺序不匹配。与非聚集索引相比，聚集索引通常提供更快的数据访问速度。

二.怎样运用索引？

8.单列索引与组合索引执行效率有差别么？

答：如果在firstname、lastname、age这三个列上分别创建单列索引，效果是否和创建一个firstname、lastname、age的多列索引一样呢？答案是否定的，两者完全不同。当我们执行查询的时候，MySQL只能使用一个索引。如果你有三个单列的索引，MySQL会试图选择一个限制最严格的索引。但是，即使是限制最严格的单列索引，它的限制能力也肯定远远低于firstname、lastname、age这三个列上的多列索引。

由于索引文件以B-树格式保存，MySQL能够立即转到合适的firstname，然后再转到合适的lastname，最后转到合适的age。在没有扫描数据文件任何一个记录的情况下，MySQL就正确地找出了搜索的目标记录！  

9.组合索引的好处是什么？最左前缀是什么？

答：

多列索引还有另外一个优点，它通过称为最左前缀（Leftmost Prefixing）的概念体现出来。继续考虑前面的例子，现在我们有一个firstname、lastname、age列上的多列索引，我们称这个索引为fname_lname_age。当搜索条件是以下各种列的组合时，MySQL将使用fname_lname_age索引： 

firstname，lastname，age 
firstname，lastname 
firstname 
从另一方面理解，它相当于我们创建了(firstname，lastname，age)、(firstname，lastname)以及(firstname)这些列组合上的索引。下面这些查询都能够使用这个fname_lname_age索引： 

SELECT peopleid FROM people WHERE firstname='Mike' AND lastname='Sullivan' AND age='17'; 
SELECT peopleid FROM people WHERE firstname='Mike' AND lastname='Sullivan'; 
SELECT peopleid FROM people WHERE firstname='Mike'; 

The following queries cannot use the index at all: 
SELECT peopleid FROM people WHERE lastname='Sullivan'; 
SELECT peopleid FROM people WHERE age='17'; 
SELECT peopleid FROM people WHERE lastname='Sullivan' AND age='17'; 
8.怎样选择索引列？（重点）

答：在性能优化过程中，选择在哪些列上创建索引是最重要的步骤之一。可以考虑使用索引的主要有两种类型的列：在WHERE子句中出现的列，在join子句中出现的列。

1）通常在where和join的判断字段上，都建立索引。以方便查询速度，在判断条件列上使用索引，方便快定位记录。

2）可以在一列或者多列创建索引。

如果经常同时搜索两列或多列或按两列或多列排序时，建立组合索引会很大提高查询速度。例如，如果经常在同一查询中为姓和名两列设置判据，那么在这两列上创建多列索引将很有意义。

3）在经常用在连接的列上，这些列主要是一些外键，可以加快连接的速度；

4）在经常需要根据范围进行搜索的列上创建索引，因为索引已经排序，其指定的范围是连续的；

SELECT age ## 不使用索引 
FROM people WHERE firstname='Mike' ## 考虑使用索引 
AND lastname='Sullivan' ## 考虑使用索引 
这个查询与前面的查询略有不同，但仍属于简单查询。由于age是在SELECT部分被引用，MySQL不会用它来限制列选择操作。因此，对于这个查询来说，创建age列的索引没有什么必要。下面是一个更复杂的例子： 

SELECT people.age, ##不使用索引 
town.name ##不使用索引 
FROM people LEFT JOIN town ON 
people.townid=town.townid ##考虑使用索引 
WHERE firstname='Mike' ##考虑使用索引 
AND lastname='Sullivan' ##考虑使用索引 

与前面的例子一样，由于firstname和lastname出现在WHERE子句中，因此这两个列仍旧有创建索引的必要。除此之外，由于town表的townid列出现在join子句中，因此我们需要考虑创建该列的索引。 
那么，我们是否可以简单地认为应该索引WHERE子句和join子句中出现的每一个列呢？差不多如此，但并不完全。我们还必须考虑到对列进行比较的操作符类型。MySQL只有对以下操作符才使用索引：<，<=，=，>，>=，BETWEEN，IN，以及某些时候的LIKE。可以在LIKE操作中使用索引的情形是指另一个操作数不是以通配符（%或者_）开头的情形。例如，“SELECT peopleid FROM people WHERE firstname LIKE 'Mich%';”这个查询将使用索引，但“SELECT peopleid FROM people WHERE firstname LIKE '%ike';”这个查询不会使用索引。 

9.哪些字段不适合加索引？

答：第一，查询中很少使用的列不应该创建索引。

第二，对于那些只有很少数据值的列也不应该增加索引。这是因为，由于这些列的取值很少，例如人事表的性别列，在查询的结果中，结果集的数据行占了表中数据行的很大比例，即需要在表中搜索的数据行的比例很大。增加索引，并不能明显加快检索速度。

第三，对于那些定义为text, image和bit数据类型的列不应该增加索引。这是因为，这些列的数据量要么相当大，要么取值很少,不利于使用索引。

第四，当修改性能远远大于检索性能时，不应该创建索引。这是因为，修改性能和检索性能是互相矛盾的。当增加索引时，会提高检索性能，但是会降低修改性能。

10.索引的返回结果是什么？

答：索引的返回结果是一个“中间结果集”，数据库根据中间结果集再去查找数据库中的具体表的记录。

11.具体查询时会选择使用哪个索引文件？

答：每次查询只能使用一个索引，默认数据库会选择限制条件最严格的索引。

12.怎样判断是否使用了索引？及查看索引的使用性能？

答：使用explain命令

EXPLAIN
SELECT * FROM mytable
WHERE category_id=1 AND user_id=2;
This is what Postgres 7.1 returns (exactlyasI expected)
NOTICE:QUERY PLAN:
Index Scan using mytable_categoryid_userid on
mytable(cost=0.00..2.02 rows=1 width=16)
EXPLAIN
以上是postgres的数据，可以看到该数据库在查询的时候使用了一个索引（一个好开始），而且它使用的是创建的第二个索引。看到上面命名的好处了吧，马上知道它使用适当的索引了。
 
13.排序分组是怎么使用索引的？
答：因为索引默认是已经自动排序的，所以在使用排序或者分组时候，用索引去查询速度会很快，产生排序或分组的中间结果集，然后根据中间结果集定位具体的表中的记录。
SELECT * FROM mytable
WHERE category_id=1 AND user_id=2
ORDER BY adddate DESC;
很简单，就像为where子句中的字段建立一个索引一样，也为ORDER BY的子句中的字段建立一个索引：
CREATE INDEX mytable_categoryid_userid_adddate ON mytable (category_id,user_id,adddate);
注意:"mytable_categoryid_userid_adddate"将会被截短为"mytable_categoryid_userid_addda"
 
 
本篇文章主要参考http://m.baidu.com/from=2001a/bd_page_type=1/ssid=502f54484154495350414e1309/uid=0/pu=usm%400%2Csz%401320_2003%2Cta%40iphone_1_9.0_1_11.0/baiduid=5467C9315869C7DD0A81320AA41BD30F/w=0_10_/t=iphone/l=3/tc?ref=www_iphone&lid=2148019621677310814&order=2&fm=alop&tj=www_normal_2_0_10_title&vit=osres&m=8&srd=1&cltj=cloud_title&asres=1&title=mysql%E7%B4%A2%E5%BC%95%E8%AF%A6%E8%A7%A3%28%E8%BD%AC%29-ggjucheng-%E5%8D%9A%E5%AE%A2%E5%9B%AD&dict=30&w_qd=IlPT2AEptyoA_yiMFVCwJqPg4YA0pyNR9-tOkq&sec=15308&di=a09af049d22e5ec5&bdenc=1&tch=124.1474227970972.122.366.0.0&nsrc=IlPT2AEptyoA_yixCFOxXnANedT62v3IEQGG_ytK1DK6mlrte4viZQRAZTT-RXiOJkfugTCcg2tSaC8hOnEobxB0r_-6sVse7mjb9fzwdhPsHxEMewJmQxz_XC5o&eqid=1dcf4c7718a523001000000357def2d5&wd=&clk_info=%7B%22srcid%22%3A%221599%22%2C%22tplname%22%3A%22www_normal%22%2C%22t%22%3A1474229347253%2C%22xpath%22%3A%22div-a-h3%22%7D
及百度百科“数据库索引”，感谢原作者
