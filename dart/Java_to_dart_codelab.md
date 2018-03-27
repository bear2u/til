# Java for Dart Codelab

다트는 타입이 있는 언어입니다.

온라인에서 에디터를 실행 후 바로 테스팅이 가능합니다.

[https://dartpad.dartlang.org/](https://dartpad.dartlang.org/)

* 다트를 특별하게 만드는 이유
* 생성자를 만드는 방법
* 매개 변수를 지정하는 다양한 방법
* getter , setter 를 만드는 방법 및 시기
* 다트가 privacy 를 처리하는 방법
* 팩토리를 만드는 방법
* 다트에서 함수형 언어가 동작되는 방법
* 다른 다트 콘셉

최종적으로 만드는 내용은

![](/assets/dart_learn1.png)

그럼 시작해보자.

```
class Bicycle {
  int cadence;
  int speed;
  int gear;
}

void main() {

}
```

> main 함수로 실행한다.

* 만약 argument를 가지고 싶다면 `main(List<String> args)` 를 사용할 수 있다. 
* 기본적으로 접근 제어자는 가지고 있지 않다. 예를 들어 `public, private, protected`
* 다트는 기본적으로 2칸을\(보통 4칸\) 띄우고 사용한다. 만약 툴을 이용해서 reformat을 하고 싶은 경우 `dartfmt`를 이용한다.

# 생성자

클래스 생성자 지정이 가능하다.

생성시 바로 this 로 지정해서 **내부 변수**로 접근이 가능하다.

```
class Bicycle {  
  int cadence;
  int speed;
  int gear;

  Bicycle(this.cadence, this.speed, this.gear);
}

void main() {
  var bike = new Bicycle(2, 0, 1); //초기화
  print(bike);
}

...........................
Instance of 'Bicycle' // 결과
```

# 초기화

일반 클래스 초기화를 하는 것처럼 New 를 붙여서 한다.

# 오버라이드 함수

내부적으로 함수를 넣을 수 있다.

```
class Bicycle {
  int cadence;
  int speed;
  int gear;

  Bicycle(this.cadence, this.speed, this.gear);

  @override
  String toString() => 'Bicycle: $speed mph'; // 자바에서 toString과 유사하다.
}

void main() {
  var bike = new Bicycle(2, 100, 1);
  print(bike);
}
```

# Getter 와 Setter

다트에선 각각의 변수에 대해 Getter 와 Setter를 제공한다. 그러기 위해선

다트에선 자바와 조금 다르게 처리를 한다.

Getter / Setter 지정하길 원하면 제어자를 붙이면 된다.

![](/assets/dart1-2.png)

Setter를 따로 지정하지 않아서 컴파일 시 오류가 발생을 한다.

Setter를 붙이는 경우

```js
class Bicycle {
  int cadence;
  int _speed;
  int gear;

  Bicycle(this.cadence, this._speed, this.gear);

  int get speed => _speed;
  set speed(int speedNum) {
    _speed = speedNum;
  }

  void applyBrake(int decrement) {
    _speed -= decrement;
  }

  void speedUp(int increment) {
    _speed += increment;
  }

  @override
  String toString() => 'Bicycle: $_speed mph';
}

void main() {
  var bike = new Bicycle(2, 100, 1);
//   bike.applyBrake(10);
  print(bike._speed);
  bike.speed = 10;
  print(bike._speed);
}

------------------------------------------------
100
10
```

# Optional 파라미터

파라미터 지정시 선택적으로 가능하다.

```java
import 'dart:math';

class Rectangle {
  int width;
  int height;
  Point origin;

  Rectangle({this.origin = const Point(0,0), this.width = 0, this.height = 0});

  @override
  String toString() => 'Origin: (${origin.x}, ${origin.y}), width: $width, height: $height';
}

main() {
  print(new Rectangle(origin: const Point(10, 20), width: 100, height: 200));
  print(new Rectangle(origin: const Point(10, 10)));
  print(new Rectangle(width: 200));
  print(new Rectangle());
}

...................................
Origin: (10, 20), width: 100, height: 200
Origin: (10, 10), width: 0, height: 0
Origin: (0, 0), width: 200, height: 0
Origin: (0, 0), width: 0, height: 0
```

# Factory를 이용해서 생성하는 방법

아래와 같이 Circle, Square 클래스가 있다고 가정하자.

그리고 생성시 각자 공통적으로 묶을수 있다고 한다.

```js
import 'dart:math';

abstract class Shape {
  num get area;
}

class Circle implements Shape {
  final num radius;
  Circle(this.radius);
  num get area => PI * pow(radius, 2);
}

class Square implements Shape {
  final num side;
  Square(this.side);
  num get area => pow(side, 2);
}

main() {
  var circle = new Circle(2);
  var square = new Square(2);
  print(circle.area);
  print(square.area);
}

................................
12.566370614359172
4
```

> 우선 최상위 함수로 올려보자. 유의해서 볼껀 Shape 클래스를 제어자 쪽으로 뺀 것이다.

```js
import 'dart:math';

abstract class Shape {
  num get area;
}

class Circle implements Shape {
  final num radius;
  Circle(this.radius);
  num get area => PI * pow(radius, 2);
}

class Square implements Shape {
  final num side;
  Square(this.side);
  num get area => pow(side, 2);
}

//Shape 제어자를 눈여겨 보자.
Shape shapeFactory(String type) {
  if (type == 'circle') return new Circle(2);
  if (type == 'square') return new Square(2);
  throw 'Can\'t create $type'; 
}

main() {
  var circle = shapeFactory('circle');
  var square = shapeFactory('square');

  print(circle.area);
  print(square.area);
}
..........................................
12.566370614359172
4
```

두번째 방법은 `Shape`클래스 안에 `factory`생성자를 만드는 것이다.

```java
 import 'dart:math';

abstract class Shape {
  //팩토리 함수를 정의
  factory Shape(String type) {
      if (type == 'circle') return new Circle(2);
    if (type == 'square') return new Square(2);
    throw 'Can\'t create $type';   
  }

  num get area;
}

class Circle implements Shape {
  final num radius;
  Circle(this.radius);
  num get area => PI * pow(radius, 2);
}

class Square implements Shape {
  final num side;
  Square(this.side);
  num get area => pow(side, 2);
}

main() {
  var circle = new Shape('circle');
  var square = new Shape('square');  

  print(circle.area);
  print(square.area);
}
```

# 상속의 개념

다트에서는 모든 클래스를 인터페이스 형태로 상속이 가능하다.

```
class CircleMock implements Circle {

}
```

상속을 하고 비어있는 상태일 경우 `Missing concrete implementations`오류가 발생될 수 있다.

그래서 상속을 할 경우 getter 변수를 다 적어줘야 한다.

```
class CircleMock implements Circle {
  num get radius;
  num get area;
}
```

# 함수형 언어 지원

자바스크립트 처럼 함수형태로 활용이 가능하다.

```
String scream(int length) {
  return "A${'a' * length}h!";
}

main() {
  var values = [1, 2, 3, 5, 10, 50];
  for (var length in values) {
    print(scream(length));
  }

  //함수형으로 바꾸는 경우
  values.map(scream).forEach(print);
  values.skip(1).take(3).map(scream).forEach(print); //이런식으로 가능하다. 
}
```

더 배우기 위해선

In completing this codelab, you've gained knowledge on some differences between Java and Dart. Dart is easy to learn and, in addition, its[core libraries](https://www.dartlang.org/guides/libraries/useful-libraries)and[rich set of available packages](https://pub.dartlang.org/)increases your productivity. Dart scales well to large applications. Hundreds of Google engineers use Dart to write mission critical apps that bring in much of Google's revenue.

## Next steps

A 20-minute codelab isn't long enough to show you all of the differences between Java and Dart. For example, this codelab hasn't covered:

* async/await, which allows you to write asynchronous code as if it were synchronous. Check out
  [this DartPad example](https://dartpad.dartlang.org/fb763a4a770b5cdd896982e10ccf4118)
  that animates the calculation of the first 5 decimals of π.
* Method cascades, where
  [everything is a builder](http://news.dartlang.org/2012/02/method-cascades-in-dart-posted-by-gilad.html)
  !
* [Null aware operators](http://news.dartlang.org/2015/08/dart-112-released-with-null-aware.html)

If you are interested in seeing Dart technologies in action, try these codelabs:

* [Building Beautiful UIs with Flutter](https://codelabs.developers.google.com/codelabs/flutter/)
* [Firebase for Flutter](https://codelabs.developers.google.com/codelabs/flutter-firebase/)
* [Write a Material Design AngularDart Web App](https://codelabs.developers.google.com/codelabs/your-first-angulardart-web-app/)
* [Build an AngularDart & Firebase Web App](https://codelabs.developers.google.com/codelabs/angulardart-firebase-web-app/)

You can learn much more about Dart with the following articles, resources, and websites.

**Articles**

* [Why I moved from Java to Dart](https://hackernoon.com/why-i-moved-from-java-to-dart-8f3802b1d652)
* [What's your favorite Dart feature?](http://news.dartlang.org/2016/10/whats-your-favorite-dart-feature.html)

**Resources**

* [A Tour of the Dart Language](https://www.dartlang.org/guides/language/language-tour)
* [Commonly Used Dart Libraries](https://www.dartlang.org/guides/libraries/useful-libraries)
* [Effective Dart](https://www.dartlang.org/guides/language/effective-dart)

**Websites**

* Dart Language:
  [www.dartlang.org](https://www.dartlang.org/)
* Dart for the Web, including AngularDart:
  [webdev.dartlang.org](https://webdev.dartlang.org/)
* Flutter mobile app SDK:
  [flutter.io](https://flutter.io/)



