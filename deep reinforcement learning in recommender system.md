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

#11. 深度强化学习中稀疏奖励问题研究综述  https://www.jsjkx.com/CN/article/openArticlePDF.jsp?id=18935

#12. 强化学习在阿里的应用   http://techforum-img.cn-hangzhou.oss-pub.aliyun-inc.com/1517812754285/reinforcement_learning.pdf
    
#13. 微信「看一看」 推荐排序技术揭秘 https://www.jiqizhixin.com/articles/2020-07-21-16

#14. 强化学习在推荐系统的应用-转转的实例（ppo) https://blog.itpub.net/70027824/viewspace-2989872/
       原始链接 https://mp.weixin.qq.com/s/y114gD0kpPiSO21nHEz_jw
> 基于上面的考虑，我们选择使用强化学习来进行多业务混排。用户在推荐场景浏览可以建模成 ov Progress，Agent 是我们的推荐系统，Action 是我们推荐了什么内容，Reward 是用户的反馈信息，包括点击、负反馈、退出等，每次我们的推荐系统 Agent 采取某个 Action，给用户推荐了内容，用户给到我们相应的反馈，通过最优化总点击数来获得最佳效果。
 未来有不确定因素，所以要对未来的收益做衰减 
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
        s->w   通过状态学习k个w，每个w对应一个位置上的参数。
        动作产生： scoreti=wti*embi    通过这k个wti乘以item的embedding来计算最终的得分，然后每个位置取得分最高的item作为最终动作ati（前面位置取了就从后面位置的候选中删除）
    critic网络：
        输入st 和 ati......atk ，输出Q(st,at)    这里at是 k个item的统-表示
    
    整体执行流程如下：    
        
        根据用户的session滑动，遍历每一个session,
        根据前一个session得到的状态s作为s0，进行预测状态at，并计算对应的奖励
        更新状态，并把st，at，rt，st+1保存起来，保存为D
        
        从D中采样N个样本，更新acotr和critic的的参数
        
  #jd1-Deep Reinforcement Learning for Page-wise Recommendations
      
      知乎：https://zhuanlan.zhihu.com/p/59081462
        
        核心的贡献点：
          1.提出一个能够学习一页中item怎么分布的推荐算法
          2.提出一个基于ddpg的深度强化学习算法来学习
          3.文中最有的测试也是采用模拟来完成，并没有真实的线上ab测试
         
        状态构建：
          
          一页中的item看成是一个M=h*w 个item组成的2d内容。每一个item包含它的side info作为输出，即xi=(id,category,feedback),其中的feedback包含用户跳过，点击，购买等行为。
          
          把一页的item看成是一个2d的图片，使用卷积提取特征，pi=conv2d(Xi)
          
          p1,p2,....,pt又用gru来学习时间上的关系，得到h1,h2,.....ht
          
          使用attention结构来来从hi中学习到当前的真正状态s_current
          
       动作的构建：
          a_current = deconv2d(s_current) ,值得注意的是，这里的a_current和pi是不同的，前者只包含了item的embedding信息，而后者还有side info和用户反馈信息。
      
      
       critic的构建：
          
          a_current是一个2d的包含item embedding信息的表示，要转化成最终的动作，还需要一步，a=conv2d(a_current),这里的a就是输入到dqn中的动作了。
          
       
       训练流程：
        
          上面的a_current是一个2d的包含item embedding信息的表示，但是并不是每一个embedding都有对应的id，所以，还需要一个mapping的过程，找到最匹配的真实item id。
          
          在online training阶段，通过cosine 相似度将a_current 和 a_val 进行关联起来。然后可以把a_val推荐给用户，获取反馈 r=sum(reward(ei))
          
          在 offline training阶段，由于使用的是用户的历史记录，所以无论a_current 怎么变化，用户的有效动作都是固定的（因为用户只对那一批item有反馈）。
         此时，a_current和a_val之间就有了gap，为了弥补这个gap，这里通过学习 mse(a_current,a_val),来让a_current 趋向于与a_val相似，
         然后就可以使用a_val的信息来正常的学习更新网络了，这里的r=sum(reward(ei))来自a_val。（也就是在online trainning基础上，额外加上mse来消除a_current和a_val直接的gap)
         
         
        训练算法：
            
            在训练过程中，使用的还是ddpg算法。值得注意的是，critic是采用replay buffer来batch训练，这里就涉及到一个动作的问题，这里的动作都是来源于a_val,a=conv2d(a_val)。
            而actor训练中的动作来自于a_current,a=conv2d(a_current),因为a_current才是actor的真实输出。
          
        测试流程：
          
            online测试时，就是计算状态获取a_current，得到动作，计算a_val，计算奖励r=sum(reward(ei))，得到新状态。
            
            offline测试时，就是给定一个session，然后算法能将用户点击/购买的item排到前面。之所以使用排序来验证，是因为我们只有这个session的ground truth reward。
            整体上也是计算状态获取a_current，得到动作，计算a_val，计算奖励r=sum(reward(ei))，得到新状态。
            
          
          
    
    
