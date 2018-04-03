6-1 Web3.js 에 대한 소개

Web3.js 가 필요한 이유는 이더리움 네트워크가 노드를 만들때 스마트 계약 함수를 실행을 할것이다. 그때 필요한 내용은 다음과 같다. 

* 스마트 계약의 주소
* 당신이 호출한 함수
* 당신이 함수에게 전달하는 변수들

이더리움 노도는 JSON-RPC 를 통해서 통신하지만 이건 읽기가 쉽지가 않음

형태는 다음과 같다. 

```
{"jsonrpc":"2.0","method":"eth_sendTransaction","params":[{"from":"0xb60e8dd61c5d32be8058bb8eb970870f07233155","to":"0xd46e8dd67c5d32be8058bb8eb970870f07244567","gas":"0x76c0","gasPrice":"0x9184e72a000","value":"0x9184e72a","data":"0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"}],"id":1}
```

Web3.js 는 이러한 불편함을 백단에서 처리해주고 프론트에선 편하게 인터페이스들을 제공해주고 있다. 

함수 실행 방법은 

```
CryptoZombies.methods.createRandomZombie("Vitalik Nakamoto 🤔")
  .send({ from: "0xb60e8dd61c5d32be8058bb8eb970870f07233155", gas: "3000000" })
```

설치방법은 여러방법이 있다. 

```
// Using NPM
npm install web3

// Using Yarn
yarn add web3

// Using Bower
bower install web3

//Or
<script language="javascript" type="text/javascript" src="web3.min.js"></script>
```



