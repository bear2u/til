# Web3.js

이더리움 네트워크내에서 통신을 하기위해선 여러가지 방법이 있지만 주로 JSON-RPC를 통한다. 하지만 형태는 직접 보기가 불편한 점이 있다. 그래서 그걸 인터페이스로 쉽게 구현하는 라이버러리가 있다. Web3.js 를 뜻한다.

`JSON-RPC` 형태는 다음과 같다.

```js
{"jsonrpc":"2.0","method":"eth_sendTransaction","params":[{"from":"0xb60e8dd61c5d32be8058bb8eb970870f07233155","to":"0xd46e8dd67c5d32be8058bb8eb970870f07244567","gas":"0x76c0","gasPrice":"0x9184e72a000","value":"0x9184e72a","data":"0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"}],"id":1}
```

하지만 Web3.js 에서 계약 내 함수 호출시 다음과 같은 형태로 가능하다.

1. 좀비를 만들고
2. send라는 트랙잭션이 발생된다.

```
CryptoZombies.methods.createRandomZombie("Vitalik Nakamoto 🤔")
  .send({ from: "0xb60e8dd61c5d32be8058bb8eb970870f07233155", gas: "3000000" })
```

Web3 라이버러리 주입

web3 설치시 여러가지 방법이 있을 수 있다.

```
// Using NPM
npm install web3

// Using Yarn
yarn add web3

// Using Bower
bower install web3
```

추가적으로 스크립트로 가져오는 방법인

```
<script language="javascript" type="text/javascript" src="web3.min.js"></script>
```

# Web3 Provider

Web3 Provider 은 이더리움 서버를 어떤 걸로 할지 설정해준다. 자신의 네트워크로 지정해도 상관은 없다. 그리고 메인이나 테스트넷등에 접속시 쉽게 하기 위해서 [Infura](https://infura.io/) 라는 서비스도 제공되어 진다.

Infura 를 공급자\(Provider\)로 사용하는 경우 자신의 노드를 설정하고 유지할 필요없이 Ethereum 블록 체인으로 메세지를 보내고 받을 수 있다.

```
var web3 = new Web3(new Web3.providers.WebsocketProvider("wss://mainnet.infura.io/ws"));
```

DAPP의 경우 읽는 것 뿐아니라 블록체인에 Write 할 것이다. 그럼 이 사용자들이 개인 키로 트랙잭션에 서명 할 수 있는 방법이 필요하다.

그렬경우 대표적으로 인기가 많은 것은 `MetaMask`이다.

하지만 만약 Metamask 를 설치를 하지 않을 경우에도 오류 메세지를 보여주는 편이 좋다.

```js
window.addEventListener('load', function() {

  // Checking if Web3 has been injected by the browser (Mist/MetaMask)
  if (typeof web3 !== 'undefined') {
    // Use Mist/MetaMask's provider
    web3js = new Web3(web3.currentProvider);
  } else {
    // Handle the case where the user doesn't have web3. Probably 
    // show them a message telling them to install Metamask in 
    // order to use our app.
  }

  // Now you can start your app & access web3js freely:
  startApp()

})
```

Web3.js 은 두가지가 필수로 주어져야 한다.

* address : 추후 스마트 계약을 배포 한 후에 주어지는 주소를 뜻한다
* ABI : Application Binary Interface 를 줄인 말이며 함수들과 기타 정보들을 JSON으로 모아놓은 형태를 말한다. 배포시 얻을 수 있다. 

```
// Instantiate myContract
var myContract = new web3js.eth.Contract(myABI, myContractAddress);
```

# 스마트 계약 연동

> **Web3.js 는 작성한 스마트 계약과의 연동시 **`주소`**와 **`ABI`** 가 필요합니다.**

## Address

스마트 계약 작성후 그 계약을 컴파일 한 후 이더리움에 배포를 하는 과정을 거칩니다. 배포를 하게 되면 이더리움 해당 되는 넷에 그 계약이 머물고 있는 주소를 얻을수 있다.

## ABI \(Application Binary Interface 응용 프로그램 바이너리 인터페이스\)

배포 전 컴파일하게 되면 web3.js 가 이해할 수 있는 JSON 향태의 파일을 제공해준다. 그럼 이 걸 주소와 같이 저장해서 사용을 하면 된다.

이 2개가 준비되었다고 가정하면 Web3 에서는 인스턴스화 할 수 있다.

```js
var myContract = new Web3js.eth.Contract(myABI, myContractAddress);
```



