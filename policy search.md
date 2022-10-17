# policy search read note

truely random search:

    (r+ - r-) 估计梯度的解释（对称采样法） （PGPE) https://paperexplained.cn/articles/article/sdetail/a6607282-20d0-4bbc-a45f-a7c3f122c206/

population-based:
  Population-based BBO methods manage a limited population of individuals, and generate new individuals randomly in the vicinity of the previous elite individuals.
  
Evolutionary strategies:
  Evolutionary strategies (ES) can be seen as specific population-based optimization methods where only one individual is retained from one generation to the next. More specifically, an optimum guess is computed from the previous samples, then the next samples are obtained by adding Gaussian noise to the current optimum guess.
    
    博客：visual evolutionary strategies(翻译版） https://www.yanxishe.com/blogDetail/5791

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

（ppt)ddpg
 
    http://www.cs.sjsu.edu/faculty/pollett/masters/Semesters/Spring18/ujjawal/DDPG-Algorithm.pdf
    
 （blog) Bayesian Optimization
 
    https://machinelearningmastery.com/what-is-bayesian-optimization/
  
  (paper) BOA The Bayesian Optimization Algorithm
  
    https://dl.acm.org/doi/pdf/10.5555/2933923.2933973
  
  Bayesian Optimization 的 acquisition function： 
    
    https://ekamperi.github.io/machine%20learning/2021/06/11/acquisition-functions.html
 
 (blog) bayesian optimization hyperparameter
 
 https://towardsdatascience.com/a-conceptual-explanation-of-bayesian-model-based-hyperparameter-optimization-for-machine-learning-b8172278050f
 
 (paper) A tutorial on
bayesian optimization of expensive cost functions, with
application to active user modeling and hierarchical reinforcement
learning.

    https://www.cs.ubc.ca/~nando/papers/bayopt.pdf
    
  （blog） open-es 梯度推导
  
  https://stats.stackexchange.com/questions/348111/how-is-the-equation-in-evolution-strategies-as-a-scalable-alternative-to-reinfo/348221#348221
  
  (blog) es 
  
  https://lilianweng.github.io/posts/2019-09-05-evolution-strategies/
  
  (blog) epsion-greedy vs softmax  q-leanring vs sarsa
  
  https://ai.stackexchange.com/questions/17603/what-is-the-difference-between-the-epsilon-greedy-and-softmax-policies
    
    The 𝜖-greedy policy is a policy that chooses the best action 
    (i.e. the action associated with the highest value) with probability 1−𝜖∈[0,1] and a random action with probability 𝜖. 
    The problem with 𝜖-greedy is that, when it chooses the random actions (i.e. with probability 𝜖), 
    it chooses them uniformly (i.e. it considers all actions equally good), 
    even though certain actions (even excluding the currently best one) are better than others. 
    Of course, this approach is not ideal in the case certain actions are extremely worse than others. 
    Therefore, a natural solution to this problem is to select the random actions with probabilities proportional to their current values. 
    These policies are called softmax policies.
  
  （blog) mdp
  
  https://juejin.cn/post/6859330833879154696
    
    强化学习任务通常使用马尔可夫决策过程（Markov Decision Process，简称MDP）来描述，具体而言：
        机器处在一个环境中，每个状态为机器对当前环境的感知；
        机器只能通过动作来影响环境，当机器执行一个动作后，会使得环境按某种概率转移到另一个状态；
        同时，环境会根据潜在的奖赏函数反馈给机器一个奖赏。
        综合而言，强化学习主要包含四个要素：状态、动作、转移概率以及奖赏函数。
        
 
 (blog) actor-critic 
    
 https://towardsdatascience.com/understanding-actor-critic-methods-931b97b6df3f
    
 (blog) multi-armed-bandit
    
 https://lilianweng.github.io/posts/2018-01-23-multi-armed-bandit/
 
 (paper) cem
 
 CROSS-ENTROPY FOR MONTE-CARLO TREE SEARCH
 Learning Tetris Using the Noisy Cross-Entropy Method
 A Tutorial on the Cross-Entropy Method Pieter-Tjerk de Boer
    
