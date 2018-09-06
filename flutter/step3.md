# 커스텀 위젯 추가

하나의 파일에 많은 코드를 추가시 복잡해지고 길어지는 단점이 있다. 이럴때 파일로 빼서 분리를 할수 있다.

### 임포트 방법![](/assets/flutter-7.png)

내부에 src 폴더를 만들고 그 안에 app.dart 파일을 만들자. 

클래스 구조를 만들수 있다.

1. import 매트리얼 
2. Stateless\(StatefulWidget\)Widget class 생성
3. Build 함수 구현
4. main.dart 에서 src/app.dart 호출

```dart
# src/app.dart

import 'package:flutter/material.dart';

class App extends StatelessWidget {
  Widget build(context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text("Lets see some images!"),
        ),
        floatingActionButton: FloatingActionButton(
          child: Icon(Icons.add), //Widget 추가
          onPressed: () {
            // 이벤트 콜백 함수            
          },
        ),
      ),
    );
  }
}
```

```dart
# main.dart

import 'package:flutter/material.dart';
import 'src/app.dart';

void main() {  
  runApp(App());
}
```

그런데 여기에서 StatelessWidget 과 StatefulWidget 이 나오는데 차이점은 무엇일까?

`StatelessWidget`의 경우 상태관리가 필요없는 위젯을 뜻한다. 즉 다시말해 내부에서 변하는 값을 가지는 게 없는 경우이다. 

`StatefulWidget`은 반대로 state 관리를 함으로써 값이 변할때 다시 랜더링을 해주는 차이이다. 

`StatefulWidget`은 어떻게 만들어지는 다음 시간에 알아보도록 하겠다. 





