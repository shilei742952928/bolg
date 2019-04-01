sql中使用in 或者 or查询时非主键字段的索引会失效，主键索引有时生效有时失效  
  
  1.在执行常量等值查询时，改变索引列的顺序并不会改变explain的执行结果，因为mysql的底层优化器会进行优化，但是推荐按照索引顺序列编写sql语句  
  2.范围查询右边的索引列失效 但是范围当前位置的索引时有效的   
     最左前缀的字段时范围查询的话可能都不会走索引
    

```
mysql> explain select * from user where name >= 'shilei' and idNo = 10;
+----+-------------+-------+-------+---------------+-----------+---------+------+------+-------------+
| id | select_type | table | type  | possible_keys | key       | key_len | ref  | rows | Extra       |
+----+-------------+-------+-------+---------------+-----------+---------+------+------+-------------+
|  1 | SIMPLE      | user  | range | name_idNo     | name_idNo | 263     | NULL |    1 | Using where |
+----+-------------+-------+-------+---------------+-----------+---------+------+------+-------------+
1 row in set (0.04 sec)

mysql> explain select * from user where name >= 'ilei' and idNo = 10;
+----+-------------+-------+------+---------------+------+---------+------+--------+-------------+
| id | select_type | table | type | possible_keys | key  | key_len | ref  | rows   | Extra       |
+----+-------------+-------+------+---------------+------+---------+------+--------+-------------+
|  1 | SIMPLE      | user  | ALL  | name_idNo     | NULL | NULL    | NULL | 100396 | Using where |
+----+-------------+-------+------+---------------+------+---------+------+--------+-------------+
1 row in set (0.00 sec)
```  
    
    3.利用最左前缀法则，中间兄弟不能断，因此用到了c1 c2 索引c3用在了排序中c4没有用到索引  
       
```
select * from user where c1= 'c1' and c2 = 'c2' and c4 = 'c4' order by c3
```
  
  4.  第一个c2  c3 会走索引 第二个不会  第二个会有using filesort 文件重排序  
  
```
select * from user where c1='c1'and c4 = 'c4' order by c2 , c3  
select * from user where c1='c1'and c4 = 'c4' order by c3 , c2
```
  
   下面这个因为c2是个定值 mysql会进行优化  不会出现using filesort 故c2 c3 都可以使用到索引  
   
```
select * from user where c1='c1'and c2 = 'c2' and c4 = 'c4' order by c3 , c2
```    
  5.group by  执行前会order by     
  
  6.使用覆盖索引优化  
  
  7.排序一个升序一个降序不能使用到索引  
  
  8.对于排序来说，多个相等条件也是范围查询   
    
```
select c1 from user where c1 in ('a1','b1') order by c2,c3  产生了using filesort
```  

总结  
   1.mysql支持两种排序filesort和index   using index 是指mysql扫描索引完成排序   index 效率高 filesort效率低  
   2.where 高于having  
     
     
     
     内功心法  
        全值匹配我最爱，最左前缀要遵守；  
        带头大哥不能死，中间兄弟不能断；  
        索引列上少计算，范围之后全失效；  
        like百分写右边，覆盖索引不写星；  
        不等空值还有or，索引失效要少用；
   
   
     
     
     补充  
       in exists    ？？？？
         
   
   
   
   
   
   
   
   
   
   
   
   
   
   
   
   
   
   
   
   

   

 



