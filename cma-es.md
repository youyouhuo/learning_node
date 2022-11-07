# cma-es
  
  &emsp;&emsp;The CMA Evolution Strategy: A Tutorial
  
## 0 前言
  
  &emsp;&emsp;cma-es作为一个比较成功的算法，已经被众多的使用者所称赞。得益于其中超参数的简单配置，以及其自动调整的能力，使用起来易上手，收到许多科研工作者的推崇。下面结合论文进行简单的梳理。

## 1 算法细节
  
  ### 1.1 整体流程之sample-select and Recombination 
  
  在使用cem的过程中，首先是sample，然后是select和recombination。sample阶段：
  $${x}^{(g+1)}_{k}\sim {m}^{(g)}+{\sigma }^{(g)}\mathcal{N}(0,{C}^{(g)})$$
