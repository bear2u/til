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

일반적인 `dApp `에서 자주 쓰는 거이므로 확장해서 사용하는 편이 좋다. 



