# 토큰 기반 이더리움 투표 개발

가짜 블록체인 위에 간단한 투표 스마트 계약을 구현하고 node.js 를 통해 웹페이지에서 성공적으로 상호작용을 하는 것을 공부해보자.

애로 사황이 발생될 경우 [**community.zastrin.com**](https://community.zastrin.com/) 에서 도움받을 수 있다.

이번강좌에서 진행상황은 다음과 같다.

* Geth 설치 - Geth는 블록체인을 내려받고 이더리움 노드를 실행하기 위한 클라이언트 소프트웨어로, 퍼블릭 이더리움 네트워크와의 연결 가교입니다.

* 스마트 계약의 컴파일과 배포에 쓰이는 이더리움 dapp 프레임워크, '트러플\(Truffle\)'을 설치합니다.

* 투표 애플리케이션을 업데이트하고 트러플을 통해서 사용 가능하게 합니다.
* Ropsten 테스트넷용으로 스마트 계약을 컴파일하고 배포합니다\(Ropsten 및 기타 네트워크에 대해서는 뒤에서 다룹니다\).
* 트러플 콘솔 및 웹페이지로 스마트 계약과 상호작용합니다.
* 트러플의 사용에 익숙해지만, 스마트 계약에 토큰 기능과 토큰을 통한 구매 기능을 추가할 것입니다.
* 프론트엔드를 업데이트하여 토큰 구입 및 토큰을 통한 투표를 구현할 것입니다.

![](/assets/zastrin_ch2_1.png)

그럼 우선 `Geth`를 설치를 해보자.

Geth 는 도커를 이용해서 우분투 16.04 버전에서 진행된다.

도커 사용이 처음이라 익숙하지 않지만 도전해본다.

일단 [도커](https://www.docker.com/) 설치를 진행하자.

```
$ docker run --rm(옵션) -it ubuntu:16.04 /bin/bash
# --rm 은 종료시 삭제가 된다. 
# -it 은 시작한다. 

$ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  us

# sudo 설치
$ apt-get update && apt-get install -y sudo && rm -rf /var/lib/apt/lists/* 
# sudo 가 설치가 안되어 있어서 해주면 편하다. 

$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository -y ppa:ethereum/ethereum
$ sudo apt-get update
$ sudo apt-get install ethereum
$ geth --testnet --syncmode fast --rpc --rpcapi db,eth,net,web3,personal --cache=1024 --rpcport 8545 --rpcaddr 127.0.0.1 --rpccorsdomain "*"

.........................
...................
INFO [04-07|05:39:03] Maximum peer count                       ETH=25 LES=0 total=25
INFO [04-07|05:39:03] Starting peer-to-peer node               instance=Geth/v1.8.2-stable-b8b9f7f4/linux-amd64/go1.9.4
INFO [04-07|05:39:03] Allocated cache and file handles         database=/root/.ethereum/testnet/geth/chaindata cache=768 handles=1024
INFO [04-07|05:39:03] Writing custom genesis block
INFO [04-07|05:39:03] Persisted trie from memory database      nodes=355 size=65.25kB time=880.18s gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [04-07|05:39:03] Initialised chain configuration          config="{ChainID: 3 Homestead: 0 DAO: <nil> DAOSupport: true EIP150: 0 EIP155: 10 EIP158: 10 Byzantium: 1700000 Constantinople: <nil> Engine: ethash}"
INFO [04-07|05:39:03] Disk storage enabled for ethash caches   dir=/root/.ethereum/testnet/geth/ethash count=3
INFO [04-07|05:39:03] Disk storage enabled for ethash DAGs     dir=/root/.ethash                       count=2
INFO [04-07|05:39:03] Initialising Ethereum protocol           versions="[63 62]" network=3
INFO [04-07|05:39:03] Loaded most recent local header          number=0 hash=419410ca4a2d td=1048576
INFO [04-07|05:39:03] Loaded most recent local full block      number=0 hash=419410ca4a2d td=1048576
INFO [04-07|05:39:03] Loaded most recent local fast block      number=0 hash=419410ca4a2d td=1048576
INFO [04-07|05:39:03] Regenerated local transaction journal    transactions=0 accounts=0
INFO [04-07|05:39:03] Starting P2P networking
INFO [04-07|05:39:05] UDP listener up                          self=enode://b1af970e1f82f68d9ebb5da483a781286ed399d823c82a0b1ec37c7946cf021a1b8e4dd32eb79bb534a350167c9bf28cdc6d428317ae36b24714983665f03d7e@[::]:30303
INFO [04-07|05:39:05] RLPx listener up                         self=enode://b1af970e1f82f68d9ebb5da483a781286ed399d823c82a0b1ec37c7946cf021a1b8e4dd32eb79bb534a350167c9bf28cdc6d428317ae36b24714983665f03d7e@[::]:30303
INFO [04-07|05:39:05] HTTP endpoint opened                     url=http://127.0.0.1:8545 cors=* vhosts=localhost
INFO [04-07|05:39:05] IPC endpoint opened                      url=/root/.ethereum/testnet/geth.ipc
```

출력 내용은 [https://ropsten.etherscan.io/](https://ropsten.etherscan.io/) 에서 확인가능하다.



docker 실행된 상태에서 나오기 위해선 exit 말고 ctrl + p , ctrl + q 를 차례대로 입력한다. 



나중에 도커 연결하기 위해서 필요한 주소

`rpcaddr 을 127.0.0.1 을 0.0.0.0` 으로 변경 후 `docker-machine ip` 로 아이피를 가져와야 한다.

```
geth --testnet --syncmode fast --rpc --rpcapi db,eth,net,web3,personal --cache=1024 --rpcport 8545 --rpcaddr 0.0.0.0 --rpccorsdomain "*"
```



## Geth 인자 설명

```
geth --testnet --syncmode fast --rpc --rpcapi db,eth,net,web3,personal --cache=1024 --rpcport 8545 --rpcaddr 127.0.0.1 --rpccorsdomain "*"
```

이제 geth 노드를 시작할 때 전달하는 인수들에 대해 알아봅시다

`--testnet`: geth가 시작하면서 테스트 네트워크로 연결하도록 지시합니다. 우리가 연결할 테스트 네트워크의 이름은 **Ropsten**입니다. 다음 절에서 다양한 테스트 네트워크에 대해 자세히 알아볼 것입니다.

`--syncmode fast`: 첫 번째 깅좌에서 블록체인 소프트웨어를 다운로드하고 시작하면 블록체인의 전체 복사본을 컴퓨터에 다운로드해야 한다는 사실을 알 수 있었습니다. 전체 블록체인을 다운로드하면 각 블록 내의 모든 단일 트랜잭션 기록도 받게 되기에, 로컬에서 블록체인의 전체 내역을 볼 수 있습니다. 이것은 시간을 굉장히 소모합니다. 하지만 각 트랜잭션을 실행하는 대신 트랜잭션 수신 확인을 다운로드하는 최적화 모드도 있습니다. 이번 실습에서는 전체 블록체인 히스토리가 필요하지 않으므로, 고속 모드를 사용하여 블록체인을 동기화할 것이며 syncmode fast가 여기에 해당하는 인자입니다. 다양한 모드에 대한 설명은 [이곳](https://ethereum.stackexchange.com/questions/11297/what-is-geths-light-sync-and-why-is-it-so-fast/11300#11300)을 참고하세요.

`--rpc --rpcapi db,eth,net,web3,personal`: 이 인자는 geth에게 RPC를 통한 요청을 수락하도록 하고, 다음 절에서 사용할 특정 API를 활성화하도록 지시합니다.

`--rpcport 8545 --rpcaddr 127.0.0.1 --rpccorsdomain "*"`: 이 인자는 web3.js 라이브러리를 사용하여 블록체인 서버 / geth와 통신할 호스트 및 포트에 해당합니다.

`--bootnodes`: 노드는 네트워크에 처음 연결할 때 사전 정의된 부트 노드\(bootnode\) 중 하나를 사용합니다. 노드는 이러한 부트 노드를 통해 네트워크에 가입하고 다른 노드를 찾을 수 있습니다.

# 이더리움내에서 네트워크 종류

Geth를 설치하고 Ropsten 테스트넷에서 블록체인을 다운로드 하기 위해서 노드를 시작했다. 그런데 이 Ropsten은 뭐고 Rinkeby 는 무엇인가.

일단 이 두개는 테스트넷이고 차이점은 작업증명에서 차이가 있다. Ropsten은 Proof of Work 알고리즘을 사용하는 반면 Rinkeby 는 권한 증명\(Proof of Authority\) 을 사용한다. Kovan 이라는 유명한 테스트넷도 있긴 하다. 이건 페리티\(Parity\)가 필요하다. 패리티는 Geth와 비슷한 또 다른 클라이언 소프트웨어이다.

이더리움 표준을 구현하기만 하면 누구든지 자신이 선호하는 언어로 클라이언트 소프트웨어로 작성이 가능하다.

이부분 [이더리움 백서](http://gavwood.com/paper.pdf)에 볼수 있다.

> 이더리움 커뮤니티는 네트워크에서 실행중인 이더리움 소프트웨어에 v1.0, v2.0 처럼 지루한 버전명을 지정하는 대신 각 버전에 독자적인 이름을 붙여서 표현하고 있습니다.
>
> 이더리움 소프트웨어를 통해 새로운 블록체인 네트워크를 쉽게 시작할 수도 있습니다. 이더리움으로 프라이빗 네트워크를 개발해서 기업용으로 사용하는 사례도 있습니다.
>
> ## 워크플로우
>
> 이더리움 기반의 분산 응용 프로그램을 빌드하기 위한 워크플로우는 아래와 같습니다.
>
> 개발: Ganache
>
> 스테이징/테스트: Ropsten, Rinkeby, Kovan 또는 그 밖의 프라이빗 네트워크
>
> 프로덕션: Homestead \(업데이트 이후에는 Metropolis\)



