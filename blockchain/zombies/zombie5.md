이더리움에는 토큰이 존재한다.

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



