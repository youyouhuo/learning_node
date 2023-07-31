### 某一个子模型 或者 子网络想单独独立成一个模块 用 sequentail  或者 Model，并在几个模型间同步参数
  
  sequentail的用法：https://becominghuman.ai/sequential-vs-functional-model-in-keras-20684f766057
  
  model的用法：https://www.tensorflow.org/api_docs/python/tf/keras/Model 
          
          这里通过 *args 和 **kwargs ，可以使得调用的时候 a = MyModel(a=xx,b=xxx,name="mymodel") 对model进行命名
          
          然后，可以通过 a.set_weights(b.get_weights()) 进行权重的直接覆盖
                
                也可以通过 a_params = tf.get_collection(tf.GraphKeys.GLOBAL_VARIABLES,scope="mymodel")
                         
                         b_params = tf.get_collection(tf.GraphKeys.GLOBAL_VARIABLES,scope="mymodel1")
                         
                         TAU=0.1  
                         
                         soft_replace = [tf.assign(ta,(1-TAU)*ta + TAU*ea) for ta,ea in zip(a_params,b_params)
                        
                        sess.run(soft_replace) 进行软替代
