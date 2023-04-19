
### 代码学习
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
