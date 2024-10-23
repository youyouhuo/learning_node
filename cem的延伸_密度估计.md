### 1.cem的公开实现 
> [石塔西版的实现](https://github.com/stasi009/PracticalGuidetoRecSys/blob/main/snippets/cem.py)
### 2.cem潜在的问题
> 累加方式估计均值和方差，前提就是假设服从一个高斯分布
> 由此衍生，如果假设是服从多个高斯分布呢？ 这就是概率密度中的问题，比如 [高斯混合模型](https://scikit-learn.org/stable/modules/generated/sklearn.mixture.BayesianGaussianMixture.html#sklearn.mixture.BayesianGaussianMixture)
> 当然，高斯混合模型也有缺点，就是不能将label 或者是先验知识耦合进来【比如说，想把cem的效果好的和效果稍微好的和效果差的作为一个先验知识】
