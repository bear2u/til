# EOSJS 를 이용한 EOS 월렛 개발

> 부산에서 매주 [스터디](https://cafe.naver.com/busandev/97)를 진행하면서 같이 공부한 내용을 정리했습니다. (1주차)
>
> 다른 스터디를 보실려면 [네이터 카페](https://cafe.naver.com/busandev) 에서 확인 가능합니다. 

## EOS 서버 종류

- [EOS 메인넷](https://eos.io/)
- [정글넷 ( 테스트넷 )](http://jungle.cryptolions.io/)
- [키린넷 ( 테스트넷 )](https://www.cryptokylin.io/)

## 계정 생성

eos는 기본적으로 계정을 만들때 비용이 지불된다. 즉 기존의 보유자가 생성을 해줘야 한다는 뜻이다. 

메인넷을 연동해서 하는 게 아니기 때문에 정글넷에 계정을 생성하는 방법에 대해 알아보자. 

### 정글넷

![1541662292153](C:\Users\dev\AppData\Roaming\Typora\typora-user-images\1541662292153.png)

메뉴에 보면 `create account ` 가 있으며 주의할 점은 a-z, 1-5만 허용되며 12글자까지 가능하다. 

### 키린넷

[키린 github](https://github.com/cryptokylin/CryptoKylin-Testnet) 에 들어가면 하단에 만드는 방법이 나온다. 

```
curl http://faucet.cryptokylin.io/create_account?(원하는 아이디 12자리)
```

그리고 만든 후 무료 eos 토큰도 받을 수 있다. 

```
curl http://faucet.cryptokylin.io/get_token?111111111ooo
or
curl http://13.230.87.138/get_token?111111111ooo
```

계정을 만든 후 다시 [키린넷](https://www.cryptokylin.io/) 에 들어가서 모니터링을 통해서 잘 만들어졌는지 확인 해보자. 

## 준비물

- NodeJS
- 에디터 ( VSCode 추천)

## API

### getInfo

> 블록들에 대한 정보
- `chainId` : 연결할려는 블록체인의 고유 ID를 뜻한다. 
- `httpEndpoint`: 키린넷에 연결하는 주소를 뜻한다. 
- `getInfo` 는 `promise` 형태를 지원하지 않는다. 
```javascript
const Eos = require('eosjs');

const config = {
    httpEndpoint : "https://api-kylin.eosasia.one",
    chainId : "5fff1dae8dc8e2fc4d5b23b2c7665c97f9e9d8edf2b6485a86ba311c25639191"
}

Eos(config).getInfo((error, info) => {
    if(error) {
        console.error(error);
    }
    console.log(info);
});
```

### getBlock

> 지정된 블록에 대한 정보

```javascript
const Eos = require('eosjs');

const config = {
    httpEndpoint : "https://api-kylin.eosasia.one",
    chainId : "5fff1dae8dc8e2fc4d5b23b2c7665c97f9e9d8edf2b6485a86ba311c25639191"
}

Eos(config).getBlock(1, (error, blockInfo) => {
    if(error) {
        console.error(error);
    }

    console.log(blockInfo.transactions[0].trx);
});

```

