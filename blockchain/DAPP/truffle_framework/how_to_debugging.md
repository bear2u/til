원본 : [http://truffleframework.com/tutorials/debugging-a-smart-contract](http://truffleframework.com/tutorials/debugging-a-smart-contract)

# `Truffle `기초 - 계약 디버깅과 테스팅

`Truffle `디버깅 및 테스팅 하는 방법을 알아보자. 

Truffle 은 DAPP 개발을 편하게 해주는 프레임워크이다. 테스팅 및 컴파일, 배포까지 쉽게 해준다. 

테스팅은 [Ganache ](http://truffleframework.com/ganache)를 이용한다. 

일반적은 테스팅시 [http://remix.ethereum.org/](http://remix.ethereum.org/) 에서 쉽게 가능하다. 

그리고 `truffle `에서도 지원한다. 

우선 작업 할 폴더를 구성하자. 

```
mkdif simple-contract
cd simple-contract
```

truffle을 초기화를 해서 기본 구성을 만들 수 있다. 

```
truffle init
```

![](/assets/truffle1_1.png)

기본 구성은 위와 같이 나온다. 

* `build `: 컴파일시 나오는 스마트 계약 빌드 파일이다. `ABI `파일이 나오는 걸로 보인다.
* `contracts `: 스마트 계약 파일들을 여기에서 보관한다. 
* `migrations `: 이미 배폰 계약에 대해서 버전링을 하는 것이다. 자세한 내용은 [여기](https://medium.com/@blockchain101/demystifying-truffle-migrate-21afbcdf3264)를 참고하기 바란다.
* `test `: 계약을 테스팅 할 수 있다. 

truffle.js 등은 서버설정 및 가타 설정이 가능하다. 

초기화를 진행 한 후 스마트 계약\(.sol\) 을 만들어 보자. 

여기에서는 `visual stuio code` 를 사용할 것이다. 

플러그인으로는 `solidity `설치해서 재 시작한다.

```
pragma solidity ^0.4.17;

contract SimpleStorage {
    uint myVariable;

    function set(uint x) public {
        myVariable = x;
    }

    function get() constant public returns (uint) {
        return myVariable;
    }
}
```

두가지 간단한 함수들이 있다. `Set, Get` 등이다. 

이제 `/migrations `폴더에 배포함수를 넣어보자. 

```
var SimpleStorage = artifacts.require("SimpleStorage");

module.exports = function(deployer) {
  deployer.deploy(SimpleStorage);
};
```

그리고 컴파일을 해본다. 

```
> truffle compile
...............
Compiling .\contracts\Store.sol...
Writing artifacts to .\build\contracts
```

컴파일 후에 build 폴더에 계약 관련 파일이 생성되었다. 

실서버에 배포를 하기 전에 제대로 솔리디티에서 함수들의 신뢰성들을 테스팅 할 필요가 있다. Truffle 프레임워크 단에서 편하게 지원해주니 우린 잘 쓰면 된다. 

그럼 `개발 모드`로 들어가본다. 

```
> truffle develop

Connected to existing Truffle Develop session at http://127.0.0.1:9545/
truffle(develop)> //입장
```

개발 모드로 실행 및 세팅된 상태이다. 

그럼 배포를 해보자. 

```
migrate
```









