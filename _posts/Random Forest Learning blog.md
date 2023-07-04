---
title: 'Random Forest Learning blog'
date: 2023-07-02
permalink: _posts/Random Forest Learning blog.md
tags:
  - ML
---
This is a sample.  

**基础概念理解**
======
随机森林是以决策树作为基本分类器的集成模型，结合了Bagging集成学习和随机子空间的两大随机思想，分别用于随机选取训练样本和随机选取分裂属性值。  
convergence theorem证明了随机森林不会出现过拟合问题：
泛化误差界generalization error bound,给出了随机森林预测的理论上界：  
袋外估计OOB（out of bag estimation）:提出了一种利用袋外数据估计泛化误差界的OOB误差估计方法，该方法效率较高，结果近似于需要大量计算的K折交叉验证。

参考相关链接
======
[The Random Subspace Method for Constructing Decision Forests](https://pdfs.semanticscholar.org/b41d/0fa5fdaadd47fc882d3db04277d03fb21832.pdf?_gl=1*5ouhn0*_ga*MTM3NDA3MzM3MS4xNjc5OTkyNjEx*_ga_H7P4ZT52H5*MTY4ODQ3NTUxOS44LjEuMTY4ODQ3NjMxMi42MC4wLjA.)  
[Random Forest](https://link.springer.com/content/pdf/10.1023/A:1010933404324.pdf)  
[集成学习—随机森林原理（理解与论文研读）](https://blog.csdn.net/weixin_44750583/article/details/99431770)  
[机器学习基础复习-随机森林(Random Forest)](https://zhuanlan.zhihu.com/p/406627649)  
[Random Forests for Complete Beginners](https://victorzhou.com/blog/intro-to-random-forests/)  
[机器学习相关博客文档](https://knowledge-record.readthedocs.io/zh_CN/latest/machine_learning/machine_learning.html#id18)  

