**Address**

이더리움 블록체인은 은행 계좌와 같은 계정들로 이루어져있음

예를 들어

```
0x0cE446255506E92DF41614C46F1d6df9Cc969183
```

이런 주소들을 이용해서 상호간에 이더등을 주고 받을수 있다.

> 주소는 특정 유저 \( 또는 스마트 컨트렉트\)가 소유한다

**Mapping**

\(key, Value\) 형식으로 되어 있음

```
mapping (address => uint) public accountBalance; //유저의 계좌 잔액을 보유 하는 uint 지정가능

mapping (uint => string) userIdToName; //key는 uint 이고 값은 string 이다.
```

M**sg.sender**

> 솔리디티에서 함수 실행은 항상 호출을 해야지 실행이 되는 구조를 가지고 있다. 그래서 누군가가 항상 있다는 게 성립된다.

그래서 실행하는 계정이 `msg.sender` 로 정의된다.

위에 배운 `mapping`을 이용해서 `msg.sender`를 어떻게 이용하는지 살펴보자.

```js
mapping (address => uint) favoriteNumber;

function setMyNumber(uint _myNumber) public {
  favoriteNumber[msg.sender] = _myNumber;
}

function getMyNumber() public view returns (uint) {
  return favoriteNumber[msg.sender];
}
```

**Require**

특정 조건이 아닐 경우 에러 메시지를 발생하고 실행을 멈추는 기능을 말한다.

```js
function checkName(string _name) public returns (string) {
    require(keccak256(_name) == keccak256("tommy"));

    return "toooooomy!!";
}
```

**상속**

하나의 긴 스마트 계약보다는 나누어서 여러개의 스마트 계약으로 만들 수 있다.

아래 계약은 `Animal`이 있는 함수를 상속받고 있는 `Dog`가 함수를 그대로 사용하고 있다는 것이다.

```
contract Animal {
    function eat() public returns (string) {
        return "eat";
    }
}

contract Dog is Animal {
    function eatAll() public returns (string) {
        return eat();
    }
}
```

**Import**

여러개의 솔리디티 파일\(.sol\) 을 가져와서 사용할 수 있다.

```js
import './testContract1.sol';

contract testContract2 is testContract1 {

}
```

**Storage vs Memory**

`Storage`는 블록체인 상에서 영구적으로 저장 하는 변수이다. \(전역 변수\)

`Memory`는 함수내에\(메모리\) 임시로 저장되는 변수.

솔리디티는 함수 외부에 쓰인 변수에는 자동으로 `storage`로 만들어 준다.

사용하는 경우는 전에 배웠던 `구조체`와 `배열`에서 사용된다.

```
contract SandwichFactory {
    struct Sandwich {
        string name;
        string status;
    }

    Sandwich[] sandwiches;

    function eatSandwich(uint _index) public {


        //Sandwich mySandwich = sandwiches[_index];
        //스토리지 변수에 넣으라는 경고 메세지를 볼수 있다(아래 그림 참고)

        Sandwich storage mySandwich = sandwiches[_index];
        //스토리지에 넣게 된다. 

        mySandwich.status = "Eaten!";
        //Save to storage sandwiches array items

        mySandwich.status = "Eaten!";
        //스토리즈로 저장됨

        Sandwich memory anotherSandwich = sandwiches[_index + 1];
        //하지만 복사객체를 만들어서 메모리상으로 만듬

        anotherSandwich.status = "Eaten!";
        //메모리상으로 복사객체에 넣게 되서 sandwiches 배열과는 무관함

        sandwiches[_index + 1] = anotherSandwich;
        //다시 스토리지로 저장된다. 

    }
}
```

![](/assets/zombie_1.png)

**접근 제어자**

솔리디티는 `public`, `private`, `internal`, `external`등 4가지의 제어자를 제공한다.

`internal`의 경우 함수가 정의된 컨트렉트를 상소하는 컨트렉트에서 접근 가능

`external`은 함수가 컨트렉트 밖에서만 호출될 수 있다. 즉 같은 컨트렉트안에서 해당 함수를 호출을 제한한다.

`internal`와  `private`의 경우 성격이 비슷하며

`external`은 `public` 과 성격이 비슷하다고 볼 수 있다.

**다른 컨트렉트와 상호작용**

이미 올라가져 있는 컨트렉트를 `인터페이스`로 가져와서 사용도 가능하다.

```
pragma solidity ^0.4.17;

contract LuckyNumber {
    mapping(address => uint) numbers;

    function setNum(uint _num) public {
        numbers[msg.sender] = _num;
    }
    function getNum(address _myAddress) public view returns (uint) {
        return numbers[_myAddress];
    }
}
```

위와 같은 계약이 이미 올라가 있다고 가정해보자.

그럼 인터페이스를 만들고

```js
contract NumberInterface {
    //위 계약의 getNum 를 호출한다. 
    function getNum(address _myAddress) public view returns (uint); //껍데기만 정의한다. 
}
```

이 인터페이스를 선언하고 다른 컨트렉트에서 사용하면 된다.

```js
contract MyContract {
    address addr = 0xabcdef..... //이더상의 계약 주소

    NumberInterface numberContract = NumberInterface(addr);
    //생성한다.

     function someFunction() public {
         //이제 getNum을 호출한다. 
         uint num = numberContract.getNum(msg.sender);
         //이제 LuckyNumber 상의 getNum을 호출해서 사용할 수 있게 한다. 
     }     
}
```

**다수의 반환값 처리하기**

솔리디티에서는 리턴값을 여러개를 가질 수 있다.

```js
function multipleReturns() internal returns(uint a, uint b, uint c) {
    return (1, 2, 3);
}

function processMultiReturns() external {
    uint a;
    uint b;
    uint c;
    //다수값을 설정함    
    (a, b, c) = multipleReturns();
}

function getLastReturnValue() external {
    uint c;
    //비어있는 건 빈값으로 설정가능
    (,,c) = multipleReturns();
}
```

**IF 문**

솔리디티에서 If문은 자바스크립트의 if문과 동일하다.

```js
function eatBLT(string sandwich) public {
    //스트링(문자)간의 동일 여부를 판단하기 위해선 keccak256 을 사용해서 해시값 비교를 해야한다는 점 유의
    if(keccak(sandwich) == keccak256("BLT")){
        eat();
    }
}
```

레벨 2 최종 소스

```
zombieFeeding.sol


pragma solidity ^0.4.19;

import "./zombiefactory.sol";

contract KittyInterface {
  function getKitty(uint256 _id) external view returns (
    bool isGestating,
    bool isReady,
    uint256 cooldownIndex,
    uint256 nextActionAt,
    uint256 siringWithId,
    uint256 birthTime,
    uint256 matronId,
    uint256 sireId,
    uint256 generation,
    uint256 genes
  );
}

contract ZombieFeeding is ZombieFactory {

  // 1. 이 줄을 지우게:
  address ckAddress = 0x06012c8cf97BEaD5deAe237070F9587f8E7A266d;
  // 2. 여기서 대입을 빼고 그냥 선언으로 바꾸게:
  KittyInterface kittyContract = KittyInterface(ckAddress);

  // 3. 여기 setKittyContractAddress 메소드를 추가하게

  function feedAndMultiply(uint _zombieId, uint _targetDna, string _species) public {
    require(msg.sender == zombieToOwner[_zombieId]);
    Zombie storage myZombie = zombies[_zombieId];
    _targetDna = _targetDna % dnaModulus;
    uint newDna = (myZombie.dna + _targetDna) / 2;
    if (keccak256(_species) == keccak256("kitty")) {
      newDna = newDna - newDna % 100 + 99;
    }
    _createZombie("NoName", newDna);
  }

  function feedOnKitty(uint _zombieId, uint _kittyId) public {
    uint kittyDna;
    (,,,,,,,,,kittyDna) = kittyContract.getKitty(_kittyId);
    feedAndMultiply(_zombieId, kittyDna, "kitty");
  }

}

```

**ZombieFactory.sol**

```
pragma solidity ^0.4.19;

contract ZombieFactory {

    event NewZombie(uint zombieId, string name, uint dna);

    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;

    struct Zombie {
        string name;
        uint dna;
    }

    Zombie[] public zombies;

    mapping (uint => address) public zombieToOwner;
    mapping (address => uint) ownerZombieCount;

    function _createZombie(string _name, uint _dna) internal {
        uint id = zombies.push(Zombie(_name, _dna)) - 1;
        zombieToOwner[id] = msg.sender;
        ownerZombieCount[msg.sender]++;
        NewZombie(id, _name, _dna);
    }

    function _generateRandomDna(string _str) private view returns (uint) {
        uint rand = uint(keccak256(_str));
        return rand % dnaModulus;
    }

    function createRandomZombie(string _name) public {
        require(ownerZombieCount[msg.sender] == 0);
        uint randDna = _generateRandomDna(_name);
        randDna = randDna - randDna % 100;
        _createZombie(_name, randDna);
    }

}

```





최종 web3 이용해서 테스트 가능한 소스는 다음과 같다.

```js
var abi = /* abi generated by the compiler */
var ZombieFeedingContract = web3.eth.contract(abi)
var contractAddress = /* our contract address on Ethereum after deploying */
var ZombieFeeding = ZombieFeedingContract.at(contractAddress)

// 우리 좀비의 ID와 타겟 고양이 ID를 가지고 있다고 가정하면 
let zombieId = 1;
let kittyId = 1;

// 크립토키티의 이미지를 얻기 위해 웹 API에 쿼리를 할 필요가 있다. 
// 이 정보는 블록체인이 아닌 크립토키티 웹 서버에 저장되어 있다.
// 모든 것이 블록체인에 저장되어 있으면 서버가 다운되거나 크립토키티 API가 바뀌는 것이나 
// 크립토키티 회사가 크립토좀비를 싫어해서 고양이 이미지를 로딩하는 걸 막는 등을 걱정할 필요가 없다 ;) 
let apiUrl = "https://api.cryptokitties.co/kitties/" + kittyId
$.get(apiUrl, function(data) {
  let imgUrl = data.image_url
  // 이미지를 제시하기 위해 무언가를 한다 
})

// 유저가 고양이를 클릭할 때:
$(".kittyImage").click(function(e) {
  // 우리 컨트랙트의 `feedOnKitty` 메소드를 호출한다 
  ZombieFeeding.feedOnKitty(zombieId, kittyId)
})

// 우리의 컨트랙트에서 발생 가능한 NewZombie 이벤트에 귀를 기울여서 이벤트 발생 시 이벤트를 제시할 수 있도록 한다: 
ZombieFactory.NewZombie(function(error, result) {
  if (error) return
  // 이 함수는 레슨 1에서와 같이 좀비를 제시한다: 
  generateZombie(result.zombieId, result.name, result.dna)
})
```



