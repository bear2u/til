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
* 다트는 기본적으로 2칸을\(보통 4칸\) 띄우고 사용한다. 만약 툴을 이용해서 reformat을 하고 싶은 경우 `dartfmt `를 이용한다.

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









