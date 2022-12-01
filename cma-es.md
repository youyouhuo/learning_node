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
  
  和公式12相比，最重要的区别是使用计算使用的均值。公式13估计selected polulation中的的方差，而公式12是在selected step中，因此公式13比公式12有更小的方差，而且相比于 $C^{(g)}$，也能降低方差。
  
  
  #### 1.1.3 rank $\mu$ update 
  
  为了达到更快的搜索（与更鲁棒，更全局搜索相反）有时候我们的population会设置得很小，因为通常（理想情况下）, ${\mu}_{eff} \approx \frac{\lambda} {4}$ 而且  $\mu _ {eff}$ 尽可能的小，我们可以假设，比如 $\mu _ {eff} <= 1+ln(n)$,此时我们使用公式12可能不能得到一个比较可靠的结果。
  作为补偿，我们可以使用之前迭代产生的信息。比如说，经过几轮的迭代后，我们计算一个所有轮数的方差： 
  
  $$ C^{(g+1)} = \frac {1} {g+1} \sum^{g} _ {i=1} ( \frac{1} {{ \sigma ^i } ^2 } C^ {(i+1)} _ \mu) \tag{14}$$
 
 这里，为了让不同轮之间可比，加入了 $\sigma ^i$。
 
 公式14中，所有轮的权重都是一样的，为了突出最近轮数的权重更高，我们可以加入指数衰减。

$$ {c}^{(g+1)}=(1-{c}_{\mu }){C}^{(g)}+{c}_{\mu } \sum^{\mu} _ {i=1} w_i { y^ {(g+1)} _ {i:\lambda} } { y^{(g+1)} _ {i : \lambda} } ^T\tag{15} $$

 这里  $C_\mu \approx min(1,\mu_{eff}/n^2)$ 是一个比较好的选择，而且  $wi$的和为1，而且 $y^ {(g+1)} _ {i:\lambda} = (x^ {(g+1)} _ {i:\lambda}-m^{(g)})/\sigma^{(g)} $。
 
 公式15也叫rank- $\mu$ -update ，因为式子中外积的求和是秩为 100%概率为 $min(\mu,n)$。
 公式15可以进一步的扩展，让求和扩展到 $\lambda$ 而且权重的和不必求和为1，也不必非负。
 
 $${c}^{(g+1)}=(1-{c}_{\mu }\sum^{\lambda} _ {i=1} w_i){C}^{(g)}+{c}_{\mu } \sum^{\lambda} _ {i=1} w_i { y^ {(g+1)} _ {i:\lambda} } { y^{(g+1)} _ {i : \lambda} } ^T\tag{16}$$
 
 $${c}^{(g+1)}={C^{(g)}}^{1/2}(I+{c}_{\mu } \sum^{\lambda} _ {i=1} w_i ({ y^ {(g+1)} _ {i:\lambda} } { y^{(g+1)} _ {i : \lambda} } ^T-I)){C^{(g)}}^{1/2}$$
 
