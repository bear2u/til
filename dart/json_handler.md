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

그리고 모델 클래스를 만들어서 매핑하고자 할때에는 값을 키값을 줘서 값을 가져온다음 set 을 통해서 모델을 만들수 있다.

```dart
import 'dart:convert';

void main() {
  var rawJson = '{"url": "http://blah.jpg","id":1}';

  var parsedJson = json.decode(rawJson);
  var imageModel = new ImageModel(
            parsedJson['id'], 
            parsedJson['url']
  );

  print(imageModel);

}

class ImageModel {
  int id;
  String url;  

  ImageModel(this.id, this.url);

  String toString() {
    return '$id,$url';
  }
}
```

하지만 만약 여러개 속성을 파싱할때 이 방식은 좀 불편하다.

그래서 fromJson 함수를 제공해준다. 이 함수는 JSON 을 미리 정의된 값을 매핑해주는 역할을 한다.

그럼 코드를 보면 이해가 빠를거라 본다. 

[관련 링크](https://flutter.io/json/)

```dart
import 'dart:convert';

void main() {
  var rawJson = '{"url": "http://blah.jpg","id":1}';

  var parsedJson = json.decode(rawJson);
  var imageModel = new ImageModel.fromJson(parsedJson);

  print(imageModel);

}

class ImageModel {
  int id;
  String url;  

  ImageModel(this.id, this.url);

  ImageModel.fromJson(Map<String, dynamic> parsedJson) 
    : id = parsedJson['id'],
      url = parsedJson['url'];


  String toString() {
    return '$id,$url';
  }
}

............
1,http://blah.jpg
```



