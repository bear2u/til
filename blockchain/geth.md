# Geth 튜터리얼

```
>> mkdir geth
>> cd geth
>> mkdir ethereum
>> vi ethereum/genesis.json

#genesis.json

{
  "config": {
    "chainId": 33,
    "homesteadBlock": 0,
    "eip155Block": 0,
    "eip158Block": 0
  },
  "nonce": "0x0000000000000033",
  "timestamp": "0x0",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "gasLimit": "0x8000000",
  "difficulty": "0x100",
  "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "coinbase": "0xBC9aAd0B1598a9388f3535272376006eeaF1eea2",
  "alloc": {}
}


```

```
# mix hash
Nonce와 결합하여 블록에서 충분한 양의 계산이 수행되었음을 증명하는 256 비트 해시 : 증명 작업 (Poof).

nonce와 mixhash의 조합은 Yellowpaper, 4.3.4에 설명 된 수학적 조건을 충족해야합니다. 블록 헤더 유효성, (44). 블록이 실제로 암호화 방식으로 채워 졌는지 확인할 수 있으므로이 측면에서 유효합니다.

이 값은 마이닝 계산 중에 DAG 데이터 파일에서 선택된 값 집합의 축소 표현 (간단한 파울러 - Noll-Vo 해시 함수 사용)입니다. 이 선택 선택은 주어진 Block 헤더에 의존하는 구현 된 Estah 'Hashimoto 알고리즘을 따른다. Minner가 올바른 Block Nonce (ASIC 저항, 높은 IO 참조)를 검색하는 동안 수행하는 각 해시 작업에 대해 적용된 혼합 해시가 다시 결정됩니다. 마지막 블록 혼합 해시는 유효한 블록으로 연결되는 값입니다. 이 값이 블록 설명자의 일부인 이유는이 값이 다음 블록의 // parentHash //의 일부가된다는 것입니다. 이로 인해 잠재적 인 공격자가 잘못된 DAG 데이터 파일을 만들어 불법 블록을 만들 수 있습니다.

# nonce
보낸 사람이 보낸 트랜잭션 수와 동일한 스칼라 값입니다.

이 블록에서 충분한 양의 계산이 수행 된 혼합 해시와 결합 된 64 비트 해시입니다.

논스 (nonce)는 특정 토큰 값을 결정할 때 특정 계산량이 소비되었다는 것을 의심 할 여지없이 증명할 수있는 암호로 안전한 채광 작업 증명입니다. (Yellowpager, 11.5 광업 증명).

최종 nonce 값은 알고리즘이 마이닝 대상을 충족시키는 nonce 값을 발견 할 수있는 마이닝 프로세스 반복의 결과입니다. Mining Target은 적용에 크게 의존하는 암호 학적으로 설명 된 조건입니다. Proce-of-Work nonce를 사용하면 블록의 유효성을 매우 빠르게 검증 할 수 있습니다.

# difficulty
이 블록의 넌스 발견 중 적용된 난이도에 해당하는 스칼라 값. 이전 블록의 난이도와 타임 스탬프에서 계산할 수있는 마이닝 타겟을 정의합니다. 난이도가 높을수록 유효한 블록을 찾기 위해 Miner가 수행해야하는 통계적 계산이 더 많습니다. 이 값은 블록 생성 빈도를 목표 범위 내로 유지하면서 블록 체인의 블록 생성 시간을 제어하는 ​​데 사용됩니다. 테스트 네트워크에서는 블록 체인에서 트랜잭션을 실행하는 데 유효한 블록 검색이 필요하므로 테스트 중에 대기하지 않도록이 값을 낮게 유지합니다.

# alloc
미리 채워진 지갑 목록을 정의 할 수 있습니다. 그것은 "Ether pre-sale"기간을 처리 할 수있는 Ethereum 특정 기능입니다. 로컬 Ether을 신속하게 마이닝 할 수 있으므로이 옵션을 사용하지 않습니다.

# coinbase
이 블록의 성공적인 마이닝에서 수집 된 모든 보상 (Ether에서)이 전송 된 160 비트 주소입니다. 그들은 광산 채권 자체와 계약 거래 집행 환급의 합계입니다. 종종 명세에서 "수혜자"라고 불리며 때로는 온라인 문서의 "etherbase"라고도합니다. 새로운 Block이 생성 될 때 Miner의 설정에 의해 값이 설정되기 때문에 이것은 Genesis Block의 모든 것일 수 있습니다.

# time stamp
이 블럭을 처음 시작할 때 Unix의 time () 함수의 합리적인 출력과 같은 스칼라 값.

이 메커니즘은 블록 사이의 시간의 관점에서 항상성을 강요합니다. 마지막 두 블록 사이의주기가 짧으면 난이도가 증가하므로 다음 유효한 블록을 찾기 위해 추가 계산이 필요합니다. 기간이 너무 길면 어려움 및 다음 블록 예상 시간이 줄어 듭니다.

타임 스탬프는 체인 내 블록 순서를 확인할 수도 있습니다 (Yellowpaper, 4.3.4. (43)).

주 : 항상성은 내부 조건이 안정되고 비교적 일정하게 유지되도록 변수가 조절되는 시스템의 특성입니다.

# parentHash
전체 부모 블록의 헤더 (nonce 및 mixhash 포함)의 Keccak 256 비트 해시입니다. 상위 블록에 대한 포인터. 따라서 효과적으로 블록 체인을 구축합니다. 창세기 블록의 경우에만이 경우에만 0입니다.

# extraData
선택적으로 무료이지만 최대 Blockchain에서 에테르를위한 스마트 한 것들을 보존하기 위해 32 바이트 길이의 공간.

# gasLimit
현재 블록 전체의 가스 소비에 대한 체인 전체 제한과 동일한 스칼라 값입니다. 우리의 경우에는 테스트 중이 임계 값에 의해 제한되는 것을 피하기 위해 높습니다. 참고 : 이것은 계약서의 가스 소비에주의를 기울여서는 안된다는 것을 의미하지는 않습니다.
```

출처: [http://blog.daum.net/\_blog/BlogTypeView.do?blogid=0PfgH&articleno=49&\_bloghome\_menu=recenttext&totalcnt=47](http://blog.daum.net/_blog/BlogTypeView.do?blogid=0PfgH&articleno=49&_bloghome_menu=recenttext&totalcnt=47)



```
>>  geth --datadir ethereum init ethereum/genesis.json
```

![](/assets/geth-1.png)

successfully ... 메세지가 보이면 성공적으로 세팅이 된것이다. 

그럼 디렉토리 구조는 

geh, geth/chaindata, geth/ethash, geth/nodes, keystore 등이 보일것이다. 





