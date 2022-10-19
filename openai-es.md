# Evolution Strategies
  
  &emsp;&emsp;Evolution Strategies as a Scalable Alternative to Reinforcement Learning
  
## 0 前言
  
  &emsp;&emsp;Evolution Strategies作为一个黑盒算法已经被广泛用于求解各种策略搜索的问题，也通常会作为强化学习对比的base。而作者在本文中，通过设计一种通信机制，让ES能够在大规模的机器中（1440台）进行通信，一起训练神经网络的参数，极大的加速了训练的效率。同时，也发现ES算法能够无视actor frequency，更多处理更长的delayed rewards而不需要对值函数进行估计。
    
## 1 算法细节
  
  ### 1.1 背景知识
    
  &emsp;&emsp;Evolution Strategies (ES)作为一种黑盒算法（black box optimization algorithms），它的启发是的搜索方式来自自然进化：在每次迭代（“生成”）中，一组参数向量（“基因型”）受到扰动（“突变”），并评估它们的目标函数值（“适应度”）。然后将得分最高的参数向量重新组合以形成下一代的种群，并重复此过程，直到目标完全优化。在ES算法中，这类算法的不同之处在于它们如何代表种群以及它们如何执行突变和重组。在众多算法中，最有代表的算法就是cma-es，它是通过full-covariance multivariate Gaussian来表示种群。
  
  &emsp;&emsp;在本文中，作者使用的是另一种进化算法，叫做natural evolution strategies (NES)。NES用基于参数 $\psi$的分布 $p_{\psi}(\theta)$来表示种群，通过在参数空间中使用stochastic gradient ascent来搜索 $\psi$以最大化 $E_{\theta \sim p_{\psi }}F(\theta)$,其中F()是作用于参数$\theta$的目标函数。为了最大化 $E_{\theta \sim p_{\psi }}F(\theta)$，我们可以采用类似于REINFORCE的计算方式来得到梯度 $\nabla E_{\theta \sim p_\psi} F(\theta)$。推导如下：
  
  
  
  ```
   μ := −6 σ2 := 100 t := 0 maxits := 100 N := 100 Ne := 10      // Initialize parameters 
  ```










  [1]: https://en.wikipedia.org/wiki/Cross-entropy_method
