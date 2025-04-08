### 关于limit offset
> mysql里分页一般用limit来[实现](https://www.cnblogs.com/dongml/p/10953846.html)  
> 当limit后面跟两个参数的时候，第一个数表示要跳过的数量，后一位表示要取的数量  
>> select* from article LIMIT 1,3  
>> select * from article LIMIT 3 OFFSET 1  
>> 上面两种写法都表示取2,3,4三条条数据  


### 关于 row_number, rank(), dense_rank() 
> [简单来说](https://zhuanlan.zhihu.com/p/133469603)  
- rank()排序相同时会重复，总数不变，即会出现1、1、3这样的排序结果；
- dense_rank()排序相同时会重复，总数会减少，即会出现1、1、2这样的排序结果；  
- row_number()排序相同时不会重复，会根据顺序排序。  


### 关于order by
> order by 可以指定一个或者多个字段进行排序，字段的顺序决定排序的优先级。可以使用asc（升序) desc（降序) 关键子来制定排序方式，默认为升序 [来自](https://blog.51cto.com/u_16175495/7344099)  
- 注意点1：字段的顺序分厂重要，决定了排序的优先级。  
- 注意点2：如果排序过程中遇到相同的值，hive将安好默认的字母驯熟进行排序，如果需要使用其他排序规则，请使用hive同的相应函数或表达式。  
- 注意点3：多个字段，根据字段的顺序先进行分别排序  
>> 举例:  order by score desc，age asc，name asc。 含义就是：先根据成绩降序，然后成绩相同，按年龄升序，最后年龄和成绩相同，按姓名的字母许进行排序。  
- 注意点4：默认情况为按照升序方式排序  
- 注意点5：多个字段，没指定情况下默认是升序。 [来自 ](https://blog.csdn.net/wangwangstone/article/details/112730026)  
>> 举例： order by score,age desc。 含义就是 score升序，age降序  
>> 举例： order by score desc,age。 含义就是 score 降序，age升序。  
>> 举例： order by score desc, age desc。 含义就是 score降序，age降序。  
- 注意点6： 多字段排序可能会影响查询性能，特别对于大型数据集进行排序时，在进行多字段排序时，建议使用分区或索引来提高性能。

### collet_list 保持原有顺序

- https://zhuanlan.zhihu.com/p/608430746
- 在collect_list之前进行distribute by  xxx sort by xxx 来进行保持

```
select 
    uid,
    collect_list(dates) as dates_list,
    collect_list(score) as score_list
from 
(
    select 
        *
    from 
        base_data
    distribute by uid sort by uid, dates asc
)t0
group by uid
;

```
