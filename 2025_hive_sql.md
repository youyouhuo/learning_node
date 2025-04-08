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

### sql学习

https://blog.csdn.net/weixin_42499444/article/details/113319711

### hive over 窗口函数
https://www.cnblogs.com/erlou96/p/13590358.html

### lead  lag 函数
https://blog.csdn.net/kent7306/article/details/50441967
https://zhuanlan.zhihu.com/p/349291325

这两个函数可以在一次查询中取出同一字段的前N行的数据(lag)和后N行的数据(lead)作为独立的列,从而更方便地进行进行数据过滤，该操作可代替表的自联接，且效率更高

LAG(col,n,DEFAULT) 用于统计窗口内往上第n行值
第一个参数为列名，第二个参数为往上第n行（可选，默认为1），第三个参数为默认值（当往上第n行为NULL时候，取默认值，如不指定，则为NULL）

比如 查询顾客上一次等购买时间：
select lag(orderDate,1,"-9999") over(partition by name order by orderDate) as lastTimeOrder from xxxx.


LEAD(col,n,DEFAULT) 用于统计窗口内往下第n行值
第一个参数为列名，第二个参数为往下第n行（可选，默认为1），第三个参数为默认值（当往下第n行为NULL时候，取默认值，如不指定，则为NULL）


### ntile() 函数：
https://www.cnblogs.com/skyEva/p/7552129.html
用于将分组数据按照顺序切分成n片，返回当前记录所在的切片值

### percentile() 函数：
https://blog.csdn.net/weixin_34203426/article/details/86015549

中位数函数: percentile
语法: percentile(BIGINT col, array(p1 [, p2]…)) 
返回值: array<double>
说明: 之后后面可以输入多个百分位数，返回类型也为array<double>，其中为对应的百分位数。
举例：
select percentile(score,<0.2,0.4>) from udftest； 取0.2，0.4位置的数据

