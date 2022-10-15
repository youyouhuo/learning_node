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
    
  ### 1.2 梯度的推导
    
    
  优化目标：
          
  其中 $\theta$是参数，而随机变量 $\xi$是对随机随机环境的编码。r()是策略或得到的trajectory奖励。通常来说 ${\pi }{\theta}$ 可以是随机策略，但是本文中使用的是确定性策略。
  在梯度方面，一个原始形式的随机搜索简单地计算沿随机方向的有限差分近似（finite difference approximaition），然后沿该方向采取一步而不使用 line search。具体来说，对于策略  
   ${\pi }{\theta}$ 的参数 $\theta$，我们探索的更新方向就是：
    
  $$\frac{{r(\pi }_{\theta +\nu \delta },{\xi }_{1})-r({\pi }_{\theta +\nu \delta },{\xi }_{2})}{\nu}$$
    
   对于2个i.i.d的随机变量 ${\xi_1}$和 ${\xi_2}$, ${\nu}$是一个正实数，而 ${\delta}$是服从均值为0的Gaussian vector.以上可以作为目标函数 ${{E_\theta}{E_\xi}[r({\pi}_{\theta+\nu\delta},\xi)]}$的参数 $\theta$的无偏估计。
   
   
 ### 1.3 基础版本 basic random search
  
 首先介绍一下基础版本的随机搜索：
 
 如上图，
<br></br> 1.首先初始化 ${\alpha}$, 以及每次迭代需要采样的参数个数N，用于探索的噪音的标准差 ${\nu}$ ,初始化参数 ${{\theta_0}=0}$
<br></br> 2.开始循环,采样N独立同分布的 ${\delta_i}$ 作为本轮的参数 ${\theta_j}$,分别执行这2N个方向策略 ${{\pi }_{j,k,+}(x)}={\theta_{j+\nu\delta_k}(x)}$ 这里
    
    
    
    
    
    
    
    
    
    
 [1]: https://arxiv.org/abs/1703.03864
 [2]: https://arxiv.org/abs/1703.02660
