
### 代码学习

  https://pemami4911.github.io/blog/2016/08/21/ddpg-rl.html

  - 这里action 通过tanh(action)*bound 来得到指定范围内容动作取值
  - 同时critic中  w1*state+w2*action + bias 来实现，听说这种方式 the action input skips the first layer. This is a design decision that has experimentally worked well

  https://github.com/openai/baselines/blob/master/baselines/ddpg

  https://keras.io/examples/rl/ddpg_pendulum/

  https://www.cnblogs.com/zle1992/p/10247326.html

  https://blog.csdn.net/qq_37388085/article/details/126135541
  
  https://github.com/ccjy88/cartpole_ddpg
  
### 问题排查
  
  问题描述：
     
     背景描述：
      
        经过多版本的迭代后，ddpg的4个塔都是用了不同的代码构建。同时critic训练5遍才更新actor和target
    
    现象描述：
        
        一上来就是输出边界值，（action激活函数为sigmoid，一上来就是0或者1）
        调小学习率0.001 -> 0.00001,刚开始还有些数值，后面也有走向边界的风险
    
    
    尝试1： https://blog.csdn.net/qq_31239371/article/details/122010193
          
          将激活函数调整为relu，释放更多正值的潜力
          同时调整学习率0.001 -> 0.00001 查看叠加的效果

### ddpg遇到问题 

20230404 ddpg迭代记录
目前尝试了3版：
- reinforce版本
   - 各模型的weight和bias趋向于一致，也就是没有差异，也没有学习到知识
- reinforce + 线上weightbias初始化版本
   - 各模型的weight和bias基本一致，比原始的reinforce版本效果稍好些
   -  也存在所有用户都输出同一个值的问题，虽然weight和bias之间有些许差异
- ddpg版本
   - 参数震荡，同时奖励和loss也是震荡，输出动作 pmmbias 和pmmweight打到最小值上
   - 从网上的学习资料来看，可能是（1没有归一化，2网络层数太多），因此这里开发一版，尝试降低actor和critic层数的，记为change_v1
