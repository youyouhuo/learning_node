# Augmented Random Search
  
  &emsp;&emsp;Simple random search provides a competitive approach to reinforcement learning
  
## 0 前言
  
  &emsp;&emsp;在model-free reinforcement learning中，一个广泛存在的认知就是在parameter space中基于random search的方法总是比在space of actions搜索的方法差。但是这篇文章告诉我们，在 static，linear ploicy的continuous control 中，随机搜索也是能够达到state-of-art的，而且这种方法还是很快的，大概比benchmarks中最快的model-free方法快15倍。
    
## 1 算法细节
  
  ### 1.1 启发点
    
  &emsp;&emsp;论文原始的启发点来自2篇论文1是[openai-es][1],论文1告诉我们，Evolution Strategies (ES)可以进行分布式的训练然后比一些之前提出的方法都更快。另一个启发点来自[Towards generalization and simplicity in
continuous control][2]，
  linear policies可以通过natural policy gradients来训练达到一个很好的效果。而本文是在两者基础上，建立的一个基于随机搜索的linear policy来进行很好的完成任务。
    
  ### 1.2 梯度的推导
    
    
  &emsp;&emsp;优化目标：
          
  &emsp;&emsp;其中 $\theta$是参数，而随机变量 $\xi$是对随机随机环境的编码。r()是策略或得到的trajectory奖励。通常来说 ${\pi }{\theta}$ 可以是随机策略，但是本文中使用的是确定性策略。
  在梯度方面，一个原始形式的随机搜索简单地计算沿随机方向的有限差分近似（finite difference approximaition），然后沿该方向采取一步而不使用 line search。具体来说，对于策略  
   ${\pi }{\theta}$ 的参数 $\theta$，我们探索的更新方向就是：
    
  $$\frac{r(\pi_{\theta +\nu\delta},\xi_1)-r(\pi_{\theta -\nu \delta},\xi_2)}{\nu}$$
    
   对于2个i.i.d的随机变量 ${\xi_1}$和 ${\xi_2}$, ${\nu}$是一个正实数，而 ${\delta}$是服从均值为0的Gaussian vector.以上可以作为目标函数 ${{E_\theta}{E_\xi}[r({\pi}_{\theta+\nu\delta},\xi)]}$的参数 $\theta$的无偏估计。
   
   
 ### 1.3 基础版本 basic random search
  
 &emsp;&emsp;首先介绍一下基础版本的随机搜索：
 
 如上图，
<br></br> &emsp;&emsp;1.首先初始化 ${\alpha}$, 以及每次迭代需要采样的参数个数N，用于探索的噪音的标准差 ${\nu}$ ,初始化参数 ${{\theta_0}=0}$
<br></br> &emsp;&emsp;2.开始循环,
<br></br> &emsp;&emsp;&emsp;&emsp;采样N独立同分布的 ${\delta_i}$ 作为本轮的参数 ${\theta_j}$,
<br></br> &emsp;&emsp;&emsp;&emsp;分别执行这2N个方向策略,即 ${\pi_{j,k,+}(x)=\theta_{j+\nu\delta_k}(x)}$ 以及 ${\pi_{j,k,-}(x)=\theta_{j-\nu\delta_k}(x)}$ 
<br></br> &emsp;&emsp;&emsp;&emsp;更新参数 $${\theta_{j+1}=\theta_j+\frac{\alpha}{N} \sum ^N_{k=1} {[r(\pi_{j,k,+})-r(\pi_{j,k,-})]\delta_k}}$$

### 1.4 增强版 augument random search

&emsp;&emsp;下面介绍一下增强版的随机搜索：
如上图，主要改动点有以下3点：
<br></br> &emsp;&emsp;1. 对状态的normalization，简单的写法就是 $\frac{x-\mu_j}{\sigma_j}$,这里写成了 $${(x-\mu_j)diag{(\Sigma_j)}^{-\frac {1} {2}}}$$ 以此来消除不同状态的量纲的差异，比如一个状态是[90,100]之间，一个状态是[-1,1]，那么只要一个很小的改变就会朝着状态值大的方向移动，而值小的带来的影响更加有限。openai-es里面是采用了virtual batch normalization来近似达到这一效果。这里的均值和方差是2N个方向计算得来的。
<br></br> &emsp;&emsp;2. 对奖励的scaling，使用奖励的标准差 ${\sigma_R}$ 来scaling。因为随着迭代的进行，得到的奖励可能越来越大，直接用来更新参数会导致在迭代的后期，步长太大而带来较大的震荡。当然，也可以通过调整 $\alpha$ 来获得相同效果。这里的${\sigma_R} 是2N个方向计算来的
<br></br> &emsp;&emsp;3. 只使用top 表现的方向来进行参数更新。
<br></br> &emsp;&emsp;&emsp;&emsp;在这里，如果 $r({+\delta_k})$ > ${r(-\delta_k)}$,那最后的参数更新就是 ${+\delta_k}$，反之则是 ${-\delta_k}$,但是因为  $r({+\delta_k})$ 和 ${r(-\delta_k)}$都是在有噪声的情况下进行评估的，此时可能更新的方向出错，比如即使 ${-\delta_k}$ 更好，但是我们更新的方向却是 ${+\delta_k}$，极端情况下，更新的方向不在两者上，而是一个其他方向而到达局部最优的效果。
<br></br> &emsp;&emsp;&emsp;&emsp;为了解决这个问题，这里提出，对奖励根据 ${max\\{r(\pi_{j,k,+}),r(\pi_{j,k,-})\\}}$进行降序排列，然后只取top b的方向进行更新。
    
    
## 2 对比openai-ES的不同点
&emsp;&emsp;1. es使用Adam来更新参数
<br></br> &emsp;&emsp;2. es将reward转换成ranking，然后使用ranking来更新参数，这种方式下，更新会更加的robust，而本文这里使用的是reward的标准差
<br></br> &emsp;&emsp;3. es中，使用状态分桶（bins the action space) ，而本文并没有使用
<br></br> &emsp;&emsp;4. es中，使用 神经网络并且结合 virtual batch normalization，而本文中并没有使用


## 3 总结
 &emsp;&emsp;本文提出的ars在没有使用神经网络的情况下，只使用简单的线性策略，就达到了openai-es的效果，同时耗时上小，所以能够尝试更多的随机种子。让我们在方法的选择上又多了一种。

    
    
   
    
 [1]: https://arxiv.org/abs/1703.03864
 [2]: https://arxiv.org/abs/1703.02660
