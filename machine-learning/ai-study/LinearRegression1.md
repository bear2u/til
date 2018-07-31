# 선형 함수를 텐서플로로 구현

먼저 그래프를 만든다.

아래 그림은 영상에 나오는 설명 스샷이다.

![](/assets/linearReg1.png)

코드로 짜보면

```py
import tensorflow as tf

x_train = [1, 2, 3]
y_train = [1, 2, 3]

w = tf.Variable(tf.random_normal([1]), name='weight')
b = tf.Variable(tf.random_normal([1]), name='bias')

hypothesis = x_train * w + b

cost = tf.reduce_mean(tf.square(hypothesis - y_train))

optimizer = tf.train.GradientDescentOptimizer(learning_rate=0.01)
train = optimizer.minimize(cost)

sess = tf.Session()
sess.run(tf.global_variables_initializer())

for step in range(2001):
    sess.run(train)
    if step % 20 == 0:
        print(step, sess.run(cost), sess.run(w), sess.run(b))
```

`training`을 시켜서 학습된 데이터를 가지고 step이 20번째 마다 최소값등이 나오는 걸 볼 수 잇다.

![](/assets/linearReg2.png)

만약 placeholder 로 적용시 원하는 값을 후에 지정을 할수가 있다.

![](/assets/linearReg3.png)

최종적으로 정리하자면

![](/assets/linearReg4.png)

