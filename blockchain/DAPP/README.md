우분투에 GO 설치

[https://medium.com/@patdhlk/how-to-install-go-1-9-1-on-ubuntu-16-04-ee64c073cd79](https://medium.com/@patdhlk/how-to-install-go-1-9-1-on-ubuntu-16-04-ee64c073cd79)

이더리움 및 솔리디티 설치

```
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install ethereum
sudo apt-get install solc
```

```
//확인
root@ip-172-31-7-147:/home/ubuntu# which geth
/usr/bin/geth
root@ip-172-31-7-147:/home/ubuntu# which solc
/usr/bin/solc
root@ip-172-31-7-147:/home/ubuntu# geth version
Geth
Version: 1.8.2-stable
Git Commit: b8b9f7f4476a30a0aaf6077daade6ae77f969960
Architecture: amd64
Protocol Versions: [63 62]
Network Id: 1
Go Version: go1.9.4
Operating System: linux
GOPATH=/root
GOROOT=/usr/lib/go-1.9


```

데이터 디렉토리를 먼저 만든다. 

```
mkdir ~/data_testnet
cd ~/data_testnet
pwd
/root/data_testnet
```



사설 네트워크 만드는 방법은 

[https://medium.com/mercuryprotocol/how-to-create-your-own-private-ethereum-blockchain-dad6af82fc9f](https://medium.com/mercuryprotocol/how-to-create-your-own-private-ethereum-blockchain-dad6af82fc9f)

그리고 환경설정 파일 genesis.json 을 만든다. 

```
{
   "config": {
      "chainId": 1994,
      "homesteadBlock": 0,
      "eip155Block": 0,
      "eip158Block": 0,
      "byzantiumBlock": 0
   },
   "difficulty": "400",
   "gasLimit": "2000000",
   "alloc": {
      "7b684d27167d208c66584ece7f09d8bc8f86ffff": { 
          "balance": "100000000000000000000000" 
      },
      "ae13d41d66af28380c7af6d825ab557eb271ffff": { 
          "balance": "120000000000000000000000" 
      }
   }
}
```

geth 실행

```
geth --datadir ./myDataDir --networkid 1114 console 2>> myEth.log
```



Geth 명령어 모음

**지갑생성**

```
personal.newAccount("패스워드") //지갑 생성
"0x80d35aac9b49f39858ff199853bbe65ef6b962c5"
```

**계정확인**

```
eth.accounts
["0x80d35aac9b49f39858ff199853bbe65ef6b962c5"]
```

채굴자 지정 및 확인

```
eth.coinbase
miner.setEtherbase(eth.accounts[1])
```

계정 잔액 확인

```
eth.getBalace(eth.accounts[0])
```

블록 노드수 확인

```
eth.blockNumber
```

채굴 시작

```
miner.start(thread_num)
-> minser.start(1)
```

ABI\(Application Binary Interface\) 를 취득한다. 

계약의 외부사항을 말한다. 계약에 포함되는 메소드와 인수, 반환값에 대한 정보로 계약에 접근할 때 필요한 정보 중 하나이다.                                                                                         

