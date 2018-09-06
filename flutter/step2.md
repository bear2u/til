# Scaffold 위젯 추가

기본적으로 모바일 구조가 상단에 `Appbar `가 있으며 하단에 바텀시트가 있으며 `floating action button`이 있다. 

그 구조를 기본적으로 지원하는 하나의 위젯이 `Scaffold `라고 보면 된다. 



그럼 하나씩 적용해보자. 

```dart
import 'package:flutter/material.dart';

void main() {
  var app = MaterialApp(
    home: Scaffold(
      appBar: AppBar(),      
    ),
  );

  runApp(app);
}
```

![](/assets/flutter-3.png)

짜잔~ 이런 타이틀바가 생긴걸 볼수 있다. 이걸 AppBar 라고 한다. 





