### 关于evaluator
- wuauc 用户加权auc【如果 groupby不为空，则计算分组的auc，然后如果 weights_for_groups 不为空，则计算加权auc，否则计算多组的平均auc】
- woauc 多级加权auc

- cumulative_deviation 
- - calculate_cumulative_deviation
  - use_rerank
  - n_quantiles
  - 含义：计算目标列与总体分位数列在分位数上的累计偏差
  - 简述：打分列进行排序，目标列进行排序，分不同的分位桶，计算桶内的 打分列 和 目标列 之间的均值相对偏差（不管正负),然后所有分桶内的偏差相加得到最终值
- calculate_mean
- - 计算总分列的均值 和 target列的均值 的差的绝对值  abs(mean_over_score-mean_target_score)
   
- top_coverage
- - calculate_top_coverage
- - ∑(前N%条目的目标列值) / 总值  使用 数值型列（收入、点击量等） 头部资源贡献效率 计算topn总和占总量的比例，类似 准确率

- pearson 计算 Pearson correlation coefficient 【使用np.corrcoef 来计算】

- portfolio
- - 函数的作用是通过排序overall_score，计算目标列的累积比率，找到达到预期收益的阈值，并确定高于该阈值的数据比例。返回值是阈值和集中度，需要解释这两个值的意义。阈值是满足预期收益的最小overall_score，集中度是高于阈值的数据占比，集中度越低说明越集中，【使用，如找出贡献90%点击量的广告占比，定位贡献80%收入的用户群体，识别产生95%销售额的商品比例】

- distinct_count_portfolio
- - calculate_distinct_count_portfolio_concentration
  - 计算target有多少个取值，然后采用over_score进行降序排列，看over_score排到什么取值时，记为threshold，能覆盖 target的expected_coverage【如0.95】。然后再计算一下over_score中打分大于threshold的个数占总个数的比例。
  - 与普通版的差异在于考虑了唯一性【使用，如分析前20%的视频覆盖了多少独特观众，确定贡献80%独特买家的商品比例，识别覆盖90%目标设备的广告条目】
- distinct_top_coverage ∑(前N%条目中的唯一值数) / 总唯一值数 适用 ID型列（用户ID、商品ID等） 头部资源覆盖广度 top_coverage关注的是数值的累积贡献，比如前5%的高分项贡献了多少总收入；而distinct_top_coverage关注的是覆盖了多少不同的实体，比如前5%的项覆盖了多少不同的用户ID。
- logmse  计算 mean{ [log(true+1)-log(pred+1)]^2}的值 也就是计算mse
- neg_rank_ratio
- - calculate_neg_rank_ratio
- - 标签列的值必须为 0（正样本）或 1（负样本） 核心公式： ratio = (2 * ∑(负样本排名)) / [(总样本数*2 - 负样本数 + 1) * 负样本数]   输出范围：(0, 1) 其中： 接近 1 → 负样本排名靠后（理想情况）接近 0 → 负样本排名靠前（需优化）【分子是负样本的排名之和，分母是负样本排在最后时的排名之和，最后的得分如果越匹配，则越好】
  - 可以简单改写下 ratio=（∑(负样本排名))/ [(total*(total+1))/2 - (pos*(pos+1))/2]
- portfolio
- - calculate_portfolio_concentration
  - 先根据overall_score降序，然后计算target_column的总和total_sum，然后计算target_column的cumsum ，也就是累计和，将累计和转化为比例（cumsum/total_sum),
  - 然后计算在cumulative_ratio>expected_return 【默认0.95】范围内，overall_score的最大值max_overall_score
  - 最后计算 overrall_score大于max_overall_score的个数/总个数，并返回
  - 通俗解释，就是计算样本的集中度，要包含label的expected_return，需要overall_score取什么值，并且overall_score中有多少是大于这个值的

- proportion
- calculate_proportion
- - 计算 overall_score==target_value 的均值，比如100个item有10个相同，那均值就是0.1


- std
  - calculate_standard_deviation
  - 计算方差




- inverse_pairs  量化排序结果中「高质量条目排在低质量条目之后」的错误情况,逆序对权重和 = ∑(每个特征的逆序对数 × 对应权重) ,结果范围 [0, +∞)，值越小越好
- tau
- - calculate_tau
  - 计算的是Kendall Tau相关系数，用于衡量两个变量之间的序数相关性。函数中的注释也提到，这个系数范围在-1到1之间，-1表示完全负相关，1表示完全正相关，0表示无相关性。

### 关于optimization
- base.py  _prepare_study(): 解析参数配置，同时初始化optuna的create_study()
- base.py  optimize(): 调用study的optimize(self.objective,n_trails)
- construct_weights.py construct_weights() 
- - equation_type="sum" 调用  construct_first_order_weights
  - equation_type="free_style" 或者 = "json" 调用 construct_free_style_weights()
  - equation_type = "log_pca" 调用 construct_log_pca_weights（）
  - equation_type = "product"  且 ob.first_order=True 调用 construct_power_weights（） + construct_first_order_weights
  - 如果  ob.first_order=False 调用 construct_power_weights(ob, trial)
  - 最后返回weights
  - - construct_weights.py construct_power_weights(ob: "MultipleObjective", trial: optuna.Trial) 根据ob.dirichlet 是否为true返回指定的weight【这里更多的是对weight是否进行变换，如果b.dirichlet=false，则不修改weight
  - - construct_weights.py construct_first_order_weights( ob: "MultipleObjective", trial: optuna.Trial) 根据 first_order_scale 的 upper_bound和 lower_bound 对权重进行约束约束到【np.power(10,weight[i] - lower_bound),np.power(10,weight[i] - upper_bound)】，如果max_min_scale_ratio不为空，还会进一步进行调整
  - - construct_weights.py construct_free_style_weights(ob: "MultipleObjective", trial: optuna.Trial) 根据 free_style 的upper_bound和lower_bound来对weight进行约束，需要一一对应
  - - construct_weights.py  construct_log_pca_weights（） 使用 pca_importance的 upper_bound和lower_bound来对weight进行约束，需要一一对应

- evaluate_targets.py
- -  pearson 调用 calculator.calculate_corrcoef()
  -  portfolio 调用 calculator.calculate_portfolio_concentration()
  -  distinct_count_portfolio  调用 calculator.calculate_distinct_count_portfolio_concentration（）
  -  top_coverage 调用calculator.calculate_top_coverage（）
  -  distinct_top_coverage 调用 calculator.calculate_distinct_top_coverage（）
  -  wuauc 调用calculator.calculate_wuauc(）
  -  auc 调用calculator.calculate_wuauc(）
  -  woauc 调用calculator.calculate_woauc()
  -  logmse 调用calculator.calculate_log_mse()
  -  neg_rank_ratio 调用 calculator.calculate_neg_rank_ratio()
  -  inverse_pairs  调用 calculator.calculate_inverse_pair()
  -  tau 调用 calculator.calculate_tau()

- multiple_objective.py
- - __init__()
  - - 如果 power_lower_bound < 0   self.dirichlet = False
  - - 如果 self.calculator.equation_type not in ["free_style", "json"]  调用  self.calculator.value_scale()
  - - 最后调用self._prepare_study()，这里会调用,这个direction就是最后的target是最大还是最小【maximize，minimize】
      - self.study = optuna.create_study(direction=self.direction,study_name=self.study_name,storage=storage,load_if_exists=True,)

- - add_evaluator() 主要是解析使用的evaluate() flag 用来判别使用时那种计算方式，如auc wauc等等

- - evaluate_custom_weights（）
  - - 调用calculator.get_overall_score(weight)计算每一个公式的打分
    - 调用evaluate_targets(）计算targets

- - objective()
  - 调用 construct_weights（）
  - 调用 evaluate_custom_weights（）
  - 计算最终多个target之间的聚合方式 如 formula = "2*targets[0]+targets[1]" 这里targets[0]，target[1] 可以是 auc ，wauc tau等等,返回最后的result，并打印每一行的效果

- optimize_parallel.py
  - parallel_optimize() 调用 multiple_objective.optimize(ntrials)
  - optimize_run（） 如果 parallel=False，则不使用并行，直接调用obj.optimize()，否则使用并行 调用parallel_opimimze,最后进行保存save_study()

- save_study.py
- - save_multiple_objective_info()
  - ob.study.trials_dataframe().to_csv()
  - get_best_trials(ob)
 

### 关于整个代码库的总体认识
- 主要是借助optuna这个库的study来学习
- 本库的核心贡献在于：
- - 搭建对应的数据输入输出方式
  - 通过在multiple_objective.objective()中构建权重的生成方式，以及对应的target聚合方式，让optuna可以有一个输入和目标能进行参数搜索
  - 同时开发并行的方式，借助计算机的多处理器进行并行处理
  - 当然，本库还有自定义的pcacaculator，这个也是没有用过，应该是来自于之前的一个项目叫mixician
