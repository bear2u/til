# Await / Future 비동기 함수 호출

Http 로 통신할때 일반적으로 비동기로 호출을 하는 편이다. 비동기란 함수 호출시 블럭이 되지 않고 한번 다 훝고 콜백식으로 완료가 됐을 경우 다시 결과를 받는 경우를 말한다.

만약 아래와 같은 코드에서 실행시 결과값은 마지막에 `'I got the data'` 로 떨어지게 된다.

그리고 자바스크립트에서 Promise 형태와 비슷한 걸 볼수 있다.

```dart
import 'dart:async';

void main () {
  print('1. start to fetch data');

  get('http://weasds.com')
    .then((data) {
      print(data);
    });

  print('3. finish call to fetch the data');
}

Future<String> get(String url) {
  return new Future.delayed(new Duration(seconds: 3), () {
    return '2. I got the data!!';
  });
}
...........
1. start to fetch data
3. finish call to fetch the data
2. I got the data!!
// 순서가 1-3-2 로 되는 걸 볼수 있다.
```

그리고 `Promise`말고도 `await`로 비동기를 기다렸다가 가져오는 방법도 지원한다.

```dart
import 'dart:async';

void main () async {
  print('1. start to fetch data');

  print(await get('http://weasds.com'));

  print('3. finish call to fetch the data');
}

Future<String> get(String url) {
  return new Future.delayed(new Duration(seconds: 3), () {
    return '2. I got the data!!';
  });
}

.........

1. start to fetch data
2. I got the data!! //약 3초뒤 나온다.
3. finish call to fetch the data
```

참고

* [https://pub.dartlang.org/documentation/http/latest/http/http-library.html](https://pub.dartlang.org/documentation/http/latest/http/http-library.html)
* [https://api.dartlang.org/dev/2.1.0-dev.2.0/dart-async/Future-class.html](https://api.dartlang.org/dev/2.1.0-dev.2.0/dart-async/Future-class.html)
* [https://pub.dartlang.org/packages/async](https://pub.dartlang.org/packages/async)



