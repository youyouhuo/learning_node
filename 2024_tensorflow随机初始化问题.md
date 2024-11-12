### 问题描述
 最近在做离线auc对比的时候，发现同一个模型在同一份数据的情况下，再运行一次后结果与上一次相比差异大，怀疑是初始化不同带来的，所以引申到当前这个问题：那就是在tensorflow中，如何固定随机种子？让随机有，但是可复现？
 
 进一步，tensorflow是否有一些神奇的种子？

### stackoverflow上的问题  [Reproducible results in Tensorflow with tf.set_random_seed](https://stackoverflow.com/questions/51249811/reproducible-results-in-tensorflow-with-tf-set-random-seed)
>>>首先解释一下tensorflow的[随机机制](https://www.tensorflow.org/api_docs/python/tf/compat/v1/set_random_seed) ，有分为 graph-level 和 operation-level。
>>>其中 tf.set_random_seed(seed) 是graph-level的。
>>>有几种情况：

>>>> 如果graph-level 和 operation-level 都没有设置，那每次都会设置一个随机的种子

>>>> 如果只设置的graph-level，那系统确定性地选择与图级种子结合的操作种子，以便获得唯一的随机序列。在同一版本的张量流和用户代码中，该序列是确定性的。然而，在不同的版本中，这个顺序可能会改变。如果代码依赖于特定种子来工作，请显式指定图形级别和操作级别种子。

>>>> 如果只设置了operation-level 那么一个默认的graph-level的种子和指定的operation-level种子将一起被使用【其实也就是只有指定的种子的部分是确定性的随机】

>>>>如果两者都设置了，那两个种子结合使用来确定随机序列。

>>>简单解释来说，如果想要tensorflow的初始化是确定性的随机，就通过 tf.set_random_seed(1234) 函数，设置全局的随机种子。

### 值得注意的是，是否能固定随机种子，还存在另一个问题，那就是使用GPU的时候 [见](https://github.com/keras-team/keras/issues/12800)
>>> 此时需要使用 tesorflow-determinism
