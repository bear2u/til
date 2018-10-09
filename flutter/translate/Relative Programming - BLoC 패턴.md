# Relative Programming - BLoC 패턴

아래 내용은 [https://www.didierboelens.com/2018/08/reactive-programming---streams---bloc/](https://www.didierboelens.com/2018/08/reactive-programming---streams---bloc/) 을 공부하고 요약해놓은 글입니다.

BLoC Pattern 은 구글 개발자 Paolo Soares  와 Cong Hui 에 의해서 디자인 되었다. 그리고 처음 발표된 건 2018년 DartConf 이다.

[관련 영상](https://www.youtube.com/watch?v=PLHln7wHgPE)

#### BLoC = Business Logic Component.

* 하나 또는 여러개의 BLoC가 존재할수 있다. 
* Presentation Layer\(UI 부분\)에서 가능하한 제거되며 로직부분만 분리해서 테스트가 용이함
* 플랫폼 종속적이지 않다.
* 환경에 종속적이지 않다.

BLoC 패턴은 스트림을 이용해서 만들어진다.

![](/assets/flutter_bloc.png)

* 위젯은 Sinks 를 통해서 Bloc에 이벤트를 보낸다.
* 위젯은 Bloc에 있는 스트림을 통해서 결과를 통지 받는다.
* BLoC에 의해서 비즈니스 로직들은 재사용이 가능하다. 
* UI 쪽에서는 BLoC 에 대해서 알 필요가 없다.

그럼 적용은 어떻게 하는 걸까?

기본적으로 앱 플젝 새로 설치시 나오는 카운트 올리는 걸 수정해보자.

> Widget에 BlocProvider를 만들어준다.

```dart
home: BlocProvider<IncrementBloc>(
  bloc: IncrementBloc(),
  child: CounterPage(),
),
```

> Bloc 인스턴스를 생성한다.

```
final IncrementBloc bloc = BlocProvider.of<IncrementBloc>(context);
```

> 리스닝하고 싶은 위젯에 등록한다.

```dart
 body: Center(
    child: StreamBuilder<int>(
      stream: bloc.outCounter,
      initialData: 0,
      builder: (BuildContext context, AsyncSnapshot<int> snapshot){
        return Text('You hit me: ${snapshot.data} times');
      }
    ),
  ),
```

그리고 BLoC 파일을 작성해준다.

```dart
class IncrementBloc implements BlocBase {
  int _counter;

  //
  // Stream to handle the counter
  //
  StreamController<int> _counterController = StreamController<int>();
  StreamSink<int> get _inAdd => _counterController.sink;
  Stream<int> get outCounter => _counterController.stream;

  //
  // Stream to handle the action on the counter
  //
  StreamController _actionController = StreamController();
  StreamSink get incrementCounter => _actionController.sink;

  //
  // Constructor
  //
  IncrementBloc(){
    _counter = 0;
    _actionController.stream
                     .listen(_handleLogic);
  }

  void dispose(){
    _actionController.close();
    _counterController.close();
  }

  void _handleLogic(data){
    _counter = _counter + 1;
    _inAdd.add(_counter);
  }
}
```

> 카운트를 올리는 이벤트를 스트림에 보낸다. \(인풋\)

```
bloc.incrementCounter.add(null);
```

> Provider도 작성해준다.

```dart
// Generic Interface for all BLoCs
abstract class BlocBase {
  void dispose();
}

// Generic BLoC provider
class BlocProvider<T extends BlocBase> extends StatefulWidget {
  BlocProvider({
    Key key,
    @required this.child,
    @required this.bloc,
  }): super(key: key);

  final T bloc;
  final Widget child;

  @override
  _BlocProviderState<T> createState() => _BlocProviderState<T>();

  static T of<T extends BlocBase>(BuildContext context){
    final type = _typeOf<BlocProvider<T>>();
    BlocProvider<T> provider = context.ancestorWidgetOfExactType(type);
    return provider.bloc;
  }

  static Type _typeOf<T>() => T;
}

class _BlocProviderState<T> extends State<BlocProvider<BlocBase>>{
  @override
  void dispose(){
    widget.bloc.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context){
    return widget.child;
  }
}
```

전체 소스는 다음과 같다.

```dart
void main() => runApp(new MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
        title: 'Streams Demo',
        theme: new ThemeData(
          primarySwatch: Colors.blue,
        ),
        home: BlocProvider<IncrementBloc>(
          bloc: IncrementBloc(),
          child: CounterPage(),
        ),
    );
  }
}

class CounterPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final IncrementBloc bloc = BlocProvider.of<IncrementBloc>(context);

    return Scaffold(
      appBar: AppBar(title: Text('Stream version of the Counter App')),
      body: Center(
        child: StreamBuilder<int>(
          stream: bloc.outCounter,
          initialData: 0,
          builder: (BuildContext context, AsyncSnapshot<int> snapshot){
            return Text('You hit me: ${snapshot.data} times');
          }
        ),
      ),
      floatingActionButton: FloatingActionButton(
        child: const Icon(Icons.add),
        onPressed: (){
          bloc.incrementCounter.add(null);
        },
      ),
    );
  }
}

class IncrementBloc implements BlocBase {
  int _counter;

  //
  // Stream to handle the counter
  //
  StreamController<int> _counterController = StreamController<int>();
  StreamSink<int> get _inAdd => _counterController.sink;
  Stream<int> get outCounter => _counterController.stream;

  //
  // Stream to handle the action on the counter
  //
  StreamController _actionController = StreamController();
  StreamSink get incrementCounter => _actionController.sink;

  //
  // Constructor
  //
  IncrementBloc(){
    _counter = 0;
    _actionController.stream
                     .listen(_handleLogic);
  }

  void dispose(){
    _actionController.close();
    _counterController.close();
  }

  void _handleLogic(data){
    _counter = _counter + 1;
    _inAdd.add(_counter);
  }
}

///
```

```dart
// Generic Interface for all BLoCs
abstract class BlocBase {
  void dispose();
}

// Generic BLoC provider
class BlocProvider<T extends BlocBase> extends StatefulWidget {
  BlocProvider({
    Key key,
    @required this.child,
    @required this.bloc,
  }): super(key: key);

  final T bloc;
  final Widget child;

  @override
  _BlocProviderState<T> createState() => _BlocProviderState<T>();

  static T of<T extends BlocBase>(BuildContext context){
    final type = _typeOf<BlocProvider<T>>();
    BlocProvider<T> provider = context.ancestorWidgetOfExactType(type);
    return provider.bloc;
  }

  static Type _typeOf<T>() => T;
}

class _BlocProviderState<T> extends State<BlocProvider<BlocBase>>{
  @override
  void dispose(){
    widget.bloc.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context){
    return widget.child;
  }
}
```

만약 여러개의 위젯에 BLoC을 붙일수 있나?

child 형태로 붙일수 있다.

```dart
void main() => runApp(
  BlocProvider<ApplicationBloc>(
    bloc: ApplicationBloc(),
    child: MyApp(),
  )
);

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context){
    return MaterialApp(
      title: 'Streams Demo',
      home: BlocProvider<IncrementBloc>(
        bloc: IncrementBloc(),
        child: CounterPage(),
      ),
    );
  }
}

class CounterPage extends StatelessWidget {
  @override
  Widget build(BuildContext context){
    final IncrementBloc counterBloc = BlocProvider.of<IncrementBloc>(context);
    final ApplicationBloc appBloc = BlocProvider.of<ApplicationBloc>(context);

    ...
  }
}
```



