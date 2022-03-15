##### Introduction: Machine Learning Fundamentals

AI vs ML vs NN

AI：替代人解决问题

ML：根据数据找出规则，而不是直接给出规则

NN：层状数据的ML，将层状数据连接

DNN：多层状数据的ML，将层状数据连接

##### Introduction to TensorFlow

Tensor: 张量

```python
#TensorFlow version 2.x
import tensorflow as tf
print(tf.version)

t = tf.zeros([5,5,5,5])

t = tf.reshape(t, [125, -1])
```

