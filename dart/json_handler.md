# JSON 핸들러

서버와 통신시 제일 자주 사용되는 형태가 JSON 이다. 그럼 다트에서는 어떻게 다룰수 있는 지 살펴보겠다. 

테스트 환경은 [https://dartpad.dartlang.org/](https://dartpad.dartlang.org/) 에서 진행한다. 

```dart
import 'dart:convert';

void main() {
  var rawJson = '{"url": "http://blah.jpg","id":1}';
  
  var parsedJson = json.decode(rawJson);
  print(parsedJson);
  print(parsedJson['url']);
  
}

.............
{url: http://blah.jpg, id: 1}
http://blah.jpg
```



