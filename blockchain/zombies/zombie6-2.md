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

* `send`트랙잭션을 수행하면 `from`해당되는 기능을 호출하는 사람의 주소가 필요하다. \(`msg.sender`\) . 그 후에 MetaMask 가 팝업으로 나와서 submit\(전송확인\) 할 것인지 물어본다. 
* `send`시 거래 비용으로 가스가 든다. 
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

# Payable 함수

```js
// zombieHelper 에 함수를 호출할 수 있다. 
function levelUp(uint _zombieId) external payable {
  require(msg.value == levelUpFee);
  zombies[_zombieId].level++;
}
```

`Wei`란?

1 ether = 10^18 wei 로 계산된다.

그래서 web3에선 편하게 1이더를 wei 로 쉽게 변환해준다.

```
web3js.utils.toWei("1", "ether");
```

levelUpFee 가 0.001 이더로 설정되어있기 때문에 우리는 0.001 이더를 보낼 수 있다.

```
CryptoZombies.methods.levelUp(zombieId)
.send({ from: userAccount, value: web3js.utils.toWei("0.001", "ether") })
```

# 이벤트 구독

`zombieFactory.sol` 에서 새로운 좀비 호출시 `NewZombie`라는 이벤트를 트리거할 수 있다. `web3js` 에서도 이벤트 발생이 가능하다.

```js
cryptoZombies.events.NewZombie()
.on("data", function(event) {
  let zombie = event.returnValues; //이벤트 결과값을 여기로 리턴받음
  console.log("A new zombie was born!", zombie.zombieId, zombie.name, zombie.dna);
}).on("error", console.error);
```

어떤 좀비라도 DAPP 에서 생성이 되면 로그를 보여주게 된다. 현재 유저뿐만 아니라 모든 좀비에 대해서 이벤트가 발생을 하게 된다. 하지만 만약 현재 유저에만 보여주고 싶다면?

# indexed 키워드 사용

이벤트에서 현재 유저에만 이벤트가 가도록 하고 싶다면 indexed 키워드를 지정하면 된다.

ERC721상의 이벤트 중 하나인 전송 부분에서 살펴볼 수 있다.

```
event Transfer(address indexed _from, address indexed _to, uint256 _tokenId);
```

\_from,\_to 가 indexed 키워드가 붙여져 있다.

    // Use `filter` to only fire this code when `_to` equals `userAccount`
    cryptoZombies.events.Transfer({ filter: { _to: userAccount } })
    .on("data", function(event) {
      let data = event.returnValues;
      // The current user just received a zombie!
      // Do something here to update the UI to show it
    }).on("error", console.error);

# 지난 이벤트 조회

`getPastEvents` 를 사용하여 지난 이벤트를 조회도 가능하다. fromBlock, toBlock 로 이벤트 로그들을 살펴볼수 있다.

    cryptoZombies.getPastEvents("NewZombie", { fromBlock: 0, toBlock: "latest" })
    .then(function(events) {
      // `events` is an array of `event` objects that we can iterate, like we did above
      // This code will get us a list of every zombie that was ever created
    });

# Web3.js 이벤트 및 메타 마스크

현재로서는 웹소켓을 이용하여 이벤트를 구독할 수 있다. 최신 1.0 web3.js 내용이다.

아직 메타메스크는 Provider 로써 제공을 못 해주고 있다. 그래서 infura 를 사용하여 만들수 있다.

```
var web3Infura = new Web3(new Web3.providers.WebsocketProvider("wss://mainnet.infura.io/ws"));
var czEvents = new web3Infura.eth.Contract(cryptoZombiesABI, cryptoZombiesAddress);
```



