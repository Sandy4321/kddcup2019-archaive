# Weekly Progress

## Time Window: 20190415~20190421

- [x] 搭建docker评测环境
- [x] 熟悉baseline模型代码：数据预处理，特征工程，模型参数调优

## Time Window: 20190422~20190428

#### 模型

- [x] 进一步摸清baseline模型
- [x] 学习GBDT

#### 特征工程

- [x] 文献阅读
1. FeatureTools: https://www.kaggle.com/willkoehrsen/automated-feature-engineering-tutorial#Feature-Tools
  
2. Paper: Deep Feature Synthesis: Towards Automating Data Science Endeavors [http://www.jmaxkanter.com/static/papers/DSAA_DSM_2015.pdf](http://www.jmaxkanter.com/static/papers/DSAA_DSM_2015.pdf)
  3. 可能存在的问题：Feature engineering allows us to combine information across many tables into a single dataframe that we can then use for machine learning model training. Finally, the next step after creating all of these features is figuring out which ones are important. 特征太多，不知道哪一个更好。（PCA？非线性降维？）

4. ExploreKit: Automatic Feature Generation and Selection
https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=7837936&tag=1

#### 超参数调优

- [x] 文献阅读
  
  1. Algorithms for Hyper-Parameter Optimization: <http://papers.nips.cc/paper/4443-algorithms-for-hyper-parameter-optimization>
  
     Sequential model-based global optimization (SMBO):
  
     - Gaussian process (GP): $O(N^{3})$ ，慢

     - Tree-structured Paren estimator (TPE): baseline算法，$O(N)$
     
  2. Multiobjective Neural Network Ensembles Based on Regularized Negative Correlation Learning: <https://ieeexplore.ieee.org/abstract/document/5416712/>
  
     多目标优化方法构建ensamble
  
- [x]  方案探索

  1. 利用超参搜索过程中不同参数训练的模型构建ensamble，无需多余计算量，下期进行实验
  
  

## Time Window: 20190429~20190512

#### 模型

- [x] 学习xgboost，lightGBM

#### 特征工程

- [x] 文献阅读
- [x] 方案探索
  - [x] featuretools：思路类似baseline方法中特征生成方法
  
  - [x] baseline方法特征工程超参改了些超参，直接提交，rank：42
  
  - [x] factorization machine:  y(x)=w_0 + \sum_{i=1}^n w_ix_i + \sum_{i=1}^n\sum_{j=i+1}^n <v_i,v_j> x_i x_j
  
    即抓住任意两个特征之间的interaction产生新的特征，但是这个技术是2010年的了，比较老了。
  
    https://www.csie.ntu.edu.tw/~b97053/paper/Rendle2010FM.pdf

#### 超参数调优

- [x] 基于高斯过程修改baseline超参数调优模块，然后提交一版

- [x] 文献阅读

- [x] 方案探索
  利用超参搜索过程中不同参数训练的模型构建ensamble，无需多余计算量，进行实验:
  1. 根据AUC，选取最好的5组参数
  
  2. 根据NCL，选取最好的5组参数 (NSGA-II) 
  
     | Algorithms                  | A        | B        | C        | D        | E        |
     | --------------------------- | -------- | -------- | -------- | -------- | -------- |
     | baseline                    | 0.719553 | 0.825020 | 0.706694 | 0.621964 | 0.647131 |
     | ensemble_AUC (top5)         | 0.717570 | 0.826221 | 0.720492 | 0.626631 | 0.637492 |
     | ensemble_NCL (NSGA-II top5) | 0.718598 | 0.826619 | 0.720492 | 0.624667 | 0.637492 |
  
     
  

## Time Window: 20190513~20190520

特征工程修改思路：
   1. There must exist a relationship between HASH_MAX and WINDOW_SIZE:
      The larger the HASH_MAX, the less information from other records with identical hash value can be used.
      The larger the WINDOW_SIZE, the more temporal information can be used.
      因此会增加四个超参数。
      在merge.py中代码修改如下，已实现，并于20190516晚上提交一版, rank:
   2. 增加PCA降维：将经过各种join后的主表的所有特征输入PCA算法，输出信息占比85%以上的降维特征
   3. 混合PCA降维加上原始特征
   4. 对于原始单列特征增加更多aggregation操作

- [x] 超参数调优修改思路：
   1. 测试class imbalance相关参数
      a. 固定参数："is_unbalance": True
      	->ADE提升明显，C严重下滑，总排名下滑，暂时关闭，**待后续研究**
      b. 自动调参：![1558700308068](C:\Users\Austin\AppData\Roaming\Typora\typora-user-images\1558700308068.png)
       ->AE提升，CD下滑明显，已关闭

| Algorithms   | A        | B        | C            | D        | E        |
| ------------ | -------- | -------- | ------------ | -------- | -------- |
| ensemble_NCL | 0.718598 | 0.826619 | 0.720492     | 0.624667 | 0.637492 |
| 固定参数     | 0.719159 | 0.825711 | **0.610744** | 0.626752 | 0.647908 |
| 自动调参     | 0.718620 | 0.824533 | 0.703756     | 0.620960 | 0.647126 |


   2. 文献阅读 
         Efficient and Robust Automated Machine Learning
            http://papers.nips.cc/paper/5872-efficient-and-robust-automated-machine-learning.pdf
            a. 自动超参调优算法：random-forest based SMAC （Baysian optimization），下周进行测试对比TPE
            b. 集成学习选择方法：ensemble selection，下周进行测试对比NCL

   3. 搜集往届获奖方案
         a. https://github.com/flytxtds/AutoGBT (NIPS2018, 1st)
            b. https://github.com/MetaLearners/NIPS-2018-AutoML-Challenge (NIPS2018, 2nd)
            c. https://github.com/jungtaekkim/automl-challenge-2018 (PAKDD2018, 2nd)

## Time Window: 20190521~20190527

### 特征工程修改思路：
1. random sampling, or data subsampling: 不一定要用到给定数据集的所有数据，resample一些出来学习，提高效率；
    - data ubsampling **(已实现)**
    - data downsampling **(已实现，已提交)**
2. Categorical Feature: an integer describing which category the instance belongs to.
    - preprocessing methods: Hash coding and frequency coding **（frequency coding已实现，效果一般, rank: 37）**
    - advice: avoid OneHot for high cardinality columns and decision tree-based algorithms.
3. 关掉PCA，设计特殊的Feature selection: 需要设计特别的feature selection方法，感觉PCA既耗时，只是选出信息量大的特征，但是没有选出真正有用的特征。
    - select feature according to feature importance generated by lightGBM **(已实现，这个非常有用，rank:29)**
    - feature selection with null importance
        - [https://www.kaggle.com/ogrellier/feature-selection-with-null-importances](https://www.kaggle.com/ogrellier/feature-selection-with-null-importances)
        - 这个方法统计特性貌似很好，但是很费时，可以试一下
        - **(已实现，但是相当费时)**
    - bagging methods: 5-kfolds feature importance average / bagging with different lightGBM model
4. Numerical Feature: a real value.
    - preprocessing methods: standardization
    - For a random variable X, standardization means converting X to its standardized random variable
5. Multi-value Categorical Feature: a set of integers, split by the comma.
    - preprocessing methods: Hash coding and frequency coding
    - 统计multi-value每一行中出现value的个数，作为该行的值**（已实现）**
6. Time Feature: an integer describing time information.
    - preprocessing methods: Using second-order features. What is second-order feature ???
7. First-order feature engineer: frequency encoding of categorical features
8. High-order feature engineer:
    - predefine a set of binary transformations based on prior knowledge
    - apply each type of transformation on the original feature sets to generate new features in an expansion-reduction fashion
    - such as:
        - numerical-numerical: +,-,*,/
        - categorical-numerical: num_mean_groupby_cat
        - categorical-categorical: cat_cat_combine, cat_nunique_groupby_cat
        - categorical-temporal: time_difference_groupby_cat
    - key steps:
        1. pre-selection: select features used for feature generation based on prior knowledge
        2. feature generation: generate new feature with all feasible pairs of the pre-selected features
        3. post-selection: select generated features based on the performance and feature importance of a coarsely trained GBDT model
9. Check the imbalance of class: mitigate the imbalance of class
    - Up-sample Minority Class: Up-sampling is the process of randomly duplicating observations from the minority class in order to reinforce its signal.**（已实现）**
    - Down-sample Majority Class: Down-sampling involves randomly removing observations from the majority class to prevent its signal from dominating the learning algorithm.**（已实现）**
    - Change Your Performance Metric: For a general-purpose metric for classification, we recommend Area Under ROC Curve (AUROC).**（已实现）**
    - Penalize Algorithms (Cost-Sensitive Training): to use penalized learning algorithms that increase the cost of classification mistakes on the minority class. A popular algorithm for this technique is Penalized-SVM
    - Use Tree-Based Algorithms: using tree-based algorithms. Decision trees often perform well on imbalanced datasets because their hierarchical structure allows them to learn signals from both classes.
10. Feature embedding: might utilize DNN to embed the selected feature???

#### 出现的问题：

1. 发现训练集和测试集上auc的表现差很多 **泛化性能不好**

### 超参数调优修改思路：

1. 提速测试：保存超参数调优中训练的模型，最终预测时，直接读取ensemble中的模型，继续训练，num_boost_round由500降到300

   ->排名39，auc和time均得到优化，已保留

2. 防止过拟合测试：选取weak model组成ensemble时，将最小化weak model中树的个数作为考量目标之一。

   ->排名53，代码已注释，**怀疑ensemble选5 out of 10不合理，ensemble需优化**

| Algorithms                        | A        | B        | C        | D        | E        | Time    |
| --------------------------------- | -------- | -------- | -------- | -------- | -------- | ------- |
| ensemble_NCL                      | 0.718598 | 0.826619 | 0.720492 | 0.624667 | 0.637492 | 2626.67 |
| 提速                              | 0.715636 | 0.825862 | 0.734846 | 0.627270 | 0.647908 | 2550.55 |
| 提速+防过拟合(maximize num_trees) | 0.716522 | 0.822976 | 0.715017 | 0.623435 | 0.630734 | 2238.36 |
| 提速+防过拟合(minimize num_trees) | 0.715263 | 0.825259 | 0.734847 | 0.621791 | 0.633713 | 2140.54 |

## Time Window: 20190527~20190602
### 特征工程修改思路：
继续实现没有做完的特征工程组件
1. Feature embedding: might utilize DNN to embed the selected feature???
2. Numerical Feature: a real value.
    - preprocessing methods: standardization
    - For a random variable X, standardization means converting X to its standardized random variable
3. Multi-value Categorical Feature: a set of integers, split by the comma.
    - preprocessing methods: Hash coding and frequency coding
    - 统计multi-value每一行中出现value的个数，作为该行的值**(已实现)**
    - frequency encoding of multi-value categorical feature**(已实现)**
4. Time Feature: an integer describing time information.
    - preprocessing methods: Using second-order features. ** What is second-order feature **???
5. First-order feature engineer: frequency encoding of categorical features **(已实现)**
6. High-order feature engineer:
    - predefine a set of binary transformations based on prior knowledge
    - apply each type of transformation on the original feature sets to generate new features in an expansion-reduction fashion
    - such as:
        - numerical-numerical: +,-,*,/ **(已实现)**
        - categorical-numerical: num_mean_groupby_cat
        - categorical-categorical: cat_cat_combine, cat_nunique_groupby_cat
        - categorical-temporal: time_difference_groupby_cat
    - key steps:
        1. pre-selection: select features used for feature generation based on prior knowledge
        2. feature generation: generate new feature with all feasible pairs of the pre-selected features
        3. post-selection: select generated features based on the performance and feature importance of a coarsely trained GBDT model **(已实现)**
7. feature selection:
    - univariate selection: selectKBest class in sklearn
    - feature importance based on tree model**(已在lightgbm中实现)**
    - pearson correlation **(已实现)**
    - selectFromModel**(已实现)**
    - Recursive Feature Elimination (RFE) **(已实现)**
    - Cross-validation **(重点关注)**
8. 还是利用下featuretools这个工具 **(已实现)**
    - Convert object data types to category (done above; functions included from his notebook);
    - Experiment with joining all the dataframes together, instead of linking them all through the entity_set relationship function;
    - Create and save partitions to disk, and;
    - Create an entity_set on each partition.
9. 特征数与record数的关系（先验知识）: So how many records are necessary for the number of features? Well, according to CalTech it is roughly 10. So if we have 10 features total features in our model, we should have at least 100 records. Given our max_depth=2 gives us 4135 features to work with, we should have 41,350 for any sort of prediction to be drawn from this experiment. We only have 21,000 and given this is just drawn from randomly generated data, I won’t adhere too much to the recommended number of records.
10. 缺失值补充: 
    - sklearn SimpleImputer:
      - numerical: median
      - categorical: most_frequent

### 超参数调优修改思路：
1. 可能需要联合所有参数进行调优（先别管超不超时，通过搜索超参的方法看能不能提高K,L的精度）
2. 设计根据time remaining利用计算资源的算法

### 加速建议：
1. 利用cython来加速代码
2. multi-fidelity，很多地方都不能拿整个输入数据集去算，极容易超时

### 本周要解决的一些问题：
1. 梳理目前整个模型的结构：现在模型组件越来越复杂了，需要整理
2. 发现训练集和测试集上auc的表现差很多 **(泛化性能不好)**，计划用**kfold选择特征和超参**

### 讨论思路总结

#### 数据预处理段

![数据预处理第1段](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/automl_discussion/WechatIMG222.jpeg)

![数据预处理第2段](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/automl_discussion/WechatIMG223.jpeg)

![数据预处理第3段](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/automl_discussion/WechatIMG224.jpeg)

#### automl段

![automl第1段](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/automl_discussion/WechatIMG219.jpeg)

![automl第1段](https://xijun-album.oss-cn-hangzhou.aliyuncs.com/automl_discussion/WechatIMG218.jpeg)

## Time Window: 20190603~20190609

### 特征工程

1. 调通featuretools

2. category特征用神经网络做embedding试一下

   - Treating some Continuous Variables as Categorical

     We generally recommend treating month, year, day of week, and some other variables as categorical, even though they could be treated as continuous. Often for variables with a relatively small number of categories, this results in better performance. This is a modeling decision that the data scientist makes. Generally, we want to keep continuous variables represented by floating point numbers as continuous.

   - **Whenever is possible, it’s best to treat things as categorical variables rather than as continuous variable.**

   - **Rule of thumb is to keep categorical variables that don’t have very high cardinality. As in if a variable has unique levels for 90% of the observations then it wouldn’t be a very predictive variable and we may very well get rid of it.**

3. 时间数据rolling修改

4. 特征选择改用shap值

### 模型参数：

1. smac尝试
2. 交叉验证

### others：

1. cython加速
2. pipe的搜索

#### 