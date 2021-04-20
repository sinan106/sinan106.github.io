---
title: Advanced_EAST
date: 2021-01-20 22:31:50
tags:
- 文本检测
categories:
- 深度学习
---

# 1. 环境

```markdown
keras=2.4.3
numpy=1.19.5
opencv-python=4.5.1.48
pillow=8.1.0
pip=20.3.3
python=3.7.9
tensorflow=2.4.0
tqdm=4.56.0

win10系统
```

# 2. 代码修改

> 针对原作者[huoyijie代码](https://github.com/huoyijie/AdvancedEAST)以适配上述环境所作出的修改，仅能跑通，还是有警告

### 1. predict.py

```python
from keras.applications.vgg16 import preprocess_input  ->
from keras.applications.imagenet_utils import preprocess_input
```

### 2. preprocess.py

```python
with open(os.path.join(origin_txt_dir, o_img_fname[:-4] + '.txt'), 'r') as f: ->
with open(os.path.join(origin_txt_dir, o_img_fname[:-4] + '.txt'), 'r', encoding='UTF-8') as f:
```

### 3. losses.py

```python
tf.log -> 
tf.math.log
```

### 4. cfg.py

```python
data_dir = '/icpr/' ->
data_dir = os.path.join(os.getcwd() , 'icpr')
```

# 3. 不足

todolist -空格[空格]空格 即可

- [ ] 长语句无法框完整

![act](http://cdn.zhouxug.cn/20210120230936.png)

![predict](http://cdn.zhouxug.cn/20210120230953.png)

- [x] 头尾部缺失一边，导致整个语句未被检测

![image-20210121144246895](http://cdn.zhouxug.cn/20210121144246.png)

![image-20210121144320976](http://cdn.zhouxug.cn/20210121144321.png)

- [x] 头尾部同时缺失，导致整个语句未被检测

![image-20210121150024993](http://cdn.zhouxug.cn/20210121150025.png)

- [ ] 图片白边剪裁
- [ ] 图片缩放清晰度
- [ ] 代码重写与优化

# 4. 头尾部缺失一边解决方案

> 主要思路：通过已知的头或尾来直接填充另一头的两个y值，然后遍历时算出这个group中x的最小或最大值填充两个x值
































