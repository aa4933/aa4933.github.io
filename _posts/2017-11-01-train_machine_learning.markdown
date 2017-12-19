---
layout:     post
title:      "机器学习错题集+经验教训"
subtitle:   "Mysql"
date:       2017-10-22 10:52:00
author:     "wuilly"
header-img: "http://ww4.sinaimg.cn/large/006tKfTcgy1fj6elgidgej30hm0b9jsd.jpg"
tags:
    - MachineLeaning
---
> 'About error'

## 关于在使用以及训练过程中产生的问题以及解决方案


[stackoverflow1](https://stackoverflow.com/questions/29263099/dataconversionwarning-fitting-randomforestregressor-in-scikit)
[stackoverflow2](https://stackoverflow.com/questions/34337093/why-am-i-getting-a-data-conversion-warning)

``` php

 DataConversionWarning: A column-vector y was passed when a 1d array was expected

解决方案
y.values.ravel()
原因
在sk-learn模型训练的时候，y.shape格式需要是(x,)而不是(x,1)

```

[stackoverflow](https://stackoverflow.com/questions/30121181/how-to-convert-string-into-float-value-in-the-dataframe)

``` php
ValueError: could not convert string to float: Normal

解决方案
df['col'] = df['col'].astype(float)
原因
使用算法过程中，需要对字符串格式转换为可量化数据，一般这个原因是由其他更深层次的原因引起的，最好检查一下数据
```

[CSDN](http://blog.csdn.net/fjqcyq2/article/details/46627319)

``` php
Input contains NaN, infinity or a value too large for dtype('float32').

解决方案
from sklearn.preprocessing import Imputer
df = Imputer().fit_transform(df)
原因
值出现缺失的时候，不能参加训练，采用填充的方法填充，最好有更好的方案填充，比如平均值之类的（这种默认方法很影响正确率）

```

[staroverflow](https://stackoverflow.com/questions/28036812/indexerror-too-many-indices-for-array)

``` php
cross_validation.cross_val_score IndexError: too many indices for array
解决方案
c, r = labels.shape
labels = labels.reshape(c,)
原因
交叉验证需要(R,)格式
```

### 比较好的入门教程

[房价预测1](http://blog.csdn.net/ns2250225/article/details/72626155)

[房价预测2](http://blog.csdn.net/u010016927/article/details/75452594)

[房价预测3](http://www.jianshu.com/p/62716b33e7be)

[泰坦尼克生存预计](http://blog.csdn.net/SilenceGTX)

[决策树分析1](http://www.cnblogs.com/leoo2sk/archive/2010/09/19/decision-tree.html)

[决策树分析2](http://blog.csdn.net/yujianmin1990/article/details/49864813)

### 机器学习入门方向指引

[学习路线1](http://blog.sina.com.cn/s/blog_d178ee980102w24k.html)

[学习路线2](http://blog.csdn.net/leayc/article/details/74178382)

### scikit-learn

[官方文档](http://scikit-learn.org/stable/user_guide.html)

### 关于python小测试

- 编写一个简单的脚本，对字符串句子中出现的单词进行数量从大到小的排列

``` python
"""Count words."""
import collections

### 需要编写的方法
def count_words(s, n):
    """Return the n most frequently occuring words in s."""
    
    # TODO: Count the number of occurences of each word in s
    
    m = s.split()
    number = collections.Counter(m)
    # TODO: Sort the occurences in descending order (alphabetically in case of ties)
    # TODO: Return the top n most frequent words.
    i = 0
    top_n = []
    for k,v in number.items():
        top_n.append((k,v))
        i = i+1
        if(i>=n):
            break
    top_n=sorted(top_n, key=lambda x: x[1],reverse=True)
    
    return top_n

###

def test_run():
    """Test count_words() with some inputs."""
    print count_words("cat bat mat cat bat cat", 3)
    print count_words("betty bought a bit of butter but the butter was bitter", 3)


if __name__ == '__main__':
    test_run()
```