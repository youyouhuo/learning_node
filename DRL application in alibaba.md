### 实时搜索策略调控
    状态s：点击的商品 + 用户长期行为特征
    奖励：最初版奖励：   pv下，1.如果点击，则为点击数量；2.如果购买，则为购买的价格；3.否则为0 
         升级版奖励： R = R(st,a,st+1) - 是否点击的交叉熵（这里是最后的得分） - 是否购买的交叉熵（这里是最后的得分）   （核心逻辑是，希望强化奖励，学习是否点击和是否购买）
    动作：输出 每个item的权重向量，最后是 排序的score = item_feature * action_theta


 升级版 ddpg，是 除了基础的ddpg学习，另外加上了 是否点击的交叉熵（这里是最后的得分） 和 是否购买的交叉熵（这里是最后的得分）的ltr loss。