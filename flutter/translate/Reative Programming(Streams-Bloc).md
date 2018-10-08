# Reactive Programming part 1 - Stream

## 스트림이란 무엇인가?

스트림을 이해하기 위해선 파이프를 상상해야 한다.

![](/assets/flutter_stream1_.png)

뭔가를 입력을 했다면 그것이 파이프 안에서 흘러서 다른 출구쪽으로 배출 되는 걸 뜻한다. 

그럼 Flutter 을 대입해보자면

1. 파이프는 Stream 이라고 부른다. 
2. Stream을 제어하기위해 우리는 자주 StreamController 를 이용한다. 
3. Stream 입력하기 하기 위해 StreamSink 를 사용한다. 

입력 할수 있는 것들은 어떤 것이든 가능하다. 

> 데이터, 오브젝트, map, 에러코드, 이벤트, 심지어 다른 스트림도 가능하다.

### 배출은 어떻게 확인하나?

일단 입력이 되면 Rx 처럼 subscribe 를 통해서 받을 수 있다. 이 과정을 flutter에서는 listen 한다고 말한다. 

**즉 Stream을 listen 하면 StreamSubscription 오브젝트를 받을수 있는 것이다. **

StreamSubscription이 할수 있는 것들은

* Stop listening
* pause
* resume

## 그럼 스트림은 단순히 파이프 역할만 하는 건가?

스트림은 또한 흐르는 데이터들을 **process 를 허용하고 있다. **

즉 **StreamTransformer **를 통해서 데이터를 조작하고 그것을 다시 배출 하는 과정을 거친다. 

**StreamTransformer 은 프로세싱하는 데 어떠한 타입 형태로도 사용 가능하다. **

예를 들어 Filtering, regrouping, modification 등등

## Streams 의 종류

### Single-subscription Streams

* 하나의 리스너를 통해서만 값을 배출 받을수 있다. 그 후 리스너들은 취소될 것이다. 

### Broadcast Streams

* 다수의 리스너에 동시에 배출을 할 수 있다. 







