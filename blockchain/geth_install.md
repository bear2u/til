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





