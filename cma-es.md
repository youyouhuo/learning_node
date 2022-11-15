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
