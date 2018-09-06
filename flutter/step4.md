# StatefulWidget

StatefulWidget은 위젯에 변경되는 state 를 담고 있는 커스텀 위젯을 뜻한다. 

그럼 어떤식으로 만들어지는 지 살펴보자. 

> 우선 StatefulWidget 을 extends 한다.

```
class App extends StatefulWidget {
    
}
```

> State를 상속받은 클래스를 만든다.

```
class AppState extends State<App> {
  int counter = 0;

  Widget build(context) {
    return MaterialApp(
      home: Scaffold(
        body: Text('$counter'),
        appBar: AppBar(
          title: Text("Lets see some images!"),
        ),
        floatingActionButton: FloatingActionButton(
          child: Icon(Icons.add), //Widget 추가
          onPressed: () {
            // 이벤트 콜백 함수            
            setState(() {
              counter += 1;
            });
          },
        ),
      ),
    );
  }
}
```

> createState 함수를 StatefullState 클래스에 정의를 한다.

```dart
@override
State<StatefulWidget> createState() {
  // TODO: implement createState
  return AppState(); // 전 단계에서 만든 클래스
}
```

> 마지막으로 변경될 값을 setState\(\) 함수내에서 정의한다.

여기에선 floating action button 을 클릭시 counter 를 1씩 올리는 작업이 이루어진다. 

```dart
....
onPressed: () {
  // 이벤트 콜백 함수            
  setState(() {
    counter += 1; // 1씩 올리고 있다.
  });
},
....          
```

![](/assets/flutter9.png)

