#tensorflow 2.0 神经网络实战  
###1.0--初识tensorflow  

tensorflow1.x中如下规则必须被遵守（图的定义和执行都是静态的）但在2.0中，并不是必须的。  

#### 1）tf.graph  
    
    tensorflow是一个c++库，我们只是使用pyhon来构造数据流    
    
    存在两种方式构建图  
      
      显式：每个应用可以有多个图，  
      
      隐式：在不显式定义的情况下，默认会定义一个图，此时只能使用单个图   
    
    tf.graph表示的是图的集合，通过集合机制来存储与图结构相关的元数据 

#### 2）tf.operation 与 tf.tensor  
    
    在上下文中，每个API的调用定义了tf.operation（节点），每个节点又可以有多个输入输出的tf.tensor（边）
    
    在图中，每个节点都是唯一的，如果存在同名的，则会新增数字后缀来区分
    
    也可以通过tf.name_scope函数定义的一个上下文，为该上下文中所有的运算添加命名范围前缀。这个明明范围前缀是用"/"分割的一个名字列表。


### 2.0 loss的含义

   loss =  tf.losses.log_loss(label,pred,weight,reduction="none")
    
   这里，假设 label=[batch_size,1]    pred = [batch_size, 1]   weight=[batch_size, 1]
   
   如果reduction= “weight_mean"    则   loss 是一个值，且为     loss/weight_sum
   
   如果 default       则 loss是一个值，且为 loss/n
   
   如果 reduction=weight_sum     则 loss 是一个值，且为 loss
   
   如果 reduction=weight_sum_by_nonzero_weights    则 loss = loss/n
   
   如果 reduction=none，    则不进行聚合，每一个batch都是一个值
   
   
import tensorflow as tf
one = tf.constant([0.533007599,0.698074224,0.67699626,0.702912419,0.851406625,0.674418677,0.701772307,0.785025003,0.647020862,0.727987531,0.511690131])
label = tf.constant([1,0,0,1,1,0,1,1,1,0,0])

weight = tf.constant([2,1,1,2,1.2,1,1,2,1,1,1])

out = tf.losses.log_loss(tf.reshape(label,[11,1]),tf.reshape(one,[11,1]),weights=tf.reshape(weight,[11,1]),reduction="none")

#,reduction="weighted_mean" 0.62666506         sum((ylogp+(1-y)logp)*weight)/sum(weight)
#,default 0.8089676                            sum((ylogp+(1-y)logp)*weight)/batch_size
#,reduction="weighted_sum" 8.8986435           sum((ylogp+(1-y)logp)*weight)
#,reduction="weighted_sum_by_nonzero_weights"   0.8089676     sum((ylogp+(1-y)logp)*weight)/sum(非0的weight个数）
#, reduction="none"                            (ylogp+(1-y)logp)*weight
"""
[[1.2584387 ]
[1.1975738 ]
 [1.130091  ]
 [0.7050457 ]
 [0.19303836]
 [1.1221428 ]
 [0.35414606]
 [0.48407912]
 [0.43537652]
 [1.3019071 ]
 [0.7168049 ]]
"""

with tf.Session() as sess:
    print(sess.run(out))


### tf.identity 用法

     构建一个新的op，注意这里是通过 y=tf.identity(x） 构建一个新的op，也可以用于在不同设备之间的传递，见[This function can also be used to explicitly transfer tensors between devices. For example, to transfer a tensor in GPU memory back to host memory](https://www.tensorflow.org/api_docs/python/tf/identity)
     
     也多见于结合 tf.control_denpendencies() 进行使用，因为tf.control_denpendencies()内部是需要  https://stackoverflow.com/questions/34877523/in-tensorflow-what-is-tf-identity-used-for

###  tf.control_denpendencies()  用法
    比较经典的是 gradnorm的实现，需要注意的是，这里一定是 对执行有先后顺序的情况下来进行处理，一定是抓住依赖 ,一个应用见 grandorm 实现https://github.com/vpetren/gradnorm_tf/blob/master/gradnorm_tf.py

### tensorflow中模型加载 用法

    https://blog.csdn.net/b876144622/article/details/79962727

    https://cloud.tencent.com/developer/article/1882043
    
   
   
   
   
