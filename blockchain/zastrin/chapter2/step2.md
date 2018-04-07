# Truffle 로 개발시작하기

Geth 를 실행한 상태에서 트러플을 설치를 해보자. 트러플관련해선 [**여기**](http://truffleframework.com/)를 참조하자.

```
# truffle 라이버러리 설치
npm install -g truffle
```

```
mkdir -p ethereum_voting_dapp/chapter2
cd ethereum_voting_dapp/chapter2
npm install -g webpack
truffle unbox webpack
ls
README.md       contracts       node_modules      test          webpack.config.js   truffle.js
app          migrations       package.json
ls app/
index.html javascripts stylesheets
ls contracts/
ConvertLib.sol MetaCoin.sol Migrations.sol
ls migrations/
1_initial_migration.js 2_deploy_contracts.js
rm contracts/ConvertLib.sol contracts/MetaCoin.sol
```

`contracts/ConvertLib.sol contracts/MetaCoin.sol` 은 샘플로 나오는 부분인데 필요없으니 삭제를 하자.

그리고 `truffle.js` 파일을 찾아서 `ganache`에 해당되는 부분에서 `7545 -> 8545` 로 변경하자.

```js
// Allows us to use ES6 in our migrations and tests.
require('babel-register')

module.exports = {
  networks: {
    development: {
      host: '127.0.0.1',
      port: 8545, // 7545 -> 8545로 변경
      network_id: '*' // Match any network id
    }
  }
}
```

# Migration 디렉토리

Migration 디렉토리의 내용을 이해하는 것은 중요하다. 배포시 자주 쓰이는 파일이기 때문이다. 트러플이 배포와 배포과정 추적을 모두 담당해주기 때문이다.

첫 번째 마이그레이션 파일인

`1_initial_migration.js`는 Migrations라는 이름의 컨트랙트를 블록체인에 배포하고 가장 최근에 배포한 컨트랙트를 저장합니다. migration을 실행할 때마다 트러플은 블록체인에 가장 최근에 배포된 컨트랙트 정보를 확인하고 아직 배포하지 않은 컨트랙트를 배포합니다. 그 다음으로 Migrations 컨트랙트의 last\_completed\_migration 필더를 업데이트하여 가장 최근에 배포한 컨트랙트 정보를 갱신합니다. 간단하게 요약하면, 이는 Migration이라는 데이터베이스 표의`last_completed_migration`라는 열을 업데이트하는 동작과 같습니다. 더 자세한 정보는 [트러플 문서 페이지](http://truffleframework.com/docs/getting_started/migrations)를 참조하기 바란다.

그럼 파일을 업데이트를 해보자.

`2_deploy_contracts.js` 파일 내용을 업데이트 하면 된다.

```
var Voting = artifacts.require("./Voting.sol");
module.exports = function(deployer) {
 deployer.deploy(Voting, ['Rama', 'Nick', 'Jose'], {gas: 290000});
};
```

첫번째인자는 계약인스턴스, 그리고 투표 후보자 배열, 코드를 배포하는데 필요한 가스를 지정한다. 가스는 계약의 규모에 따라 다르지만 여기에서는 490000 이면 충분해보인다.

그리고 `truffle.js` 파일 내용을 업데이트 하자.

```
require('babel-register')

module.exports = {
 networks: {
  development: {
   host: 'localhost',
   port: 8545,
   network_id: '*',
   gas: 470000
  }
 }
}
```

이제 투표하는 스마트 계약을 만들어보자. 

구조는 다음과 같다. 

1. 후보의 배열을 초기화하는 생성자.
2. 투표에 참여하는 메소드 \(총 투표 수를 증가시킴\)
3. 후보자가 받은 총 투표 수를 반환하는 메소드

> Voting.sol

```js
pragma solidity ^0.4.18;

contract Voting {

  mapping (bytes32 => uint8) public votesReceived;
  bytes32[] public candidateList;

  function Voting(bytes32[] candidateNames) public {
    candidateList = candidateNames;
  }

  function totalVotesFor(bytes32 candidate) view public returns (uint8) {
    require(validCandidate(candidate));
    return votesReceived[candidate];
  }

  function voteForCandidate(bytes32 candidate) public {
    require(validCandidate(candidate));
    votesReceived[candidate]  += 1;
  }

  function validCandidate(bytes32 candidate) view public returns (bool) {
    for(uint i = 0; i < candidateList.length; i++) {
      if (candidateList[i] == candidate) {
        return true;
      }
    }
    return false;
   }
}
```

이 `Voting.sol` 을 `contracts` 폴더로 만들어서 넣자. 

app/index.html 에 소스를 수정하자. 

```js
<!DOCTYPE html>
<html>
<head>
 <title>Hello World DApp</title>
 <link href='https://fonts.googleapis.com/css?family=Open+Sans:400,700' rel='stylesheet' type='text/css'>
 <link href='https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css' rel='stylesheet' type='text/css'>
</head>
<body class="container">
 <h1>A Simple Hello World Voting Application</h1>
 <div id="address"></div>
 <div class="table-responsive">
  <table class="table table-bordered">
   <thead>
    <tr>
     <th>Candidate</th>
     <th>Votes</th>
    </tr>
   </thead>
   <tbody>
    <tr>
     <td>Rama</td>
     <td id="candidate-1"></td>
    </tr>
    <tr>
     <td>Nick</td>
     <td id="candidate-2"></td>
    </tr>
    <tr>
     <td>Jose</td>
     <td id="candidate-3"></td>
    </tr>
   </tbody>
  </table>
  <div id="msg"></div>
 </div>
 <input type="text" id="candidate" />
 <a href="#" onclick="voteForCandidate()" class="btn btn-primary">Vote</a>
</body>
<script src="https://code.jquery.com/jquery-3.1.1.slim.min.js"></script>
<script src="app.js"></script>
</html>
```



