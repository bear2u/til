# Transform 활용

Stream 사용시 주어진 값들을 이용해서 가공할 일이 생길수 있다. <br/> 
그럴경우 Transform 을 활용해서 가능하다. 

우선 예제를 보면서 살펴보자. 
- 이메일을 넣는 경우 유효성 체크 하는 validate stream 을 만든다고 가정하자. 
- '@' 포함되는 경우 정상, 없는 경우 오류로 리턴하는 아주 간단한 예제

우선 bloc 패턴을 이용할 예정이다. 

- bloc 생성하자. 
- StreamController 생성
- sink 정의
- transform 정의 
  
```dart
...

final bloc = new Bloc();

...

class Bloc {
    final emailController = StreamController<String>();
    
    Function(String) get changeEmail => emailController.sink.add;
    
    Stream<String> get email => emailController.stream.transform(validateEmail);
    
    final validateEmail = 
        StreamTransformer<String, String>.fromHandlers(handleData: (email, sink) {
        if(email.contains('@')){
            sink.add(email); //정상
        } else {
            sink.addError('Enter a valid email'); //실패
        }        
        });
}
```

- Main 함수 정의

```dart

  bloc.email.listen((value) {
    print(value);
  });
  
  bloc.changeEmail('test'); // => Error
  bloc.changeEmail('test@test.net'); // => 정상

```

전체 소스는 다음과 같다. 

```dart
import 'dart:async';

void main() {
  final bloc = new Bloc();
    
  bloc.email.listen((value) {
    print(value);
  });
  
  bloc.changeEmail('test@test.net');
}

class Bloc {
  final emailController = StreamController<String>();
    
  Function(String) get changeEmail => emailController.sink.add;
  
  Stream<String> get email => emailController.stream.transform(validateEmail);
  
  final validateEmail = 
    StreamTransformer<String, String>.fromHandlers(handleData: (email, sink) {
      if(email.contains('@')){
       sink.add(email); 
      } else {
        sink.addError('Enter a valid email');
      }        
    });
  
}
```
