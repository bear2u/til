# ERC20 토큰을 직접 만들어서 배포까지

우선 truffle 을 설치를 해야한다.

```
npm install -g truffle
```

그리고 코인을 설치할 디렉토리를 만든다.

```
mkdir hamburger-coin
cd hamburger-coin
truffle init
```

그럼 다음과 같은 결과가 나온다.

```js
Downloading...
Unpacking...
Setting up...
Unbox successful. Sweet!

Commands:

  Compile:        truffle compile
  Migrate:        truffle migrate
  Test contracts: truffle test
```

그럼 [OpenZepplin](https://github.com/OpenZeppelin) 프레임워크를 설치해야 한다.

OpenZepplin 프레임워크는 토큰 말고도 미리 만들어진 여러가지의 계약이 포함되어 있다.

```
npm install zeppelin-solidity
```

그럼 이제 토큰을 만들수 있습니다.

우선 솔리디티 스크립트를 만들어보자.

```js
pragma solidity ^0.4.18;
import "zeppelin-solidity/contracts/token/ERC20/StandardToken.sol"; //ERC20 위치를 추가하자.

contract HamburgerCoin is StandardToken {
  string public name = "HamburgerCoin"; 
  string public symbol = "HBC"; //이름
  uint public decimals = 2; //소수점 자리
  uint public INITIAL_SUPPLY = 10000 * (10 ** decimals);

  function HamburgerCoin() public {
    totalSupply = INITIAL_SUPPLY;
    balances[msg.sender] = INITIAL_SUPPLY;
  }
}
```

[StandardToken](https://github.com/OpenZeppelin/zeppelin-solidity/tree/master/contracts/token) 을 상속 받아서 사용한다. 한번 소스를 보는 것도 괜찮은 방법이다.

그리고 2\_deploy\_hambugercoin.js 를 작성한다.

```js
var Every15MinToken = artifacts.require("./Every15MinToken.sol");

module.exports = function(deployer) {
  deployer.deploy(Every15MinToken);
};
```

```
npm install --save-dev dotenv truffle-wallet-provider ethereumjs-wallet //.env 파일에 속성을 추가할 수 있다.
```

실제 배포하는 소스는

```js
require('dotenv').config();
const Web3 = require("web3");
const web3 = new Web3();
const WalletProvider = require("truffle-wallet-provider");
const Wallet = require('ethereumjs-wallet');

var mainNetPrivateKey = new Buffer(process.env["MAINNET_PRIVATE_KEY"], "hex") //private key
var mainNetWallet = Wallet.fromPrivateKey(mainNetPrivateKey);
var mainNetProvider = new WalletProvider(mainNetWallet, "https://mainnet.infura.io/");

var ropstenPrivateKey = new Buffer(process.env["ROPSTEN_PRIVATE_KEY"], "hex")
var ropstenWallet = Wallet.fromPrivateKey(ropstenPrivateKey);
var ropstenProvider = new WalletProvider(ropstenWallet, "https://ropsten.infura.io/");


module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    },
    ropsten: {
      provider: ropstenProvider,
      // You can get the current gasLimit by running
      // truffle deploy --network rinkeby
      // truffle(rinkeby)> web3.eth.getBlock("pending", (error, result) =>
      //   console.log(result.gasLimit))
      gas: 4600000,
      gasPrice: web3.toWei("20", "gwei"),
      network_id: "3",
    },
    mainnet: {
      provider: mainNetProvider,
      gas: 4600000,
      gasPrice: web3.toWei("20", "gwei"),
      network_id: "1",
    }
  }
};
```

`private keysms metamask` 에서 메뉴중에 비밀번호 추출 하기 라는 걸 통해서 얻을 수 있다.

```
ROPSTEN_PRIVATE_KEY="123YourPrivateKeyHere"
MAINNET_PRIVATE_KEY="123YourPrivateKeyHere"
```

이렇게 키를 입력을 해도 되거나 직접 소스에 넣어도 된다.

```js
$ truffle deploy --network ropsten
Compiling ./contracts/HamburgerCoin.sol...
Compiling ./contracts/Migrations.sol...
Compiling zeppelin-solidity/contracts/math/SafeMath.sol...
Compiling zeppelin-solidity/contracts/token/BasicToken.sol...
Compiling zeppelin-solidity/contracts/token/ERC20.sol...
Compiling zeppelin-solidity/contracts/token/ERC20Basic.sol...
Compiling zeppelin-solidity/contracts/token/StandardToken.sol...
Writing artifacts to ./build/contracts

Using network 'ropsten'.

Running migration: 1_initial_migration.js
  Deploying Migrations...
  ... 0xc2bbe6bf5a7c7c7312c43d65de4c18c51c4d620d5bf51481ea530411dcebc499
  Migrations: 0xd827b6f93fcb50631edc4cf8e293159f0c056538
Saving successful migration to network...
  ... 0xe6f92402e6ca0b1d615a310751568219f66b9d78b80a37c6d92ca59af26cf475
Saving artifacts...
Running migration: 2_deploy_contracts.js
  Deploying HamburgerCoin...
  ... 0x02c4d47526772dc524851fc2180b338a6b037500ab298fa2f405f01abdee21c4
  HamburgerCoin: 0x973b1a5c753a2d5d3924dfb66028b975e7ccca51 <-- 여기가 최종 배포된 코인 위치
Saving artifacts..
```

0x973b1a5c753a2d5d3924dfb66028b975e7ccca51 주소를 이더 스캔에서 확인 해본다.

이 코인은 [Mist  ](https://github.com/ethereum/mist)또는 [MyEtherWallet](https://www.myetherwallet.com/)을 통해서 주고 받을 수 있다.

그리고 마지막으로 메인넷으로 배포할려면

```
truffle deploy --network mainnet
```



