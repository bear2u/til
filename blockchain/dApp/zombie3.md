**OpenZepplin의 Ownable 컨트렉트**

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

`modifier `를 함수에 붙여 주면 그 함수를 제어 할 수 있다. 

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

하지만 예외 상황이 있다. 바로 `struct `안에서이다. 

```
struct Normal {
    uint a;
}

struct Mini {
    uint32 a;
}

Normal normal = Normal(10);
Mini mini = Mini(10);

//이 2개는 같지만 가스를 소모하는 게 다르다. 

struct Mini {
    uint32 a;
    uint32 b;
    uint c;
}

struct Mini {
    uint32 a;
    uint c;  
    uint32 b;  
}

//이 2개도 같아 보이지만 옆으로 바로 선언하는게(상위) 가스를 덜 소모한다. 

```

# **시간 단위\(Time uints\)**

솔리디티는 유닉스 타임을 쓰고 있다. `now `를 사용시 현재의 유닉스 타임스탬프를 값을 가지고 올 수 있다. 

솔리디티는 또한 `seconds `, `minutes `, `hours `, `days `, `weeks `, `years`

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



