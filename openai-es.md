# Evolution Strategies
  
  &emsp;&emsp;Evolution Strategies as a Scalable Alternative to Reinforcement Learning
  
## 0 前言
  
  &emsp;&emsp;Evolution Strategies作为一个黑盒算法已经被广泛用于求解各种策略搜索的问题，也通常会作为强化学习对比的base。而作者在本文中，通过设计一种通信机制，让ES能够在大规模的机器中（1440台）进行通信，一起训练神经网络的参数，极大的加速了训练的效率。同时，也发现ES算法能够无视actor frequency，更多处理更长的delayed rewards而不需要对值函数进行估计。
    
## 1 算法细节
  
  ### 1.1 背景知识-nes
    
  &emsp;&emsp;Evolution Strategies (ES)作为一种黑盒算法（black box optimization algorithms），它的启发是的搜索方式来自自然进化：在每次迭代（“生成”）中，一组参数向量（“基因型”）受到扰动（“突变”），并评估它们的目标函数值（“适应度”）。然后将得分最高的参数向量重新组合以形成下一代的种群，并重复此过程，直到目标完全优化。在ES算法中，这类算法的不同之处在于它们如何代表种群以及它们如何执行突变和重组。在众多算法中，最有代表的算法就是cma-es，它是通过full-covariance multivariate Gaussian来表示种群。
  
  &emsp;&emsp;在本文中，作者使用的是另一种进化算法，叫做natural evolution strategies (NES)。NES用基于参数 $\psi$的分布 $p_{\psi}(\theta)$来表示种群，通过在参数空间中使用stochastic gradient ascent来搜索 $\psi$以最大化 $E_{\theta \sim p_{\psi }}F(\theta)$,其中F()是作用于参数 $\theta$的目标函数。为了最大化 $E_{\theta \sim p_{\psi }}F(\theta)$，我们可以采用类似于REINFORCE的计算方式来得到梯度 $\nabla E_{\theta \sim p_\psi} F(\theta)$。推导如下：
  $$\nabla E_{\theta \sim p_\psi} F(\theta) = E_{\theta \sim p_{\psi }}[F(\theta)\nabla_\psi logp_{\psi}(\theta)]$$
  $$\nabla_\psi logp_{\psi}(\theta)=\nabla_\psi\frac{(\theta-\psi)^2}{{\sigma}^2} = \frac{\theta-\psi}{{\sigma}^2}$$
  其中 $\theta \sim p_\psi$,而 $p_\psi$ 是均值为 $\psi$ 方差为 ${\sigma}^{2}\mathcal{I}$的高斯分布，因此对 $\psi$梯度就是 $\frac{\theta-\psi}{{\sigma}^2}$。 $\theta=\psi+\sigma\epsilon$,这里 $\epsilon \sim \mathcal{N}(0,\mathcal{I})$,因此，我们可得：
  $$\frac{\theta-\psi}{{\sigma}^2}=\frac{\epsilon}{\sigma}$$
  带回到上面的公式，得到（这里 $\psi$ 其实是上一轮的均值 $\theta$)：
  $${\nabla E_{\theta \sim p_\psi} F(\theta) =\frac1{\sigma}E_{\theta \sim p_{\psi }}[F(\theta)\epsilon]=\frac1{\sigma}E_{\epsilon \sim \mathcal{N}(0,\mathcal{I})}[F(\psi+\sigma\epsilon)\epsilon]}$$
  最后得到论文里的公式: $$\nabla_\theta E_{\epsilon \sim \mathcal{N}(0,\mathcal{I})}F(\theta+\sigma\epsilon) = \frac1{\sigma}E_{\epsilon \sim \mathcal{N}(0,\mathcal{I})}[F(\theta+\sigma\epsilon)\epsilon]$$
  
 ### 1.2 背景知识-mirrored sampling
 
 &emsp;&emsp; mirrored sampling 是一种黑盒算法中，比较常用的采样方式，简单来说，就是采样一个 $\epsilon \sim \mathcal{N}(0,\mathcal{I})$，然后通过 $\epsilon$ 和 $-\epsilon$ 就得到2组参数,最后更新的时候，使用 $r(\theta_{\epsilon})-r(\theta_{-\epsilon})$ 作为 $\epsilon$的最终的奖励用于参数更新。
 
 
 ### 1.3 背景知识-fitness shaping
 
 &emsp;&emsp; [fitness shaping][2]是一种对奖励进行变换的技巧。通过对奖励的分布做一个变换，降低一些离群点对整体的影响，防止陷入到局部最优。操作上，首先对奖励进行降序排列，然后根据排的序分别赋值。在[fitness shaping][2]原论文中，赋值逻辑是：
  $$r_k=\frac{max(0,log(\frac\lambda2)+1-log(k))}{\Sigma^\lambda_{j=1}max(0,log(\frac\lambda2+1)-log(j))}-\frac1\lambda$$
  
  这里 $r_1 >r_2>....>r_\lambda$,也就是根据奖励降序排列，而 $\lambda$ 是采样的个数。
  当然，openai-es在实现的过程中，并没有这么复杂的逻辑，而是将奖励大致缩放到[-0.5,0.5]之间，具体可以看下面的代码[openai fitness shapling][3]
  
  ```
  def compute_ranks(x):
    """
    Returns ranks in [0, len(x))
    Note: This is different from scipy.stats.rankdata, which returns ranks in [1, len(x)].
    """
    assert x.ndim == 1
    ranks = np.empty(len(x), dtype=int)
    ranks[x.argsort()] = np.arange(len(x))
    return ranks


def compute_centered_ranks(x):
    y = compute_ranks(x.ravel()).reshape(x.shape).astype(np.float32)
    y /= (x.size - 1)
    y -= .5
    return 
  ```
### 1.4 背景知识-virtual batch normalization
  
&emsp;&emsp; [virtual batch normalization][4]最早是一种训练gan的时候的加速技巧。我们都是知道batch normalization是在一个batch内，对所有的数据进行一个归一化。但是这种简单的归因化会出现一个问题，那就是一个输入x的输出高度依赖于同minibatch的其他样本。为了解决这个问题，virtual batch normalization引入一个reference batch，这个reference batch在训练一开始就指定好，然后其他batch就是在reference batch的统计信息之上进行归一化操作[quora-what is virtual batch normalization][5],[stackoverflow-what is virtual batch normalization][6],代码实现可以参考[virtual batch normalization-implement][7]。


### 2.1 openai-es 基础版算法

&emsp;&emsp; 在补充完几个基本的知识后，我们来介绍openai-es的算法细节。具体可以看下图：

可以看到，整体算法还是比较简单，这里有一个值得注意的点：

  （a)在原文中，作者假设参数服从的高斯分布的方差 $\sigma^2$是固定的，作者解释是因为发现在训练的方式下，并没有发现啥优势。所以作为一个超参数来设置。
  （b）在原文中，作者提到，使用virtual batch normalization 可以极大提升策略的可用性，但是在算法图中，也没有具体的写出来。
  （c）在原文中，作者提到，使用mirrored sampling 和 fitness shaping来降低整体奖励的方差。这在算法图中，也没有写出来。（在实现中，将奖励简单缩放到[-0.5,0.5]中了，比原版fitness shaping更加简单）。
  （d）在原文中，作者提到，使用了weight decay来更新policy network，来防止参数更新的步子太大了。






  [1]: https://en.wikipedia.org/wiki/Cross-entropy_method
  [2]: https://www.jmlr.org/papers/volume15/wierstra14a/wierstra14a.pdf
  [3]: https://github.com/openai/evolution-strategies-starter/blob/951f19986921135739633fb23e55b2075f66c2e6/es_distributed/es.py
  [4]: https://arxiv.org/abs/1606.03498
  [5]: https://www.quora.com/What-is-virtual-batch-normalization-I-read-about-it-in-the-evolution-strategies-paper-by-Salimans-et-al-2017-but-I-could-not-find-a-concrete-definition
  [6]: https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&ved=2ahUKEwjZrs-74vj6AhVFx2EKHTUVCSAQFnoECCsQAQ&url=https%3A%2F%2Fstackoverflow.com%2Fquestions%2F50917182%2Fwhat-is-the-difference-between-virtual-batch-normalization-and-batch-normalizati&usg=AOvVaw2VrsQVvGKeHsDYMC2YjEWP&cshid=1666611453986331
  [7]: https://github.com/openai/improved-gan/blob/4f5d1ec5c16a7eceb206f42bfc652693601e1d5c/imagenet/model.py
