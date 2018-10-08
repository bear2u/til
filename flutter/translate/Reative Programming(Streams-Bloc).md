# Reactive Programming part 1 - Stream

## 스트림이란 무엇인가?

스트림을 이해하기 위해선 파이프를 상상해야 한다.

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

```
import 'dart:async';

void main() {
  //step 1 StreamController 등록
  final StreamController ctrl = StreamController();

  //step 2 listen 등록
  final StreamSubscription subscription = ctrl.stream.listen((data) => print('$data'));

  //step 3 streamcontroller 에 sink를 통해 입력
  ctrl.sink.add('Hello Stream');
  ctrl.sink.add(1004);
  ctrl.sink.add({'1':100,'2':'200'});

  //사용 종류 후 close()
  ctrl.close();
}
```

### Broadcast Streams

* 다수의 리스너에 동시에 배출을 할 수 있다. 

```
import 'dart:async';

void main() {
  final StreamController ctrl = StreamController<int>.broadcast();

  ctrl.stream
    .where((value) => value % 2 == 0) //StreamTransformer 적용함
    .listen((data) => print('$data'));

  for(int i=0; i< 10; i++) {
    ctrl.sink.add(i);
  }

  ctrl.close();
}
```

## 그러면 Flutter에서는 연동을 어떻게 할까?

Flutter Widget에서는 `StreamBuilder`라고 지원해준다.

```
StreamBuilder<T>(
    key: ...optional, the unique ID of this Widget...
    stream: ...the stream to listen to...
    initialData: ...any initial data, in case the stream would initially be empty...
    builder: (BuildContext context, AsyncSnapshot<T> snapshot){
        if (snapshot.hasData){
            return ...the Widget to be built based on snapshot.data
        }
        return ...the Widget to be built if no data is available
    },
)
```

StreamBuilder를 이용해서 Counter 를 1씩 올리는 예제

```
import 'dart:async';
import 'package:flutter/material.dart';

class CounterPage extends StatefulWidget {
  @override
  _CounterPageState createState() => _CounterPageState();
}

class _CounterPageState extends State<CounterPage> {
  int _counter = 0;
  //Step1 StreamController 선언
  final StreamController<int> _streamController = StreamController<int>();

  @override
  void dispose(){
    _streamController.close();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Stream version of the Counter App')),
      body: Center(
        child: StreamBuilder<int>(
          //Step2 Stream Listener 등록
          stream: _streamController.stream,
          initialData: _counter,
          builder: (BuildContext context, AsyncSnapshot<int> snapshot){
            return Text('You hit me: ${snapshot.data} times');
          }
        ),
      ),
      floatingActionButton: FloatingActionButton(
        child: const Icon(Icons.add),
        onPressed: (){
          //StreamController에 데이터 입력
          _streamController.sink.add(++_counter);
        },
      ),
    );
  }
}
```

그럼 다음 시간에는 Bloc 패턴을 배워보는 시간을 가져보자.

