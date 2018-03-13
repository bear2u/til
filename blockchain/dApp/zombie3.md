# 챕터3에서 배우는 

CryptoKitties 컨트랙트를 변경하는 한 가지 방법을 배웠네

* `onlyOwner`
  를 이용해 핵심적인 함수를 보호하는 방법을 배웠네
* 가스와 가스 사용 최적화에 대해 배웠네
* 우리 좀비들에게 레벨과 대기시간 개념을 적용했네
* 좀비가 특정 레벨이 되면 좀비의 이름과 DNA를 재설정할 수 있는 함수를 만들었네
* 마지막으로, 사용자의 좀비 군대를 반환하는 함수를 만들었네.

# **OpenZepplin의 Ownable 컨트렉트**

계약에 대해 `소유권을`적용할 수 있는 `라이버러리이다`.

소스를 잠깐 살펴보면 다음과 같다.

```js
pragma solidity ^0.4.17;

contract Ownable {
    address public owner;
    event OwnerShipTransferred(address indexed previousOwner, address indexed newOwner);

    function Ownable() public {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner);
        _;
    }

    function transferOwnership(address newOwner) public onlyOwner {
        require(newOwner != address(0));
        OwnerShipTransferred(owner, newOwner);
        owner = newOwner;
    }
}
```

위 코드엔 생성자 , 함수 제어자 \(modify onlyOwner\(\)\)\( require를 확장해서 사용가능하다. \)

\_; 는 owner인걸 확인 한 후에 그 다음 코드를 포함한다는 뜻\(실행함\)

즉 위 코드는 Ownable 컨트렉트 생성시 msg.sender \(컨트렉트를 배포한 사람\) 을 대입한다.

그리고 onlyOwner 라는 접근 제어자가 붙은 경우 사용할 수 있게끔 접근 제어자를 붙인다.

그리고 새로운 소유자에게 이동할수 있게끔 하는 함수도 있다. \( `transferOwnership`\)

일반적인 `dApp`에서 자주 쓰는 것이므로 확장해서 사용하는 편이 좋다.

**함수 제어자**

전에 require를 통해서 제어를 했었다.

이젠 함수 자체를 제어하는 방법을 배워보자.

`modifier`를 함수에 붙여 주면 그 함수를 제어 할 수 있다.

```js
.. Ownable 계약서에 있는
modifier onlyOwner() {
    require(msg.sender == owner);
    _
}

contract MyContract is Ownable {
    event eat(string food);

    function eatBread() external onlyOwner {
        eat("Bread");
    }
}
```

## 가스 - 이더리움 DApp이 사용하는 연료 {#-dapp-}

솔리디티에서는 사용자들이 자네가 만든 DApp의 함수를 실행할 때마다_**가스**_라고 불리는 화폐를 지불해야 하네. 사용자는 이더\(ETH, 이더리움의 화폐\)를 이용해서 가스를 사기 때문에, 자네의 DApp 함수를 실행하려면 사용자들은 ETH를 소모해야만 하네.

함수를 실행하는 데에 얼마나 많은 가스가 필요한지는 그 함수의 로직\(논리 구조\)이 얼마나 복잡한지에 따라 달라지네. 각각의 연산은 소모되는_**가스 비용\(gas cost\)**_이 있고, 그 연산을 수행하는 데에 소모되는 컴퓨팅 자원의 양이 이 비용을 결정하네. 예를 들어, storage에 값을 쓰는 것은 두 개의 정수를 더하는 것보다 훨씬 비용이 높네. 자네 함수의 전체_**가스 비용**_은 그 함수를 구성하는 개별 연산들의 가스 비용을 모두 합친 것과 같네.

함수를 실행하는 것은 자네의 사용자들에게 실제 돈을 쓰게 하기 때문에, 이더리움에서 코드 최적화는 다른 프로그래밍 언더들에 비해 훨씬 더 중요하네. 만약 자네의 코드가 엉망이라면, 사용자들은 자네의 함수를 실행하기 위해 일종의 할증료를 더 내야 할 걸세. 그리고 수천 명의 사용자가 이런 불필요한 비용을 낸다면 할증료가 수십 억 원까지 쌓일 수 있지.

## 가스는 왜 필요한가? {#-}

이더리움은 크고 느린, 하지만 굉장히 안전한 컴퓨터와 같다고 할 수 있네. 자네가 어떤 함수를 실행할 때, 네트워크상의 모든 개별 노드가 함수의 출력값을 검증하기 위해 그 함수를 실행해야 하지. 모든 함수의 실행을 검증하는 수천 개의 노드가 바로 이더리움을 분산화하고, 데이터를 보존하며 누군가 검열할 수 없도록 하는 요소이지.

이더리움을 만든 사람들은 누군가가 무한 반복문을 써서 네트워크를 방해하거나, 자원 소모가 큰 연산을 써서 네트워크 자원을 모두 사용하지 못하도록 만들길 원했다네. 그래서 그들은 연산 처리에 비용이 들도록 만들었고, 사용자들은 저장 공간 뿐만 아니라 연산 사용 시간에 따라서도 비용을 지불해야 한다네.

> 참고: 사이드체인에서는 반드시 이렇지는 않다네. 크립토좀비를 만든 사람들이 Loom Network에서 만들고 있는 것들이 좋은 예시가 되겠군. 이더리움 메인넷에서 월드 오브 워크래프트 같은 게임을 직접적으로 돌리는 것은 절대 말이 되지 않을 걸세. 가스 비용이 엄청나게 높을 것이기 때문이지. 하지만 다른 합의 알고리즘을 가진 사이드체인에서는 가능할 수 있지. 우린 다음에 나올 레슨에서 DApp을 사이드체인에 올릴지, 이더리움 메인넷에 올릴지 판단하는 방법들에 대해 더 얘기할 걸세.

# uInt 팁

솔리디티 안에서 uint8, uint16, uint32 등의 타입을 지정하는 건 별로 의미가 없다. 이유는 생성때부터 256비트의 저장공간을 잡아놓기 때문이다.  즉 `uint256 == uint8` 과 같은 공간을 쓴다는 뜻이다.

하지만 예외 상황이 있다. 바로 `struct`안에서이다.

struct Normal {

```
uint a;
```

}

struct Mini {

```
uint32 a;
```

}

Normal normal = Normal\(10\);

Mini mini = Mini\(10\);

//이 2개는 같지만 가스를 소모하는 게 다르다.

struct Mini {

```
uint32 a;

uint32 b;

uint c;
```

}

struct Mini {

```
uint32 a;

uint c;  

uint32 b;  
```

}

//이 2개도 같아 보이지만 옆으로 바로 선언하는게\(상위\) 가스를 덜 소모한다.

# **시간 단위\(Time uints\)**

솔리디티는 유닉스 타임을 쓰고 있다. `now`를 사용시 현재의 유닉스 타임스탬프를 값을 가지고 올 수 있다.

솔리디티는 또한 `seconds`, `minutes`, `hours`, `days`, `weeks`, `years`

을 제공하고 있다.

```
1 minutes = 60
1 hours = 3600 (60 * 60)
1 days = 86400 ( 24 * 1 hours)
```

그럼 사용예제를 보자.

```
uint lastUpdated;

function updateTimestamp() public {
    lastUpdated = now //now로 설정
}
//5분이 지난 경우 true , 아닌 경우 false
function fiveMinutesHavePassed() public view returns(bool) {
    return (now >= (lastUpdated + 5 minutes));
}
```

# 캐스팅

uint 256 를 uint32 으로 캐스팅시

```
uint32(uint256 변수) //이런식으로 캐스팅함
```

# 구조체를 인수로 전달하기

storage 구조체를 주고 받을 수 있다.

```js
function _doStuff(Zombie storage _zombie) internal {
    // _zombie 처리
}
```

# Public 함수 보안

함수를 public 으로 지정시 외부에서 인터페이스로 해당 함수를 호출할 수 있다는 걸 유의해야 한다.

그래서 위에 언급했던 `onlyOwner`를 사용을 할 수 있고 그 외 `private`또는 `internal`로 만들 수 있다.

# 인수를 가지는 함수 제어자

```
// 사용자의 나이를 저장하는 매핑
mapping ( uint => uint ) public age;

//사용자의 특정 나이 이상인지 확인 하는 제어자
modifier olderThan( uint _age, uint _userId ) {
    require(age[_userId]) >= _age);
    _;
}

function buyAlchoDrinks(uint _userId) public orderThan(16, _userId) {
    //필요한 함수 내용들
}
```

# View 

함수가 데이터를 쓰고,수정,삭제를 하지 않고 오직 읽기만 할 경우 사용될 수 있다. 

View 함수는 사용자에 의해서 외부에서 호출 시 가스를 전혀 소모하지 않는다. 

즉 로컬 노드에 데이터만 조회하고 따로 어떠한 트렉젝션이 필요없다는 뜻이다. 

```
external view //오직 읽기에만 사용가능하다. 
```

# Storage는 비싸다.

데이터의 일부를 쓰거나 바꿀때마다 블록체인에 영구적으로 기록되기 때문에 가스비가 들수 밖에 없다. 

비용이 드는 문제이다. 

그래서 비용을 최소화 하기 위해 필요한 경우가 아니면 storage에 데이터를 쓰지 않는 편이 좋다. 

# 메모리에 배열 선언하기

Storage 에 쓰지 않고 배열을 만들려면 memory 키워드를 이용하면 된다. 

Storage 보다 휠씬 가스비가 절약할 수 있다. 외부에서 호출하는 View 함수라는 무료이다. 

```
function getArray() external pure returns(uint[]){
    //배열의 크기는 항상 주어져야 한다. 이후에는 변경될 수 있을것이다. 
    uint[] memory values = new uint[](3);
        
    values.push(1);
    values.push(2);
    values.push(3);
    
    return values;
}
```

# For 반복문 사용하기

솔리디티에서 for문은 자바스크립트와 유사하다. 

```js
function getEvens() pure external returns(uint[]) {
    uint[] memory evens = new uint[](5);
    
    uint counter = 0;
    
    for(uint i = 0; i <= 10; i++ ){
        if(i % 2 == 0){
            evens[counter] = i;
            counter++;
        }
    }
    return evens;    
}
```





