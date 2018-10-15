# 도커 2주차 수업

## 복습
http://javaexpert.tistory.com/967?category=719756

## docker-compose를 만드는 방법에 대해 진행


### 1. package.json

```javascript
{
    "dependencies": {
        "express": "*",
        "redis": "2.8.0"
    },
    "scripts": {
        "start": "node index.js"
    }
}
```

### 2. index.js
- Docker compose를 통해서 내부 네트워크 설정을 진행
- redis를 연결시 `redis-server`을 바로 호출 해서 연결함
- process를 강제로 죽는 오류를 발생시켜서 도커 컨테이너상에서 오류에 대한 여러가지 대처방법을 배움
- `"no"`,`always`,`on-failure`등 여러가지 프로세스 오류에 대처할 수 있음

```javascript
const express = require("express");
const redis = require('redis');
const process = require('process');

const app = express();
const client = redis.createClient({
    host: "redis-server",
    port: 6379
});
client.set("visits", 0);

app.get("/", (req, res) => {
    process.exit(1);
    client.get("visits", (err, visits) => {
        if(err != null) {
            console.error(err);
        }
        res.send("Number of visits is " + visits);
        client.set('visits', parseInt(visits) + 1);
    });
});

app.listen(8081, () => {
    console.log('Listening on port2 8081');
});
```

## 3. Dockerfile
- alpine 노드 이미지 사용
- `/app` 에 작업 폴더 만들고 진행
- package.json을 복사해서 이 부분만 캐싱 되서 변경될 경우 npm install 진행
- 소스를 복사
- npm start 명령어 실행

```cmd
FROM node:alpine

WORKDIR '/app'

COPY package.json .
RUN npm install

COPY . .

CMD ["npm", "start"]
```

## 4.docker-compose.yml

```cmd
version: '3'
services:
  redis-server:
    image: 'redis'
  node-app:
    restart: on-failure
    build: .
    ports:
      - "4001:8081" 
```

> 참고사항

- docker-compose up 을 통해서 올릴수 있음
- Dockerfile 내용이 변경된 경우 docker-compose up --build를 통해서 다시 빌드해서 올릴 수 있음

여기까지 2주차를 진행했으며 다음시간에 여러 컨테이너에 올리는 방법에 대해서 배워보자.