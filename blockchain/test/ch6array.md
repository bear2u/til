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

`Public `으로 배열을 선언 가능하다. 이 경우는 자동으로 `getter `함수가 추가된다. 

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



