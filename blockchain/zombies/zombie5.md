# 5장에서 배울 내용

* 토큰, ERC721 표준 및 거래 가능한 자산 / 좀비

* 라이브러리 및 사용 방법

* SafeMath 라이브러리를 사용하여 오버플로 및 언더 플로우를 방지하는 방법

* 코드 및 natspec 표준 주석

#  토큰

기본적으로 ERC20 토큰이 있어서 이걸 거래가 가능하다. 이더리움 토큰을 생각해보면 된다.

하지만 게임상에서 게임 수집물에 대한 토큰으로 표기하기에는 한계가 있다..

좀비하나마다 아이디를 부여해야 하기 때문이다.

그래서 ERC721 토큰을 제공한다.

> 참고 : ERC721 같은 표준을 사용하는 장점은 경매와 플레이어가 좀비를 거래 / 판매하는 방식을 결정하는 에스크로 로직을 계약에서 구현할 필요가 없어진다는 점입니다.
>
> 사양을 준수하면 다른 사람이 수집 가능한 ERC721 스크립트 자산의 교환 플랫폼을 만들 수 우리의 ERC721 좀비는 그 플랫폼에서 사용할 수 있습니다.
>
> 따라서 자신의 거래 로직을 전개하는 대신 토큰 규격을 사용하는 것은 분명히 장점이 있습니다.

# ERC 721 토큰 규격

```
contract ERC721 {
  event Transfer(address indexed _from, address indexed _to, uint256 _tokenId);
  event Approval(address indexed _owner, address indexed _approved, uint256 _tokenId);

  function balanceOf(address _owner) public view returns (uint256 _balance);
  function ownerOf(uint256 _tokenId) public view returns (address _owner);
  function transfer(address _to, uint256 _tokenId) public;
  function approve(address _to, uint256 _tokenId) public;
  function takeOwnership(uint256 _tokenId) public;
}
```

토큰 계약 구현시 먼저 할껀 Import 를 해서 가져오는 것이다.

```
import "./erc721.sol"

//참고로 계약은 다중상속을 허용하고 있다. 
contrat A is B,C {
}
```

```
function balanceOf(address _owner) public view returns (uint256 _balance);
//Address 입력시 그 계정에 balance가 얼마나 있는지 조회할 수 있다.
```

```
function ownerOf(uint256 _tokenId) public view returns (address _owner);
//해당 토큰 아이디에 address를 리턴해준다.
```

# 오버플로우

연산시 만약 uinit8 을 지정한 상태에서 8비트만 가질수 있다. 즉 최대수는 `2^8 -1 = 255` 이다. 

하지만 만약 

```
uint number = 255;
number++; //이 경우 오버플로우 발생함
```

# 언더플로우

오버플로우 반대로 **음수**로 가는 경우 발생될 수 있다. 

# SafeMath

이를 방지하기 위해 `OpenZepplin `은 `SafeMath `라는 라이버러리를 제공한다. 

기본적으로 함수는 4개의 기능을 제공한다. 

`add, sub, mul, div`

```js
using SafeMath for uint256; //라이버러리를 사용하겠다고 정의

uint256 a = 5;
uint256 b = a.add(3);
uint256 c = a.mul(2);
```

내부 소스를 들어다 보면

```js
library SafeMath {

  function mul(uint256 a, uint256 b) internal pure returns (uint256) {
    if (a == 0) {
      return 0;
    }
    uint256 c = a * b;
    assert(c / a == b);
    return c;
  }

  function div(uint256 a, uint256 b) internal pure returns (uint256) {
    // assert(b > 0); // Solidity automatically throws when dividing by 0
    uint256 c = a / b;
    // assert(a == b * c + a % b); // There is no case in which this doesn't hold
    return c;
  }

  function sub(uint256 a, uint256 b) internal pure returns (uint256) {
    assert(b <= a);
    return a - b;
  }

  function add(uint256 a, uint256 b) internal pure returns (uint256) {
    uint256 c = a + b;
    assert(c >= a);
    return c;
  }
}
```

**library** 라는 키워드 확인해볼 필요있다. 

library 타입으로 지정시 다른 타입을 앞으로 끌어들일 수 있다. 

```js
using SafeMath for uint;
uint test = 2;
test = test.mul(3); //인자가 2개가 필요하지만 자동으로 앞 변수(test)를 입력받게 된다.. 
```

# 사용법

```
//num++ 이 방법을 쓰지 말고
num = num.add(1); //이렇게 대체하면 됩니다.
```

# Assert

사용법은 **require **와 비슷하다. 값이 false 일때 반환한다. 

그러면 require 와 차이점은 무엇일까. 

**require **실패시 가스비가 반환된다. 하지만 **assert **사용시 가스비는 반환되지 않고 false 로 끝난다. 

그래서 극단적인 오류의 경우에만 사용해야 한다. 예를 들어 오버플로우나 언더 플로우등..

# uint16 , uint32 상의 오버/언더 플로 해결방법

기본적으로 SafeMath 타입은 uint256 이다.  그래서 추가적으로 2개의 라이버러리를 더 가져와야 한다. 

# `SafeMath16, SafeMath32`

내부적으로 같지만 타입만 변경되었다. 

# 주석

솔리디티에서 주석은 자바스크립트 과 동일하다. 

주석은 _**natspec 을 따른다. **_

```
/// @title A contract for basic math operations
/// @author H4XF13LD MORRIS 💯💯😎💯💯
/// @notice For now, this contract just adds a multiply function
contract Math {
  /// @notice Multiplies 2 numbers together
  /// @param x the first uint.
  /// @param y the second uint.
  /// @return z the product of (x * y)
  /// @dev This function does not currently check for overflows
  function multiply(uint x, uint y) returns (uint z) {
    // This is just a normal comment, and won't get picked up by natspec
    z = x * y;
  }
}
```

@notice 는 이 함수가 하는 일을 설명한다. 

@dev 는 추가적으로 개발자에게 할 말을 적는다. 

