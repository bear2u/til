Flutter 시작하기. 

Flutter는 구글에서 나온 모바일 프레임워크입니다. Reactive Native 와 비슷하다고 보면 될것 같습니다. 네이티브 성격을 가진 하이브리드 앱인 셈이죠. 

무엇보다 강력한 점은 각 플랫폼\(Android, IOS\)에 있는 대표 디자인 즉 메테리얼 디자인을 손쉽게 짤수가 있습니다. 

하지만 플랫폼별로 다르게 나올수가 있는데 그 이유는 메테리얼도 플랫폼에 맞게 바뀌어서 나올 수 있기 때문입니다. 



만약 IOS 에서 특유의 시스템을 이용하고 싶으면 [쿠퍼티노 위젯](https://flutter.io/widgets/cupertino/)을 사용하면 됩니다.

그 외에도 안드로이드 스튜디어, 비쥬얼 스튜디어 코드등 통합 IDE 도 사용 가능한 점이 큰 메리트입니다. 

더 자세한 내용을 원하시면 [공식홈페이지](https://flutter.io/)를 방문하시길 바랍니다. 

> # 구조

헬로우 플러터를 우선 보는게 중요하죠. 

각 플랫폼별로 플러터 프로젝트를 생성합니다. 

\(vscode 에선 view -&gt; 명령 팔렛트 -&gt; flutter new project 선택\)

그리고 실행을 해봅니다. \( 터미널에서 flutter run \)

중요한건 화면까지 뛰우는 구조인데. 

중요한건 4가지를 보시면 됩니다. 

1. import
2. main 함수 생성
3. main에 widget 추가
4. 그 위젯을 화면에 추가

코드로 살펴보겠습니다. 

```dart

import 'package:flutter/material.dart';

void main() {
  var app = MaterialApp(
    home: Text('Hi there!'),

  );

  runApp(app);
}
```

그럼 다음과 같이 투박한 화면이 나옵니다. ^^;;

![](/assets/flutter-2.png)

그럼 다음시간에는 좀 더 위젯을 추가하도록 해보겠습니다. 

