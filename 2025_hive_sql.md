### 关于limit offset
> mysql里分页一般用limit来[实现](https://www.cnblogs.com/dongml/p/10953846.html)  
> 当limit后面跟两个参数的时候，第一个数表示要跳过的数量，后一位表示要取的数量  
> select* from article LIMIT 1,3  
> select * from article LIMIT 3 OFFSET 1  
>上面两种写法都表示取2,3,4三条条数据  


### 关于 row_number, rank(), dense_rank() 
> [简单来说](https://zhuanlan.zhihu.com/p/133469603)  
> rank()排序相同时会重复，总数不变，即会出现1、1、3这样的排序结果；  
> dense_rank()排序相同时会重复，总数会减少，即会出现1、1、2这样的排序结果；  
> row_number()排序相同时不会重复，会根据顺序排序。  
