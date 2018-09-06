# Scaffold 위젯 추가

기본적으로 모바일 구조가 상단에 `Appbar`가 있으며 하단에 바텀시트가 있으며 `floating action button`이 있다.

그 구조를 기본적으로 지원하는 하나의 위젯이 `Scaffold`라고 보면 된다.

그럼 하나씩 적용해보자.

```dart
import 'package:flutter/material.dart';

void main() {
  var app = MaterialApp(
    home: Scaffold(
      appBar: AppBar(),    // 추가  
    ),
  );

  runApp(app);
}
```

![](/assets/flutter-3.png)

짜잔~ 이런 타이틀바가 생긴걸 볼수 있다. 이걸 AppBar 라고 한다.

그리고 아래 `floating button` 을 추가도 아주 쉽게 할수 있다. 

```dart
import 'package:flutter/material.dart';

void main() {
  var app = MaterialApp(
    home: Scaffold(
      appBar: AppBar(
        title: Text("Lets see some images!"),        
      ),
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.add), //Widget 추가
        onPressed: () { // 이벤트 콜백 함수
          print('Hi there!');
        },
      ),      
    ),
  );

  runApp(app);
}
```

![](/assets/flutter-4.png)



