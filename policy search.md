# policy search read note

truely random search:

    (r+ - r-) 估计梯度的解释（对称采样法） （PGPE) https://paperexplained.cn/articles/article/sdetail/a6607282-20d0-4bbc-a45f-a7c3f122c206/

population-based:
  Population-based BBO methods manage a limited population of individuals, and generate new individuals randomly in the vicinity of the previous elite individuals.
  
Evolutionary strategies:
  Evolutionary strategies (ES) can be seen as specific population-based optimization methods where only one individual is retained from one generation to the next. More specifically, an optimum guess is computed from the previous samples, then the next samples are obtained by adding Gaussian noise to the current optimum guess.
    
    博客：visual evolutionary strategies(翻译版） https://www.yanxishe.com/blogDetail/5791

Bayesian Optimization 的 acquisi-
tion function： 
    
    https://ekamperi.github.io/machine%20learning/2021/06/11/acquisition-functions.html

策略梯度的简介：
   
   https://lilianweng.github.io/posts/2018-04-08-policy-gradient/


( ppt) Natural Policy Gradients, TRPO, PPO
     
     https://www.andrew.cmu.edu/course/10-703/slides/Lecture_NaturalPolicyGradientsTRPOPPO.pdf
 
(ppt)  Augmented Random Search
    
     https://cse.buffalo.edu/~avereshc/rl_spring20/Augmented_Random_Search_Gautam_Suryawanshi_Prajit_Krisshna_Kumar.pdf

（blog) augmented Random Search
    
    https://towardsdatascience.com/introduction-to-augmented-random-search-d8d7b55309bd
   
(cem) Cross-Entropy for Monte-Carlo Tree Search

 (blog) natual policy gradients 
    
    https://jonathan-hui.medium.com/rl-natural-policy-gradient-actor-critic-using-kronecker-factored-trust-region-acktr-58f3798a4a93

（blog) ddpg
    
    https://towardsdatascience.com/deep-deterministic-policy-gradients-explained-2d94655a9b7b
