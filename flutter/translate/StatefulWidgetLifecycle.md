# Stateful Widget Lifecycle

https://flutterbyexample.com/stateful-widget-lifecycle/#6-didupdatewidget
을 공부해서 요약한 글입니다.<br/>자세한 내용은 원문확인 부탁드립니다. 


StatefulWidget을 만들때 `State` 라는 오브젝트를 만든다. 이 오브젝트는 위젯이 동작하는 동안 `mutable state`를 뜻한다.

`state`의 컨셉은 두가지로 정의되어 진다. 
1. 위젯에 의해서 사용되어지는 데이터는 변할수 있다. 
2. 위젯이 빌드 될때 데이터들을 동기적으로 읽을수 없다. 모든 `State` 들은 `build` 함수가 호출될 때까지 설정되어야 한다. 

# StatefulWidget 와 State 는 클래스를 분리를 한 이유는?

> 성능때문이다.

## 1. createState()
Framework가 StatefulWidget을 만들경우 createState() 가 즉시 호출된다. 

```dart
class MyHomePage extends StatefulWidget {
  @override
  _MyHomePageState createState() => new _MyHomePageState();
}
```

## 2. mounted is true
`createState` 함수는 `buildContext` 가 state 에 할당되게 된다. 
`BuildContext` 는 위젯트리를 단순하기 위해 필요한 것이다. (좀 더 공부 필요)

모든 위젯은 `this.mounted : bool` 속성을 가지고 있다. 즉 `buildContext` 가 할달될 때 `this.mounted` 가 true 로 리턴된다. 

위젯이 `unmounted` 일 경우에는 `setState` 를 부를 경우 에러가 발생될 수 있습니다.

> 이 속성은 중요하다고 볼 수 있습니다. setState() 함수를 호출시 만약 비동기로 시간이 지연되는 경우 widget이 마운트가 해제될 수 있기 때문에 `if(mounted) {...}` 을 사용해서 setState() 를 하는 걸 추천하다. 

## 3. initState()

widget이 만들어지고 생성자 후에 처음 메소드 실행할때 이 함수가 실행된다. `super.initState()` 를 필수적으로 호출해야 한다. 

이 함수를 호출할 때 최고의 선택은 

- 위젯 인스턴스를 만들기 위해 `BuildContext` 를 이용해서 데이터들을 초기화를 할 경우
- 위젯 트리에서 부모 위젯의 속성을 초기화할때
- 스트림을 구독(리스닝) 할 때

```dart
@override
initState() {
  super.initState();
  // Add listeners to this class
  cartItemStream.listen((data) {
    _updateWidget(data);
  });
}
```

## 4. didChangeDependencies()
이 함수는 `initState`를 호출한 뒤에 실행된다. 또한 이 위젯은 데이터에 의존하는 객체가 호출될 때마다 호출됩니다. `InheritedWidget` 에 의존하는 경우 업데이틀 합니다. 

`build` 는 항상 `didChangeDependencies` 호출 후에 실행되는 점을 명심하자. 잘 사용하지 않지만 `BuildContext.inheritFromWidgetOfExactType` 을 호출하기 위해서 첫단계 시작점이다. 
이건 데이터를 상속받는 위젯의 변경사항을 리스닝 하게 만든다. 

> 이 함수는 상속된 위젯이 업데이트를 하는 경우 당신이 네트워크 호출이라던가 그런 코스트가 많이 드는 액션을 할 때 유용하다. 

## 5. build()
필수적으로 오버라이딩해서 구현해야되는 함수이다. 위젯을 리턴한다. 

## 6. didUpdateWidget(Widget oldWidget)
만약 부모 위젯이 업데이트가 되거나 이 위젯이 다시 만들 경우 이 함수가 호출되고 같은 `runtimeType` (이건 또 뭐징?) 을 함께 다시 만들어진다. 

Flutter는 state를 재사용한다. 이 경우 `initState` 에서 값을 초기화를 해야 할수 있다. 

build 함수가 변경 할 수 있는 스트림이나 다른 개체를 사용하는 경우 이전 개체에서 구독을 취소하고 `didUpdateWidget` 에서 새 인스턴스에 다시 구독합니다.

> 이 메소드는 기본적으로 상태와 관련된 위젯이 rebuild 될 것으로 예상 되는 경우 `initState` 를 대체합니다.

```dart
@override
void didUpdateWidget(Widget oldWidget) {
  if (oldWidget.importantProperty != widget.importantProperty) {
    _init();
  }
}
```

## 8. deactivate()

## 9. dispose()
영구적인 State Object가 삭제될때 호출된다. 
이 함수는 주로 Stream 이나 애니메이션 을 해제시 사용된다. 

## 10. mounted is false
state object가 다시 마운팅 되지 않을 경우 setState 호출시 에러를 리턴한다. 

