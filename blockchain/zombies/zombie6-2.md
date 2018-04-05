# Call & Send

자 이제 모든 준비는 다 된 상태이다. 그럼 함수들을 호출을 해보자. 함수 호출시 `call`과 `send`를  사용한다.

> **`Call `**: View, Pure 함수로 주로 사용된다. 이건 로컬 노드에서 실행되기 때문에 블록체인에서 transaction 을 실행하지 않는다.

View,Pure 함수는 가스소모가 없다. 

```js
myContract.methods.myMethod(123).call()
```

* **`send `**: 트랙잭션이 발생되며 블록체인내에서 변화가 이루어진다. View,Pure 함수를 제외하고 함수들이 send로 불리어진다. 

`send `함수를 실행시 가스가 소모되며 만약 메타메스크를 실행한 상태라면 그 메타메스크가 열리면서 함수를 실행을 할 수 있게 된다. `transaction `에는 submit 도 가능하다. 

```js
myContract.methods.myMethod(123).send()
```

# 유저 계정 정보를 획득하기

MetaMask 를 통해 Web3 은 유저정보를 실시간으로 받아올 수 있다. 

```js
var userAccount = web3.eth.accounts[0]
```

만약 유저 계정을 바뀌는 경우라면 어떻게 되는 것일까.. 크롬에서 metamask를 켜고 유저정보를 실시간으로 변경시 그 정보를 업데이트를 해줘야 한다. 

그걸 하기 위해선 매초마다 체크를 해서 업데이트를 해주면 된다. 

```js
var accountInterval = setInterval(function() {
    //변경이 된 경우라면
    if(web3.eth.accounts[0] !== userAccount) {
        userAccount = web3.eth.accounts[0];
        updateInterface();
    }
}, 100);
```



