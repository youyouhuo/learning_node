# Cross-Entropy Method
  
  &emsp;&emsp;cross-entropy for monte-carlo tree search(cem with mcts)
  
  &emsp;&emsp;A Tutorial on the Cross-Entropy Method
  
  &emsp;&emsp;Learning Tetris Using the Noisy Cross-Entropy Method(ncem)
  
## 0 前言
  
  &emsp;&emsp;在调参算法中，cem（Cross-Entropy Method）是一个易实现的算法，在众多的深度强化学习论文中，也经常被用来作为对比的base。cem算法最早是用来估计一些稀有事件的发生概率，后来人们也逐渐意识到这个算法也可以作为model-free的一种方法来求解优化问题。本文在开头提到的几篇论文基础上进行简单介绍，想了解更多细节的同学可以去阅读原论文。
    
## 1 算法细节
  
  ### 1.1 最通常的版本
    
  &emsp;&emsp;首先介绍最通常的版本，来自[wiki][1]
  
  ```
   μ := −6 σ2 := 100 t := 0 maxits := 100 N := 100 Ne := 10      // Initialize parameters 
   while t < maxits and σ2 > ε do                               // While maxits not exceeded and not converged
      X := SampleGaussian(μ, σ2, N)                             // Obtain N samples from current sampling distribution
      S := exp(−(X − 2) ^ 2) + 0.8 exp(−(X + 2) ^ 2)            // Evaluate objective function at sampled points
      X := sort(X, S)                                           // Sort X by objective function values in descending order          
      μ := mean(X(1:Ne));σ2 := var(X(1:Ne))                     // Update parameters of sampling distribution                            
      t := t + 1
   return μ                                                    // Return mean of final sampling distribution as solution
  ```
  &emsp;&emsp;整体算法很简单。这里简单叙述一下：
  
  ```
  首先，假设参数服从高斯分布所以初始化的时候，需要初始化 mu sigma
  其次，需要初始化 最大迭代次数 maxits 和 每轮迭代需要的参数组个数N
  然后，需要初始化 我们最优的参数组数 Ne，这里其实是通过Ne来控制最后的参数更新范围，也就是用Ne个最优的参数来拟合理想中的最优参数，达到cross-entropy最小。
  初始化完成后，开始迭代，分为 
      a.采样新参数
      b.评估新参数
      c.根据新参数的奖励对新参数进行降序排列
      d.选取top Ne的参数用来更新均值方差
      e.进入下一轮迭代
  ```    
  ### 1.2 几个关键参数的选取
    
  &emsp;&emsp;（1）在[cem with mcts][2]中提到，topN（elite sample）的选取中，如果Ne很大，也就是很多组内参数用来预估最优分布，一个问题就是收敛太慢，
  因此文中谈到，Ne的比例实践中在[0:01; 0:1].
  <br></br>&emsp;&emsp;（2）另一个，如何理解cem中ce呢，在[cem with mcts][2]中提到，cross-entropy distance 也就是 $${D_{ce}(g|h)=\int{g(x)log(\frac{g(x)}{h(x)})dx}}$$
  这里g(x)是最优的分布，而h(x)是我们elite sample生成的分布，最终h(x)会收敛到g(x)。当我们的参数服从高斯分布时，我们只要估计h(x)的均值和方差就可以了。当然为了防止更新的步子迈得太大，在[cem with mcts][2]中提到，更新参数的时候,引入alpha来调节，公式如下： $${\mu_{t+1} =\alpha*\mu_{t} + (1-\alpha)*\mu_{elite sample}}$$ 
 $${\sigma_{t+1} = \alpha *  \sigma_t + (1- \alpha ) * \sigma_{elite sample} }$$
  <br></br>&emsp;&emsp;（3）此外，在[cem with mcts][2]中提到由于我们从高斯分布中采样的参数是没有下界的，因此在一些业务场景中（比如参数不为负）我们需要舍弃一些参数，对于这些舍弃的参数，我们可以假设他们的得分是一个非常大的负分值，在这个假设上，整体算法就不需要改变。
  此外，文中还提到通过采样时对参数取log，举例来说，假设参数的阈值是[0.1,100]，在普通情况下，50%概率取值在[0.1,50],50%概率取值在[50,100]，但是一些小的值，比如0.1到1之间可能就是我们的最优值，此时确很难被采样到，因此这里取log后，参数的值域就是一半是[0.1,3.16]一半是[3.16,100]。另外一点，文中还讲到，对参数进行离散化处理，因为文中的应用场景是蒙特拉罗树搜索，因此将参数都转化成整数值。
<br></br>&emsp;&emsp;（4）在[ncem][3]中，提到cem方法在强化学习中，虽然收敛很快，但是容易进入到局部最优中，而一个解决方法就是通过加入噪声，文中以此提出了noisy cross-entropy method。文中在计算 $\sigma^2$的时候，加入了一个噪声（这个噪声是与迭代轮数相关的常数向量），此时 $\sigma^2_{t+1} = var(X(1:Ne))+Z_{t+1}$（这里和[cem with mcts][2]记得区分开）。文中实验中采用 $z=max(5-\frac{t}{10},0)$ 。最后作者也提到 $z$可以极大的影响表现，比如衰减版的 $z=\frac{c}{t}$但是目前还没有进行优化，因为cem还是跑起来太耗时。



## 2 cem的缺点

  在[cem with mcts][2]中提到，cem收敛还是很慢的。主要是因为我们只使用了top Ne的样本来更新参数，剩下的参数中，很多参数已经表现不好了，我们还是也没有利用这部分信息。因此作者提出可以使用Multi-Armed Bandit algorithm 来加速学习。









  [1]: https://en.wikipedia.org/wiki/Cross-entropy_method
  [2]: https://content.iospress.com/articles/icga-journal/icg31303
  [3]: https://ieeexplore.ieee.org/document/6796865
