# cma-es
  
  &emsp;&emsp;The CMA Evolution Strategy: A Tutorial
  
## 0 前言
  
  &emsp;&emsp;cma-es作为一个比较成功的算法，已经被众多的使用者所称赞。得益于其中超参数的简单配置，以及其自动调整的能力，使用起来易上手，收到许多科研工作者的推崇。下面结合论文进行简单的梳理。

## 1 算法细节
  
  ### 1.1 整体流程之sample-select and Recombination 
  #### 1.1.1 整体流程之sample
  
  在使用cem的过程中，首先是sample，然后是select和recombination。sample阶段：
  $${x}^{(g+1)}_{k}\sim {m}^{(g)}+{\sigma }^{(g)}\mathcal{N}(0,{C}^{(g)})$$
  其中g表示第g轮，一轮中的第k个参数（比如这轮采样100个参数，那k就是100中的1个）m是上一轮的均值，C是采样的方差。 $\sigma$ 可以看成是更新的step-size。
  现在需要处理的就是，怎么计算m, $\sigma$ 和C.
  
  #### 1.1.2 整体流程之select and Recombination 
  $$m^{(g+1)}=\sum ^\mu_{i=1}{w_i}{x}^{(g+1)}_{i:\lambda}\tag{6}$$
  
  $${\sum^\mu_{i=1}{w_i}=1}\tag{7}$$
  
  $${w_1>=w_2>=w3>=......w_\mu>0}$$
  
  这里 ${x}^{(g+1)}_{i:\lambda }$ 中的 $i:\lambda$表示的是在种群 $\lambda$中排序第i位置的参数（根据损失函数降序排列，损失越小，参数越优质，i越小）。此外 $\mu$是所选择的参数集合大小，通常 $\mu<=\lambda$。
  
  这里引入一个新的参数 $$\mu_{eff} = \frac{\sum^\mu_{i=1}{\left|w_i\right|}^2} {\sum^\mu_{i=1}w^2_i} \tag{8}$$

  进一步，可以将公式6写成增量式的
  
  $${m^{(g+1)}=m^{(g)} + c_m{\sum^\mu_{i=1}{w_i (x^{(g+1)}_{i:\lambda}-m^{(g)})}} }\tag{9}$$ 
  
  这里是，其中 ${c_m { \sum^ {\mu}_ {i=1} {w_i} } = 1}$。
  
  
  接下来是估计 Covariance Matrix，一个empirical covariance matrix是： 
  
  $$C^{(g+1)}_{emp} =  \frac{1}{\lambda-1} * coma \tag{10}$$
  
  $$coma = \sum^\lambda_{i=1} {((x^{(g+1)}_i-mu)*(x^{(g+1)}_i-mu)^T)}$$
  
  $$mu = \frac{1}{\lambda} \sum^\lambda_{j=1} {x^{(g+1)}_i}$$
  
  这里的 $C^{(g+1)}_{emp}$ 是 $C^{(g)}$的unbias estimator。此时我们考虑另一个版本：
  
  $$C^{(g+1)}_{\lambda} =  \frac{1}{\lambda} * coma \tag{11}$$
  
  $$coma = \sum^\lambda_{i=1} {((x^{(g+1)}_i-m^{(g)})*(x^{(g+1)}_i-m^{(g)})^T)}$$
  
  这里 $C^{(g+1)}_{\lambda}$也是 $C^{(g)}$ 的unbias estimator。
  
  其中 $C^{(g+1)} _ {emp}$ 和 $C^{(g+1)} _ \lambda$之间一个显著的区别就是方差计算时使用的均值。 这里 $C^{(g+1)} _ {emp}$ 使用的是实际计算的，而 $C^{(g+1)} _ {\lambda}$ 使用的是真实的均值，所以我们可以说 $C^{(g+1)} _ {emp}$是在sampled points里面估计，而 $C^{(g+1)} _ {\lambda}$是在sample steps里面进行估计。
  
  公式11重新估计了原来的方差矩阵，一个更好的方式就是使用weighted selection（公式6)中的方式，进行加权:
  
  $$C^{(g+1)}_{\mu} =  comb\tag{12}$$
  
  $$comb = \sum^\mu_{i=1} {w_i((x^{(g+1)}_i-m^{(g)})*(x^{(g+1)}_i-m^{(g)})^T)}$$
  
  这里 $C^{(g+1)} _ {\mu}$更加倾向于产生一个被选择过的，比如成功的参数。
  
  在公式11基础上，我们将公式12和Estimation of Multivariate Normal Algorithm 进行对比，这里 $EMNA _ {global}$具体形式如下，也叫 Cross-Entropy method：
  
  $$C^{(g+1)}_{EMNA_{global}} =  \frac{1}{\mu} * comc\tag{13}$$
  
  $$comc = \sum^\mu_{i=1} {((x^{(g+1)}_i-m^{(g+1)})*(x^{(g+1)}_i-m^{(g+1)})^T)}$$
  
  $$m^{(g+1)} = \frac{1}{\mu} \sum^\mu_{i=1} {x^{(g+1)}_i}$$
  
  和公式12相比，最重要的区别是使用计算使用的均值。公式13使用的是selecselec
  
  
