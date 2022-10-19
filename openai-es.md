# Evolution Strategies
  
  &emsp;&emsp;Evolution Strategies as a Scalable Alternative to Reinforcement Learning
  
## 0 前言
  
  &emsp;&emsp;在调参算法中，cem（Cross-Entropy Method）是一个易实现的算法，在众多的深度强化学习论文中，也经常被用来作为对比的base。cem算法最早是用来估计一些稀有事件的发生概率，后来人们也逐渐意识到这个算法也可以作为model-free的一种方法来求解优化问题。本文在开头提到的几篇论文基础上进行简单介绍，想了解更多细节的同学可以去阅读原论文。
    
## 1 算法细节
  
  ### 1.1 最通常的版本
    
  &emsp;&emsp;首先介绍最通常的版本，来自[wiki][1]
  
  ```
   μ := −6 σ2 := 100 t := 0 maxits := 100 N := 100 Ne := 10      // Initialize parameters 
  ```










  [1]: https://en.wikipedia.org/wiki/Cross-entropy_method
