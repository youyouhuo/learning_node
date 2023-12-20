### pepnet阅读笔记
>pepnet 模型解决的是多场景多任务问题
>> 1.epnet 多场景，中不同的场景有各自的特点所以使用epnet进行提取不同domain的特性

>>> domain-side features E(d) （主要是domain的id，以及用户在domain中的相关个性化信息): including domain ID and domain-specific personalized
statistical features, such as the count of user behaviors and item
exposures in each domain

>> 2.pepnet实现上 https://zhuanlan.zhihu.com/p/611532716

>>> 请教个问题， id特征作为gate的输入之后，还会同时作为主网络的输入吗

>>> ppnet用独立一套id特征，不参与主网络计算，我的场景下这样做有收益

>>> 是的，比如主网络用64维的uid，ppnet用32维的uid

>>> 是指这个id特征的表征不共享 单独一套吧？这个特征本身主网络也有的吧

>> 2. ppnet 个性化

>>> user/item/author-side features (F𝑢/F𝑖/F𝑎) as personalized priors for PPNet（就是用户的个性化信息和item的个性化信息）： such as user ID, item ID, author ID (the producer of short videos in Kuaishou), and other side information features, e.g., user age/gender, item category/popularity,

>ppnet 快手牛亚男：基于多Domain多任务学习框架和Transformer，搭建快手精排模型 https://mp.weixin.qq.com/s/C4deyo0-LVgfO7AcN1hH2w
>>> 从业界的演化来看，一方面是从特征的交叉角度，另一方面是从用户的行为序列建模来提升模型个性化。这里DNN核心为全连接网络。 特征全局共享，主要用来捕捉全局用户和短视频的特征。要做到真正的千人千面，需要用户个性化的特征更强一些。所以当时我们探索了如何为DNN网络增加个性化。我们尝试了一些方法，最开始尝试用stacking的方法，在最顶层或中间加一些user独有的一些网络，对网络的参数，每个用户是不同的，但是收益甚微。然后我们尝试了另外一种方式，受LHUC的启发，思想来源于语音识别，给每个用户学习个性化的偏置项。

