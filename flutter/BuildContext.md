# Flutter BuildContext 알아보기

https://flutterbyexample.com/build-context-class를 보고 공부한 내용입니다.

Flutter 에서 BuildContext는 위젯 트리에 있는 위젯 위치라고 보면 된다. 

각각의 위젯마다 고유의 BuildContext 가 존재한다. 

```dart
class _MyHomePageState extends State<MyHomePage> {
  _MyHomePageState() {
    print(context.hashCode);
    // prints 2011
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
        title: new Text(widget.title),
      ),
      body: new Container(),
      floatingActionButton:
          new FloatingActionButton(onPressed: () => print(context.hashCode)),
          // prints 63
    );
  }
}
```

위에서 보듯이 두개의 `context` 의 `hashcode`가 다르게 보인다. 

그래서 `of` 함수를 사용시 조심해서 사용해야 한다. 

`of` 함수는 위젯트리에서 위,아래로 옮겨다니면서 해당 위젯을 참조한다. 

대표적인 예로 `Theme` 를 들수 있는데

```dart
@override
Widget build(context) {
  return new Text('Hello, World',
    style: new TextStyle(color: Theme.of(context).primaryColor),
  );
}
```

이경우 Theme 라는 위젯을 참조해서 해당 컬러를 가져오는 경우이다. 

다른 예로 `snackbar` 위젯을 들수 있다.
snackbar 표시하는 데 있어서 Scaffold 위젯 build context가 필요하다. 
그럼 이렇게 하는건 가능할까?

```dart
@override
  Widget build(BuildContext context) {
    return new Scaffold(
        appBar: new AppBar(
          title: new Text(widget.title),
        ),
        body: new Container(),
        /// Scaffold doesn't exist in this context here
        /// because the context thats passed into 'build'
        /// refers to the Widget 'above' this one in the tree,
        /// and the Scaffold doesn't exist above this exact build method
        ///
        /// This will throw an error:
        /// 'Scaffold.of() called with a context that does not contain a Scaffold.'
        floatingActionButton: new FloatingActionButton(onPressed: () {
          Scaffold.of(context).showSnackBar(
                new SnackBar(
                  content: new Text('SnackBar'),
                ),
              );
        }));
  }
```

이건 동작이 안될 것이다. 이유는 `Scaffold.of(context).showSnackBar` 에서 context가 `Scaffold` 이지 않기 때문이다. 
그래서 이걸 해결 하기 위해서는 Builder 함수를 통해서 context 를 전달을 할 수 있다. 

```dart
  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      appBar: new AppBar(
        title: new Text(widget.title),
      ),
      body: new Container(),
      /// Builders let you pass context
      /// from your *current* build method
      /// Directly to children returned in this build method
      ///
      /// The 'builder' property accepts a callback
      /// which can be treated exactly as a 'build' method on any
      /// widget
      floatingActionButton: new Builder(builder: (context) {
        return new FloatingActionButton(onPressed: () {
          Scaffold.of(context).showSnackBar(
                new SnackBar(
                  backgroundColor: Colors.blue,
                  content: new Text('SnackBar'),
                ),
              );
        });
      }),
    );
  }
```

이상으로 BuildContext를 공부해보았다.





