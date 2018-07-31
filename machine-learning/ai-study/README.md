# 텐서플로 첫걸음

설치는 [https://www.tensorflow.org/install/install\_windows](https://www.tensorflow.org/install/install_windows) 에서 참고 해서 하면 된다. 

CPU / GPU 에 맞혀서 설치를 해야 한다. 

그럼 일단 헬로우 월드 먼저 찍어보자. 

```py
import tensorflow as tf

hello = tf.constant("Hello, Tensorflow!")

sess = tf.Session()

print(sess.run(hello))

>> b'Hello, Tensorflow!' 
```

b' 은 바이트 문자열이란 뜻이다. 그럼 일단 성공은 했다. 

만약 3 + 4 = 7이라는 더하기 연산을 하고 싶을 경우 어떻게 해야 할까? 

```py
node1 = tf.constant(3.0, tf.float32)
node2 = tf.constant(4.0)
node3 = tf.add(node1, node2)

print("node1:", node1, "node2", node2)
print("node3:", node3)

=======================
node1: Tensor("Const_1:0", shape=(), dtype=float32) node2 Tensor("Const_2:0", shape=(), dtype=float32)
node3: Tensor("Add:0", shape=(), dtype=float32)
```

흠...생각했던 것과는 다르게 나온다. 그래프 형태로 표시되는 것이기 때문이다. 

이걸 세션으로 만들어서 다시 출력해보자. 

```py
sess = tf.Session()
print("sess.run(node1, node2): " , sess.run([node1, node2]))
print("sess.run(node3):", sess.run(node3))

=============================
sess.run(node1, node2):  [3.0, 4.0]
sess.run(node3): 7.0
```

기대 했던 연산이 되서 나오는 걸 볼수 있다. 

순서가 중요한데 

1. 그래프로 빌드 하고
2. 그 그래프를 연산한다. 

이걸 좀 줄여서 `placeholder `함수를 써서 가능하다고 한다. 

```py
a = tf.placeholder(tf.float32)
b = tf.placeholder(tf.float32)
adder_node = a + b

print(sess.run(adder_node, feed_dict={a: 3, b: 4.5}))
print(sess.run(adder_node, feed_dict={a: [1,3], b: [2,4]}))

==========================
7.5
[3. 7.]
```



