### 多目标融合辅助loss学习

 > 论文见 Unsupervised Ranking Ensemble Model for Recommendation
 >>> 核心思路是在多目标融合中，首先是排序相似loss（就是当前目标的打分pred要和模型的打分score表现类似，sum( tanh(pred[i]-pred[j])*tanh(score[i]-score[j]))，
 >>> 加入额外的2个辅助loss，一个是重构个模型打分的重构loss【loss系数是0.1】，一个是辅助loss学习是否曝光【loss系数是0.5】

### 交叉熵损失 融合其他辅助loss学习

> 论文见 Understanding the Ranking Loss for Recommendation with Sparse User Feedback
>>> 核心思路是探讨 交叉熵损失中，如果样本很稀疏的情况下，如何通过辅助loss【比如正负样本对，阿里的jrc，fucalloss等等】强化效果，来缓解负样本的梯度消失问题
>>> 代码见 https://github.com/SkylerLinn/Understanding-the-Ranking-Loss/tree/main
>>> combined_pair_loss的tensorflow的实现可以参考 https://stackoverflow.com/questions/47132665/cartesian-product-in-tensorflow

 ```
a = tf.constant([1,2,3]) 
b = tf.constant([4,5,6,7]) 

tile_a = tf.tile(tf.expand_dims(a, 1), [1, tf.shape(b)[0]])  
tile_a = tf.expand_dims(tile_a, 2) 
tile_b = tf.tile(tf.expand_dims(b, 0), [tf.shape(a)[0], 1]) 
tile_b = tf.expand_dims(tile_b, 2) 

cartesian_product = tf.concat([tile_a, tile_b], axis=2) 

cart = tf.Session().run(cartesian_product) 

print(cart.shape) 
print(cart)
```
