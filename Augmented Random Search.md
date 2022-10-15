# Augmented Random Search
  
  Simple random search provides a competitive approach to reinforcement learning
  
## 0 前言
  
  在model-free reinforcement learning中，一个广泛存在的认知就是在parameter space中基于random search的方法总是比在space of actions搜索的方法差。但是这篇文章告诉我们，
  在 static，linear ploicy的continuous control 中，随机搜索也是能够达到state-of-art的，而且这种方法还是很快的，大概比benchmarks中最快的model-free方法快15倍。
    
## 1 算法细节
  
  ### 1.1 启发点
    
  论文原始的启发点来自2篇论文1是[openai-es][1],论文1告诉我们，Evolution Strategies (ES)可以进行分布式的训练然后比一些之前提出的方法都更快。另一个启发点来自[Towards generalization and simplicity in
continuous control][2]，
  linear policies可以通过natural policy gradients来训练达到一个很好的效果。而本文是在两者基础上，建立的一个基于随机搜索的linear policy来进行很好的完成任务。
    
  ### 1.2 算法细节
    
 优化目标：
          
 其中 $\theta$是参数，而随机变量 $\xi$是对随机随机环境的编码。r()是策略或得到的trajectory奖励。通常来说 ${\pi }_{\theta}$  可以是随机策略，但是本文中使用的是确定性策略。
    
    
    
    
    
    
    
    
    
    
    
    
    
    
 [1]: https://arxiv.org/abs/1703.03864
 [2]: https://arxiv.org/abs/1703.02660
