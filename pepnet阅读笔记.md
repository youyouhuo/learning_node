### pepnet阅读笔记
>模型解决的是多场景多任务问题
>>epnet 多场景，中不同的场景有各自的特点所以使用epnet进行提取不同domain的特性

>>> domain-side features E(d) （主要是domain的id，以及用户在domain中的相关个性化信息): including domain ID and domain-specific personalized
statistical features, such as the count of user behaviors and item
exposures in each domain

>> ppnet 个性化
>>> user/item/author-side features (F𝑢/F𝑖/F𝑎) as personalized priors for PPNet（就是用户的个性化信息和item的个性化信息）： such as user ID, item ID, author
ID (the producer of short videos in Kuaishou), and other side information features, e.g., user age/gender, item category/popularity,
