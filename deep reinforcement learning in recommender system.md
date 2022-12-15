### 强化学习在推荐的应用

#0. Reinforcement Learning based Recommender Systems: A Survey （2022 june acm)

#1. A Survey of Deep Reinforcement Learning in Recommender Systems: A Systematic Review and Future Directions（arXiv-2021.09）
  
 (blog)https://cloud.tencent.com/developer/article/1881235   （上面论文对应的博客）
 
 
#2. 强化学习在美团"猜你喜欢"的应用 https://tech.meituan.com/2018/11/15/reinforcement-learning-in-mt-recommend-system.html

#3. 强化学习在京东的应用 https://www.6aiq.com/article/1547826520120

#4. youtube value-base的强化学习推荐系统 https://blog.csdn.net/weixin_44289754/article/details/119122740

#5. 基于强化学习的推荐研究综述 （计算机科学）https://www.jsjkx.com/CN/article/openArticlePDF.jsp?id=20212

#6. 强化学习在 美团 京东 微软的实践（知乎） https://zhuanlan.zhihu.com/p/355041851

#7. (微软drn paper）DRN: A Deep Reinforcement Learning Framework for News Recommendation

#8. 猜您所想：淘宝搜索/推荐系统背后深度强化学习与自适应在线学习的实践之路 https://toutiao.io/posts/9j6ze2/preview （原始链接 https://mp.weixin.qq.com/s/gKlyvv8hzlHRAOLRlVV3dw?）

#9. 结合用户长短期兴趣的深度强化学习推荐方法（中文信息学报）  http://jcip.cipsc.org.cn/CN/Y2021/V35/I8/107

#10. Flink + 强化学习搭建实时推荐系统（博客园---精读）  https://www.cnblogs.com/massquantity/p/13842139.html

#Top-𝐾 Off-Policy Correction for a REINFORCE Recommender System
  整体上，使用了Chaos Free RNN (CFN) 建模状态之间的转移
  使用 梯度不回传的beta网络处理离线训练中的data bias，
  为了降低状态空间，先使用近邻搜索K个，然后预估这k个action的值。
  为了降低方差，使用weight clipping
  （blog)http://wd1900.github.io/2019/06/23/Top-K-Off-Policy-Correction-for-a-REINFORCE-Recommender-System-on-Youtube/
  
 #slateQ （blog) https://blog.csdn.net/zackerzhuang/article/details/100978955   与 https://blog.csdn.net/qq_16234613/article/details/105339645
    整体上在排序层使用sarsa算法来进行学习。
    2个基本假设：
              
                single choice：用户一次只在推荐列表中点击一个推荐项或不点击任何推荐项
               
               RTDS(Reward/transition dependence on selection) 用户根据选择推荐项而会产生不同的回报和状态转移，没选择时，就是没有转移。
    
 #jd-Deep Reinforcement Learning for List-wise Recommendations
 
 整篇文章核心是建立一个离线模拟器来模拟线上的推荐过程，没有进行真实的ab测试
 
 整体上采用ddpg来搭建：
    
    模拟时的奖励：
      由于是离线的模拟，所以不能直接收集到用户的反馈，这里做了一个假设：有相似兴趣的用户对同一个item会做出相似的决策。因此基于此，可以通过历史的<st,at> 来对模拟时产生的状态动作生成奖励。
      使用一个memory保存 (s,a)->r的映射
      对于算法产生的(s,a)，通过和memory里面的保持的映射计算相似度，然后近似产生映射。（这里的s是N个正反馈的item构成的，每次有新的item，就删除最旧的那个item）
    
    动作网络： 
        每次推荐k个item
        s->wt   通过状态学习k个w
        动作产生： scoret=wt*embi    通过这k个w乘以item的embedding来计算最终的得分，然后每个位置取得分最高的item作为最终动作ai（前面位置取了就从后面位置的候选中删除）
    critic网络：
        输入st 和 ai......ak ，输出Q(st,at)    这里at是 k个item的统-表示
    
    整体执行流程如下：    
        
        根据用户的session滑动，遍历每一个session,
        根据前一个session得到的状态s作为s0，进行预测状态at，并计算对应的奖励
        更新状态，并把st，at，rt，st+1保存起来，保存为D
        
        从D中采样N个样本，更新acotr和critic的的参数
    
