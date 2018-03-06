배열에는 `정적`배열과 `동적`배열 두 종류가 있다.

```js
unit[2] fixedArray; //2개의 원소를 담을 수 있는 고정길이 배열

string[5] stringArray; //또다른 고정 배열으로 5개의 스트링을 담을 수 있다. 

unit[] dynamicArray; //동적배열은 고정된 크기가 없으며 계속 크기가 커질 수 있다.
```

구조체 배열을 생성할 수 있다.

```js
Person[] people: //이는 동적 배열로 원소를 계속 추가 가능함
```

`Public`으로 배열을 선언 가능하다. 이 경우는 자동으로 `getter`함수가 추가된다.

```js
Person[] public people //public이 중간에 붙는 걸 유의하자
```

배열에 추가를 하는 경우

```
Person satoshi = Person(172, "Satoshi");
people.push(satoshi) //이런식으로 추가 가능하다.
```

함수 선언은 다음과 같다.

```
function eat(string _name, uint _amount) {
    //TODO
}

eat('hambuger',1000); //함수의 실행
```

`private`으로도 생성을 할 수 있다.

```
function addToArray(uint _number) private {
  numbers.push(_number)
}
```

반환값 반환하는 경우

```
string greeting = "what's up dog"

function sayHello() public returns (string) { //string 타입으로 리턴합니다. 
  return greeting;
}
```

함수제어자의 경우 `view `와 `pure `를 가지고 있다. 

view의 경우 함수가 데이터를 보기만 하고 변경하지 않는 다는 뜻이다. 

pure는 어떤 데이터도 접근하지 않는 것을 의미한다. 앱에서 읽지도 않고, 반환값이 함수에 전달된 인자값에 따라서 달라진다. 

> 솔리디티 컴파일러는 이러한 부분에 대해서 무엇을 쓰면 좋을지 경고를 표시해준다.

```js
function sayHello() public view returns (string) {
        
}

function _multiply(uint a, uint b) private pure returns (uint) {
  return a * b;
}
```

**랜덤문자 생성**

솔리디티에서 랜덤 발생하는 방법은 SHA3의 한 종류인 `keccak256 `을 이용하면 된다. 

> 완벽하고 안전한 난수 발생기는 아닌걸 유의하자.

```
//6e91ec6b618bb462a4a6ee5aa2cb0e9cf30f7a052bb467b0ba58b8748c00d2e5
keccak256("aaaab");
//b1f078126895a1424524de5321b339ab00408010b7cf0e6ed451514981e58aa9
keccak256("aaaac");
```

위 글자 하나때문에 값이 완전히 달라지는 걸 유념하자. 

**형 변환**

가끔씩 형병환 할 경우가 있다. 

```
uint a = 5;
uint b = 6;
// a * b가 uint8이 아닌 uint를 반환하기 때문에 에러메세지가 나옴
uint8 c = a * b
// b를 unit8 으로 형변환해서 코드가 제대로 동작하기 위해서...
uint8 c = a * uint8(b);

```



