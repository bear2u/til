# 타입스크립트로 만드는 블록체인의 이해

* 우선 타입스크립트는 \*.ts 확장자를 붙인다.

```
-타입스크립트 컨버팅 도구 설치-
yarn global add tsc
```

```
//tsc-watch yarn add 해줌
"start": "tsc-watch --onSuccess \" node dist/index.js \" "
```

그리고 tsconfig.json으로 환경설정해줘야 함

```
{
    "compilerOptions": {
        "module": "commonjs",
        "target": "ES2015",
        "sourceMap": true,
        "outDir": "dist" //컨버팅된 후 처리
    },
    "include": ["src/**/*"], //소스파일
    "exclude": ["node_moudles"]
}
```

기본 블록체인 형태

1. 제네시스 블록을 생성
2. 블록을 생성
3. 해쉬값 구하는 라이버러리 설치해서 해쉬값 가져오기 \( 모든 정보를 모아서 유니크한 해쉬값 만들어야 함\)
4. 그리고 블록이 정상적인지 체크
5. 마지막으로 볼록리스트\(배열\)에 해당 블록 추가

전체 소스

```typescript
import * as CryptoJS from "crypto-js";

class Block {
  static calculateBlockHash = (
    index: number,
    previousHash: string,
    timestamp: number,
    data: string
  ): string =>
    CryptoJS.SHA256(index + previousHash + timestamp + data).toString();

  static validateStructure = (aBlock: Block): boolean =>
    typeof aBlock.index === "number" &&
    typeof aBlock.hash === "string" &&
    typeof aBlock.previousHash === "string" &&
    typeof aBlock.timestamp === "number" &&
    typeof aBlock.data === "string";

  public index: number;
  public hash: string;
  public previousHash: string;
  public data: string;
  public timestamp: number;

  constructor(index, hash, previousHash, data, timestamp) {
    this.index = index;
    this.hash = hash;
    this.previousHash = previousHash;
    this.data = data;
    this.timestamp = timestamp;
  }
}

const genesisBlock: Block = new Block(0, "202020202020", "", "Hello", 123456);

let blockchain: [Block] = [genesisBlock];

const getBlockchain = (): Block[] => blockchain;

const getLatestBlock = (): Block => blockchain[blockchain.length - 1];

const getNewTimeStamp = (): number => Math.round(new Date().getTime() / 1000);

const createNewBlock = (data: string): void => {
  const previousBlock: Block = getLatestBlock();
  //   console.log(previousBlock);
  const newIndex: number = previousBlock.index + 1;
  //   console.log(newIndex);
  const newTimeStamp: number = getNewTimeStamp();
  const newHash: string = Block.calculateBlockHash(
    newIndex,
    previousBlock.hash,
    newTimeStamp,
    data
  );
  const newBlock: Block = new Block(
    newIndex,
    newHash,
    previousBlock.hash,
    data,
    newTimeStamp
  );
  addBlock(newBlock);
};

const getHashforBlock = (aBlock: Block): string =>
  Block.calculateBlockHash(
    aBlock.index,
    aBlock.previousHash,
    aBlock.timestamp,
    aBlock.data
  );

const isBlockValid = (candidateBlock: Block, previousBlock: Block): boolean => {
  if (!Block.validateStructure(candidateBlock)) {
    return false;
  } else if (previousBlock.index + 1 !== candidateBlock.index) {
    return false;
  } else if (previousBlock.hash !== candidateBlock.previousHash) {
    return false;
  } else if (getHashforBlock(candidateBlock) !== candidateBlock.hash) {
    return false;
  } else {
    return true;
  }
};

const addBlock = (candidateBlock: Block): void => {
  if (isBlockValid(candidateBlock, getLatestBlock())) {
    blockchain.push(candidateBlock);
  }
};

createNewBlock("block #1");
createNewBlock("block #2");
createNewBlock("block #3");
createNewBlock("block #4");

console.log(blockchain);

export {};

```

그럼 결과값은 다음과 같이 나와야 한다. 

```bash
[ Block {
    index: 0,
    hash: '202020202020',
    previousHash: '',
    data: 'Hello',
    timestamp: 123456 },
  Block {
    index: 1,
    hash: 'cb8de0d4dd720b05964d455ab9036dc3dd0606901e4d73e3521f85bee6beb0df',
    previousHash: '202020202020',
    data: 'block #1',
    timestamp: 1526353579 },
  Block {
    index: 2,
    hash: '9ba31a9eebd967bb74a2928733042c938481b6ba42a69c22a7924fe5283677b4',
    previousHash: 'cb8de0d4dd720b05964d455ab9036dc3dd0606901e4d73e3521f85bee6beb0df',
    data: 'block #2',
    timestamp: 1526353579 },
  Block {
    index: 3,
    hash: '0fff9b05edb251fb1a008e9caacba07e42afe7483ea83cb23783b7287c9aacf0',
    previousHash: '9ba31a9eebd967bb74a2928733042c938481b6ba42a69c22a7924fe5283677b4',
    data: 'block #3',
    timestamp: 1526353579 },
  Block {
    index: 4,
    hash: '9f5ecf51e0e3ed9157e4af0e1e79e2fb5638a417db1e200e8311c19142a91958',
    previousHash: '0fff9b05edb251fb1a008e9caacba07e42afe7483ea83cb23783b7287c9aacf0',
    data: 'block #4',
    timestamp: 1526353579 } ]
```



