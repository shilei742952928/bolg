索引的最左前缀原理       
   explain 详解与索引最佳实践   结合show warnings （优化建议）
   id      
     id相同执行顺序由上到下
     id不同  值越大执行顺序越靠前  id 为null最后执行
     
   select_type  
      SIMPLE    
         简单select查询  查询中不包含子查询和union
      PRIMARY    
         查询中若包含任何复杂的子查询  最外层查询被标记为
      SUBQUERY    
         在select或where列表中包含子查询
      DERIVED    
         在from列表中包含的子查询被标记为derived  mysql会递归执行这些子查询，把结果放到临时表里，也叫派生表。
      UNION     
         若第二个select出现在union之后则被标记为UNION
         
      UNION RESULT  
         从UNION表获取结果的select
      
   table   
      显示这一行的数据是关于那张表的
   type     
      显示查询使用了何种类型  
      从最好到最差  system>const>eq_ref>ref>range>index>ALL  一般来说至少达到range级别最好达到ref级别  
      system    
         表中只有一行记录  这是const的特例
      const    
         表示通过索引一次就找到了，const用于比较primary key或者union 索引  因为只匹配一行数据，所以很快  如将主键置于where列表中，mysq就能将该查询转化为一个常量
      eq_ref   
         唯一索引扫描
         primary key或unique key 索引的所有部分被连接使用，最多只返回一条复核条件的记录
         
      ref  
         非唯一性索引扫描  返回匹配某个单独的值的所有行
      range    
         只检索给定范围的行，使用一个索引来选择行，key列显示使用了那个索引
      index    
         index与ALL区别为index只遍历索引树，这通常比ALL快因为索引文件通常比数据文件小
      ALL
      
      
      
   possible_keys    
      可能使用到的索引
   key    
      实际使用到的索引
   key_len    
      索引的字节长度
   ref    
     显示索引的哪一列被使用了，如果可能的话是一个常量  那些列h或常量被用于查找索引列上的值
   rows    
      大致估算出查找结果的行数
   Extra  
     using index   
     
     using  where  
     
     
     
 索引失效案例
全值匹配  

最佳左前缀原理  
  联合索引中的索引字段一定要根据联合索引字段的顺序来查询   俗称 带头大哥不能死   
不再索引列上做任何操作  
尽量使用覆盖索引  
使用 > < != 索引失效  
is null  is not null  索引失效  
like  %abc   索引失效   但 abc% 索引不会失效  
字符串不加单引号索引失效  
or 连接 索引失效

  
  



补充   
    mysql的事务隔离级别  
                          脏读         不可重复读             幻读
     读未提交             是               是                    是  
     读已提交             否               是                    是  
     可重复读             否               否                    是  
     序列化               否                否                    否     
     
     脏读 ：  
           一个事务中读取到另一个未提交的事务中的数据  
     不可重复读  
           一个事务中读取别的事务提交的数据导致一个事务中前后两次读取的数据不一致  
     幻读  
           两个事务中读取数据后判断进行相同的操作导致后提交的事务不能执行  
           例子  比如数据库中没有id为6的数据 两个事务同时来读取数据库数据 都没有读取到 然后同时插入id为6的数据 后提交的事务不能成功  
       
     覆盖索引  
        索引包含了所有查询的字段  （因为这个字段只需要在B+TREE中获取就可以，无需再找到数据）