# Call & Send

자 이제 모든 준비는 다 된 상태이다. 그럼 함수들을 호출을 해보자. 함수 호출시 `call`과 `send`를  사용한다.

> `Call`: View, Pure 함수로 주로 사용된다. 이건 로컬 노드에서 실행되기 때문에 블록체인에서 transaction 을 실행하지 않는다.

View,Pure 함수는 가스소모가 없다.

```js
myContract.methods.myMethod(123).call()
```

* `send`: 트랙잭션이 발생되며 블록체인내에서 변화가 이루어진다. View,Pure 함수를 제외하고 함수들이 send로 불리어진다. 

`send`함수를 실행시 가스가 소모되며 만약 메타메스크를 실행한 상태라면 그 메타메스크가 열리면서 함수를 실행을 할 수 있게 된다. `transaction`에는 submit 도 가능하다.

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

    getZombieDetails(id)
    .then(function(zombie) {
      // Using ES6's "template literals" to inject variables into the HTML.
      // Append each one to our #zombies div
      $("#zombies").append(`<div class="zombie">
        <ul>
          <li>Name: ${zombie.name}</li>
          <li>DNA: ${zombie.dna}</li>
          <li>Level: ${zombie.level}</li>
          <li>Wins: ${zombie.winCount}</li>
          <li>Losses: ${zombie.lossCount}</li>
          <li>Ready Time: ${zombie.readyTime}</li>
        </ul>
      </div>`);
    });

# Send 함수

* `send `트랙잭션을 수행하면 `from `해당되는 기능을 호출하는 사람의 주소가 필요하다. \(`msg.sender`\) . 그 후에 MetaMask 가 팝업으로 나와서 submit\(전송확인\) 할 것인지 물어본다. 
* `send `시 거래 비용으로 가스가 든다. 
* `send`를 하는 순간 블록에 노드를 업데이트 하므로 최소 `15이상의 시간`이 걸릴 수 있으므로 그에 대한 UI 를 고려 해야 할 것이다. 즉 비동기 함수가 필요할 것이다. 

```js
... Solidity 코드
function createRandomZombie(string _name) public {
  require(ownerZombieCount[msg.sender] == 0);
  uint randDna = _generateRandomDna(_name);
  randDna = randDna - randDna % 100;
  _createZombie(_name, randDna);
}

.... 비동기로 처리 되는 부분을 눈여겨 봐야 한다.
.... Web3.js 코드
function createRandomZombie(name) {
  // This is going to take a while, so update the UI to let the user know
  // the transaction has been sent
  $("#txStatus").text("Creating new zombie on the blockchain. This may take a while...");
  // Send the tx to our contract:
  return CryptoZombies.methods.createRandomZombie(name)
  .send({ from: userAccount })
  .on("receipt", function(receipt) {
    $("#txStatus").text("Successfully created " + name + "!");
    // Transaction was accepted into the blockchain, let's redraw the UI
    getZombiesByOwner(userAccount).then(displayZombies);
  })
  .on("error", function(error) {
    // Do something to alert the user their transaction has failed
    $("#txStatus").text(error);
  });
}
```

여기에서 이벤트 리스너인 receipt 와 error 부분이 나온다. 

* receipt : 트랙잭션이 이더리움의 블록에 포함되면 트리거된다. 좀비가 계약서에 저장되었음을 의미한다. 
* error : 가스가 불충분하거나 거래가 블록에 업데이트가 되지 않을 경우 발동된다. 그럼 여기에선 오류 발생됨을 알려야 할것이다. 

> 그리고 전송시 가스를 코드에서 지정하거나 `MetaMask` .에서 지정하는 방법이 있을 수 있다. 
>
> .send\({ from: userAccount, gas: 3000000 }\) // 이렇게 코드에서 지정하거나 MetaMask 로 사용자가 가스비를 설정하게끔 할수 있다.







