# GETH 로 설치하기

로컬 테스트넷에서 Geth 를 가동하기 위해선 두가지를 준비해야함

1. 데이터 디렉터리 \( chaindata \)
2. Genesis.json 파일

윈도우 기반에서 진행한다.

1. 윈도우로 GETH 설치를 진행하자. [https://geth.ethereum.org/downloads/](https://geth.ethereum.org/downloads/)
2. genesis.json 을 만들자. 
3. ```
   {
     "coinbase"   : "0x0000000000000000000000000000000000000001",
     "difficulty" : "0x20000",
     "extraData"  : "",
     "gasLimit"   : "0x8000000",
     "nonce"      : "0x0000000000000042",
     "mixhash"    : "0x0000000000000000000000000000000000000000000000000000000000000000",
     "parentHash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
     "timestamp"  : "0x00",
     "alloc": {},
     "config": {
           "chainId": 15,
           "homesteadBlock": 0,
           "eip155Block": 0,
           "eip158Block": 0
       }
   }
   ```
4. 하위에 chaindata 폴더를 만들어서 저장할 곳을 만들자. 

그리고 geth로 초기화를 진행한다.

```
geth --datadir=chaindata/ init genesis.json

//macos
geth --datadir=./chaindata/ init ./genesis.json
```

그러면 chaindata 디렉토리 아래에는 블록에 대한 정보가 보이며

keystore 아래에는 계정에 관한 정보가 보일것이다.

```
-geth - chinadata, lightchaindata
-keystore
```

그럼 실행해보자.

```
geth --networkid 4649 --nodiscover --maxpeers 0 --datadir chaindata console 2>> chaindata/geth.log
```

> -- networkdid : 네트워크 식별자로 쓰인다. 0~3까지는 예약어이다.
>
> 0 = onlympic, 1= Frontier, 2=morden\(disused\), 3=Ropsten  기본값은 1이다.
>
> -- nodiscover : 생성자의 노드를 다른 노드에서 검색할 수 없게 하는 옵션이다. 노드 추가는 수동으로 해야함,
>
> -- maxpeers 0 : 생성자의 노드에 연결할 수 있는 노드의 수를 지정함, 0은 다른 노드뢍 연결안함
>
> -- datadir 데이터 디렉토리 저정
>
> console 콘솔로 진입한다.
>
> 2&gt;&gt; 데이터 디렉토리/geth.log 로그 파일 만들때 사용하는 옵션 리눅스 셀 명령어

이더리움에서는 계정이 두가지로 나뉜다.

* EOA \(Externally Owned Account\) 일반 사용자가 사용하는 계정이고 Ether를 송금하거나 계약 실행시 필요한 계정
* Contract 계정은 계약을 배포시 만들어지는 나오는 계정으로 블록체인에 존재함

EOA 계정생성 \(콘솔에서\)

```
> personal.newAccount("pass0")
"0x295e3893ed0cd5fb04fbfb4bba656f509ac21aff" //계정 주소
```

계정 정보 확인

```
> eth.accounts
["0x295e3893ed0cd5fb04fbfb4bba656f509ac21aff"]
```

exit를 하면 geth 가 자동 중지가 된다.

```
> exit
```

그럼 채굴을 해보자.

우선 다시 콘솔로 진입해보자.

```
geth --networkid 4649 --nodiscover --maxpeers 0 --datadir chaindata console 2>> chaindata/geth.log
```

```
> eth.coinbase
"0x295e3893ed0cd5fb04fbfb4bba656f509ac21aff"
```

coinbase는 채굴에 성공시 보상을 받는 계정을 조회하는 명령어이다.

만약 다른 계정으로 하고 싶을땐

`miner.setEtherbase` 로 가능하다.

```
> eth.accounts
["0x295e3893ed0cd5fb04fbfb4bba656f509ac21aff", "0x65682c210e9229e5616f1a49f710ce1b4577688b", "0xa817f884ebc1d181dbb3c35c
1cbdeae9b36221c5"]
> eth.coinbase
"0x295e3893ed0cd5fb04fbfb4bba656f509ac21aff"
> miner.setEtherbase(eth.accounts[1])
true // 성공됨
> eth.coinbase
"0x65682c210e9229e5616f1a49f710ce1b4577688b" //2번째 계정으로 지정됨
```

다시 첫번째 계정으로 돌려 놓자.

```
> miner.setEtherbase(eth.accounts[0])
true
```

잔고확인도 해보자.

```
> eth.getBalance(eth.accounts[0])
0
> eth.getBalance(eth.accounts[1])
0
> eth.getBalance(eth.accounts[2])
0
```

블록 수 확인도 가능하다. 아직 블록을 생성하지 않았으므로 0 이다.

```
> eth.blockNumber
0
```

그러면 채굴을 시작해보자.

`miner.start(threadnum)` 로 명령어 사용가능하다. 여기에서 `thread_num`은 채굴시 사용하는 쓰레드 수이다. 우선 1로 설정

```
> miner.start(1)
null or true
> miner.mining //마이닝 되고 있는 지 체크
true
> eth.hashreate
1086190 or 0
> eth.blockNumber
61
```

그리고 miner.stop 을 해보자.

```
> miner.stop()
//계좌 확인
> eth.getBalance(eth.coinbase)
125000000000000000000
> eth.getBalance(eth.accounts[1]) //현재 1로 설정되어 있는 상태입니다.
125000000000000000000
> eth.getBalance(eth.accounts[0])
95000000000000000000
```

0이 엄청 많아 보이지만 최소 단위인 wei 로 보여서 그렇다. 10^18승이 1이더이다. 그럼 이더로 변환하면

```
> web3.fromWei(eth.getBalance(eth.accounts[1]),"ether")
125 // 이더로 변환
```

다시 한번 블록 갯수 체크해본다.

```
> eth.blockNumber
44
```

블록당 보상금액이 5ether이다. 그래서 계정 2개 보상 금액 합치면 220 \( 125 + 95 \) 이더이다. 즉 220 = 5 \* 44 로 맞아떨어진다.

이렇게 보상금액을 계산을 가능하다.

