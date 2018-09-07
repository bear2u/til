# Flutter App Tutorial \#1

이 글의 모든 내용은 udemy 강좌를 공부하고 따라해본 내용입니다. 

[https://www.udemy.com/dart-and-flutter-the-complete-developers-guide](https://www.udemy.com/dart-and-flutter-the-complete-developers-guide)

완성 된 형태이다. 

 첫 화면에서 오른쪽 아래 플로팅 버튼 클릭시 오른쪽 화면처럼 이미지와 글자를 가진 뷰가 하나씩 추가되는 형태이다. 

소스는 http json 을 통해서 가져왔다. 

![](/assets/flutter_sample_1.jpg)

> main.dart 시작

`flutter`는 첫진입은 일반적으로 `lib/main.dart` 로 시작된다. 

* 매트리얼 테마를 사용하기 위해서 import를 한다. 
* runApp 은 메테리얼 라이버러리를 통해 실행된다. 
* App 은 따로 빼서 진행한다. 

```dart
# lib/main.dart

import 'package:flutter/material.dart';
import 'src/app.dart';

void main() {  
  runApp(App());
}
```

lib에 src 폴더를 만들고 `app.dart` 를 생성한다. 

* StatefulWidget을 통해서 서버로 들어오는 아이템들을 리스트로 보내주고 있다. 
* Scaffold 는 Floating Action Button 과 타이틀바 및 바텀바등을 기본적으로 가지는 위젯이다. 
* await 는 비동기방식을 동기형태로 바꿔주고 네트워크 결과값을 가져온다. 
* 그리고 미리 정의된 ImageModel.FromJson 을 통해 디코딩을 거친다. 
* 마지막으로 setState\(\)을 통해 images 라는 리스트에 아이템을 추가를 해서 state 변경을 한다. 

```dart
# lib/src/app.dart

import 'package:flutter/material.dart';
import 'package:http/http.dart' show get;
import 'models/image_model.dart';
import 'dart:convert';
import 'widgets/image_list.dart';

class App extends StatefulWidget {
  @override
    State<StatefulWidget> createState() {
      // TODO: implement createState
      return AppState();
    }
}

class AppState extends State<App> {
  int counter = 0;
  List<ImageModel> images = [];

  void fetchImage() async {
    counter++;
    var response = await get('https://jsonplaceholder.typicode.com/photos/$counter');
    var imageModel = ImageModel.fromJson(json.decode(response.body));

    setState(() {
      images.add(imageModel);
    });

  }

  Widget build(context) {
    return MaterialApp(
      home: Scaffold(
        body: ImageList(images),
        appBar: AppBar(
          title: Text("Lets see some images!"),
        ),
        floatingActionButton: FloatingActionButton(
          child: Icon(Icons.add), //Widget 추가
          onPressed: fetchImage,
        ),
      ),
    );
  }
}

// Must define a 'build' method that returns
// the widgets that *this* widget will show

```

> 모델 정의

서버로 부터 가져온 JSON 결과값을 dart 내 오브젝트 형태로 변경할수 있도록 할수 있다. 

```dart
# lib/src/models/image_model.dart

/**
 * JSON Decoding Model
 */
class ImageModel {
  int id;
  String url;
  String title;

  ImageModel(this.id, this.url, this.title);

  ImageModel.fromJson(Map<String, dynamic> parsedJson) {
    id = parsedJson['id'];
    url = parsedJson['url'];
    title = parsedJson['title'];
  }

  // 이렇게도 정의할 수 있다.
  // ImageModel.fromJson(Map<String, dynamic> parsedJson)
  // : id = parsedJson['id'],
  //   url = parsedJson['url'],
  //   title = parsedJson['title'];

  @override
    String toString() {
      // TODO: implement toString
      return '$id,$url,$title';
    }
}
```

> 리스트 뷰 정의

* ListView 의 경우 React상에 바보뷰라고 하는 상태가 전혀 없고 값만 가지고 핸들링을 한다. 
* final 사용을 통해 값이 immutable 한지 체크를 할 수 있다.  \(바뀌면 안된다\)
* 그리고 위젯을 함수로 빼서 사용가능하다. \(buildImage\)
* border의 경우 decoration 으로 가능하다. 
* 생성자로 값을 받아서 그 값을 뷰에 세팅해주고 있다. 

```dart
import 'package:flutter/material.dart';
import '../models/image_model.dart';

class ImageList extends StatelessWidget {
  final List<ImageModel> images;

  ImageList(this.images);

  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return ListView.builder(
        itemCount: images.length,
        itemBuilder: (context, int index) {
          return buildImage(images[index]);
        });
  }

  Widget buildImage(ImageModel image) {
    return Container(
      decoration: BoxDecoration(
        border: Border.all(color: Colors.grey),
      ),
      padding: EdgeInsets.all(20.0),
      margin: EdgeInsets.all(20.0),
      child: Column(
        children: <Widget>[
          Padding(
            child: Image.network(image.url),
            padding: EdgeInsets.only(bottom: 10.0),
          ),        
          Text(image.title)
        ]),
    );
  }
}

```



이로써 아주 간단한 앱을 만들어보았다. 

큰 흐름을 이해 할수 있는 소스였다. 



