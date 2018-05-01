# 다트 기본

### 함수편

> 기본 출력

```dart
void main() {
    var test = "test";
    print('test = $test');
}
```

> 기본값 설정

```dart
void printMsg(String msg, [String value = 'undefined']) {
  print('msg = $msg, value = $value');
}
void main() {
  printMsg('1234', '5678');
  printMsg('1234');
}

........................
msg = 1234, value = 5678
msg = 1234, value = undefined
```

> name, value 쌍으로 map 형태로 인수 넣기

```dart
void printMsg({String msg, String value}) {
  print('msg = $msg, value = $value');
}
void main() {
  printMsg(
    msg: "1234",
    value: "5678"
  );
}
..............................
msg = 1234, value = 5678
```

> 함수를 인자로 넣을수도 있다.

```dart
double add(double a, double b) => a + b;

//Function 정의자를 유의해서 보자
Function makeAdder(double value) {
  return (x) => add(value, x);
}

void main() {
  var adder1 = makeAdder(10.0);
  var adder2 = makeAdder(20.0);

  print('adder1 = ${adder1(5)} , adder2 = ${adder2(10)}');
}
...................................
adder1 = 15 , adder2 = 30
```

### 리스트

> 일반 리스트에 추가
>
> **유의할 점은 같은 타입만 추가 가능하다.**

```dart
void main() {
  List<int> list = [2, 1, 3];
  list.add(4);
  print(list);
  print(list[0]);
}
..............

[2, 1, 3, 4]
2
```

> for문 사용해서 출력

```dart
void main() {
  List<int> list = [2, 1, 3];
  printList(list);
}

void printList(List<int> list) {
  for(var i=0; i< list.length; i++) {
    print(i);
  }
}
```

> foreach 사용

```dart
void main() {
  List<int> list = [2, 1, 3];
  usingForEach(list);
}

void usingForEach(List<int> list) {
  list.forEach( (x) {
    print(x);
  });
}

//짧게 설정 가능
void usingForEachShort(List<int> list) {
  list.forEach( (x) => print(x));
}
```

#### 맵

```dart
void main() {
  Map<String, int> cats = {
    'ABC': 1,
    'DEF': 2
  };

  print(cats);
}
```

#### Class

```dart
class Cat {
  String name;
  int age;
  
  @override
  String toString() {
    return '$name , $age';
  }
}

void main() {
  final cat = new Cat();
  cat.name = 'steve';
  cat.age = 10;
  
  print(cat.toString());
}
```



