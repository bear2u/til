# 도커 & 쿠버네티스 5주차

> 부산에서 매주 진행되는 스터디입니다. 
>
> 부산에서 다른 스터디 내용을 보실려면 [카페](https://cafe.naver.com/busandev) 에서 보실수 있습니다.
>
> https://www.udemy.com/docker-and-kubernetes-the-complete-guide 을 공부하고 있습니다.

1주차 스터디

- [Dockerfile을 이용한 서버 배포](http://javaexpert.tistory.com/967?category=719756)

2주차 스터디

- [Docker-compose 입문](http://javaexpert.tistory.com/975?category=719756)

3주차 스터디

- [github를 통해 aws ec2로 자동 배포 #1](http://javaexpert.tistory.com/984?category=719756)

4주차 스터디

- [github를 통해 aws ec2로 자동 배포 #2](http://javaexpert.tistory.com/986)



## 5주차 스터디 공부 내용

이번 차에서는 좀 더 복잡한 형태의 서비스를 구성해볼 예정이다. 

인덱스를 넣으면 해당되는 피보나치 수열을 계산하는 출력해주는 앱을 개발할 예정이다. 

![1541256629219](https://github.com/bear2u/til/raw/master/devops/docker-study5/1541256629219.png)

## 화면 구성

화면은 `React` 로 작성될 예정이며 아래와 같이 구성된다. 

- 인덱스를 입력받는다. 
- Indicies I hava seen 에서는 지금까지 입력받은 인덱스를 저장해서 보여준다. ( by postgres db)
- 마지막 Worker 라는 로직을 통해서 수열이 계산되어 지고 그 값이 `Redis` 에 저장되서 보여주게 된다. 

![1541257175219](https://github.com/bear2u/til/raw/master/devops/docker-study5/1541257175219.png)

## 서비스 구성

![1541256933268](https://github.com/bear2u/til/raw/master/devops/docker-study5/1541256933268.png)

![1541257225557](https://github.com/bear2u/til/raw/master/devops/docker-study5/1541257225557.png)

- Nginx` 서버를 통해서 접근된다. 
- 프론트는 `React Server` 로 프록시 된다. 
- API 서버는 `Express Server` 로 프록시 된다. 
- `Express Server`는 `Redis` 로 값을 호출하지만 있는 경우 바로 리턴이 되겠지만 없는 경우 `Worker` 를 통해서 계산된 값을 다시 가져와서 저장하고 리턴을 한다. 
- 마지막으로 유저가 입력한 index은 저장을 `postgres` 통해서 한다.

> 이후에 진행되는 [소스](https://github.com/bear2u/docker_study_examp1/tree/eb8932b6b0acdcfed706177ed9f97bd21e3f57e5)는 여기에서 받을수 있다.

## 소스 구성

### Worker

피보나치 수열을 계산하는 로직이 담긴 서비스를 개발한다. 

- `redis` 추가시 구독
- 새로운 값이 입력되는 경우 `fib(index)` 함수를 통해서 값을 계산해서 다시 `redis` 에 저장한다.

> keys.js

```javascript
module.exports = {
  redisHost: process.env.REDIS_HOST,
  redisPort: process.env.REDIS_PORT
};
```

> index.js

```javascript
const keys = require('./keys');
const redis = require('redis');

const redisClient = redis.createClient({
  host: keys.redisHost,
  port: keys.redisPort,
  retry_strategy: () => 1000
});
const sub = redisClient.duplicate();

function fib(index) {
  if (index < 2) return 1;
  return fib(index - 1) + fib(index - 2);
}

sub.on('message', (channel, message) => {
  redisClient.hset('values', message, fib(parseInt(message)));
});
sub.subscribe('insert');
```

> package.json

```javascript
{
  "dependencies": {
    "nodemon": "1.18.3",
    "redis": "2.8.0"
  },
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon"
  }
}
```

### Server

> keys.js

- 설정값은 추후 도커 환경변수로 입력받게 된다. 

```javascript
module.exports = {
  redisHost: process.env.REDIS_HOST,
  redisPort: process.env.REDIS_PORT,
  pgUser: process.env.PGUSER,
  pgHost: process.env.PGHOST,
  pgDatabase: process.env.PGDATABASE,
  pgPassword: process.env.PGPASSWORD,
  pgPort: process.env.PGPORT
};

```

> index.js

- `express` 서버 사용
- `postgres` 호출
- `redis` 호출
- api 호출 따른 restful 작성

```javascript
const keys = require('./keys');

// Express App Setup
const express = require('express');
const bodyParser = require('body-parser');
const cors = require('cors');

const app = express();
app.use(cors());
app.use(bodyParser.json());

// Postgres Client Setup
const { Pool } = require('pg');
const pgClient = new Pool({
  user: keys.pgUser,
  host: keys.pgHost,
  database: keys.pgDatabase,
  password: keys.pgPassword,
  port: keys.pgPort
});
pgClient.on('error', () => console.log('Lost PG connection'));

pgClient
  .query('CREATE TABLE IF NOT EXISTS values (number INT)')
  .catch(err => console.log(err));

// Redis Client Setup
const redis = require('redis');
const redisClient = redis.createClient({
  host: keys.redisHost,
  port: keys.redisPort,
  retry_strategy: () => 1000
});
const redisPublisher = redisClient.duplicate();

// Express route handlers

app.get('/', (req, res) => {
  res.send('Hi');
});

app.get('/values/all', async (req, res) => {
  const values = await pgClient.query('SELECT * from values');

  res.send(values.rows);
});

app.get('/values/current', async (req, res) => {
  redisClient.hgetall('values', (err, values) => {
    res.send(values);
  });
});

app.post('/values', async (req, res) => {
  const index = req.body.index;

  if (parseInt(index) > 40) {
    return res.status(422).send('Index too high');
  }

  redisClient.hset('values', index, 'Nothing yet!');
  redisPublisher.publish('insert', index);
  pgClient.query('INSERT INTO values(number) VALUES($1)', [index]);

  res.send({ working: true });
});

app.listen(5000, err => {
  console.log('Listening');
});

```

> package.json

```javascript
{
  "dependencies": {
    "express": "4.16.3",
    "pg": "7.4.3",
    "redis": "2.8.0",
    "cors": "2.8.4",
    "nodemon": "1.18.3",
    "body-parser": "*"
  },
  "scripts": {
    "dev": "nodemon",
    "start": "node index.js"
  }
}
```

### Client

- Routing 을 통해서 OtherPage.js 호출
- `react-create-app` 을 통해서 설치
- `Fib.js`, `App.js`, `Otherpage.js` 참고

> Fib.js

```javascript
import React, { Component } from 'react';
import axios from 'axios';

class Fib extends Component {
  state = {
    seenIndexes: [],
    values: {},
    index: ''
  };

  componentDidMount() {
    this.fetchValues();
    this.fetchIndexes();
  }

  async fetchValues() {
    const values = await axios.get('/api/values/current');
    this.setState({ values: values.data });
  }

  async fetchIndexes() {
    const seenIndexes = await axios.get('/api/values/all');
    this.setState({
      seenIndexes: seenIndexes.data
    });
  }

  handleSubmit = async event => {
    event.preventDefault();

    await axios.post('/api/values', {
      index: this.state.index
    });
    this.setState({ index: '' });
  };

  renderSeenIndexes() {
    return this.state.seenIndexes.map(({ number }) => number).join(', ');
  }

  renderValues() {
    const entries = [];

    for (let key in this.state.values) {
      entries.push(
        <div key={key}>
          For index {key} I calculated {this.state.values[key]}
        </div>
      );
    }

    return entries;
  }

  render() {
    return (
      <div>
        <form onSubmit={this.handleSubmit}>
          <label>Enter your index:</label>
          <input
            value={this.state.index}
            onChange={event => this.setState({ index: event.target.value })}
          />
          <button>Submit</button>
        </form>

        <h3>Indexes I have seen:</h3>
        {this.renderSeenIndexes()}

        <h3>Calculated Values:</h3>
        {this.renderValues()}
      </div>
    );
  }
}

export default Fib;

```

> OtherPage.js

```javascript
import React from 'react';
import { Link } from 'react-router-dom';

export default () => {
  return (
    <div>
      Im some other page
      <Link to="/">Go back to home page!</Link>
    </div>
  );
};

```

> App.js

```javascript
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
import { BrowserRouter as Router, Route, Link } from 'react-router-dom';
import OtherPage from './OtherPage';
import Fib from './Fib';

class App extends Component {
  render() {
    return (
      <Router>
        <div className="App">
          <header className="App-header">
            <img src={logo} className="App-logo" alt="logo" />
            <h1 className="App-title">Welcome to React</h1>
            <Link to="/">Home</Link>
            <Link to="/otherpage">Other Page</Link>
          </header>
          <div>
            <Route exact path="/" component={Fib} />
            <Route path="/otherpage" component={OtherPage} />
          </div>
        </div>
      </Router>
    );
  }
}

export default App;
```



>  위의 모든 소스는 [여기](https://github.com/bear2u/docker_study_examp1/tree/eb8932b6b0acdcfed706177ed9f97bd21e3f57e5)에서 받을수 있다. 



자 그럼 실제 이러한 서비스들을 각각의 도커로 만들어서 묶어서 연결하는지를 공부해도록 하자. 기대되는 순간이 아닐수 없다. 

## 도커 구성

![1541258576369](https://github.com/bear2u/til/raw/master/devops/docker-study5/1541258576369.png)

![1541258668555](https://github.com/bear2u/til/raw/master/devops/docker-study5/1541258668555.png)



### Complex 폴더

자 그럼 이제 도커 세팅을 해보자. 우선 기본 환경은 세개의 폴더로 구성된 루트에서 작업이 시작될 예정이다. 

> `complex` 폴더

![1541258847725](https://github.com/bear2u/til/raw/master/devops/docker-study5/1541258847725.png)

## Client Dockerfile.dev

> Client > Dockerfile.dev

```javascript
FROM node:alpine
WORKDIR '/app'
COPY './package.json' ./
RUN npm install
COPY . .
CMD ["npm", "run", "start"]
```

이게 낯설다면 이전 주차들을 다시 공부하도록 하자. 

```
> docker build -f Dockerfile.dev .
....
> docker run 도커 아이디
```

## Server Dockerfile.dev

> Server > Dockerfile.dev

```
FROM node:alpine
WORKDIR "/app"
COPY ./package.json ./
RUN npm install
COPY . .
CMD ["npm", "run", "dev"]
```

```
> docker build -f Dockerfile.dev .
....
> docker run 도커 아이디
```

커넥션 오류가 나올것이다. 아직 DB를 올린게 아니라서 그렇다. 

## Worker

> workder > Dockerfile.dev

```
FROM node:alpine
WORKDIR "/app"
COPY ./package.json ./
RUN npm install
COPY . .
CMD ["npm", "run", "dev"]
```

## Dcoker-Compose

우리가 구성할 건 다음과 같은 형태를 지닌다. 

![1541317170356](https://github.com/bear2u/til/raw/master/devops/docker-study5/1541317170356.png)

그러면 하나씩 구성해보도록 하자.  마지막에 설정한 환경변수는 각각의 폴더내 `keys.js` 에 들어갈 내용들이다. 

```javascript
module.exports = {
  redisHost: process.env.REDIS_HOST,
  redisPort: process.env.REDIS_PORT,
  pgUser: process.env.PGUSER,
  pgHost: process.env.PGHOST,
  pgDatabase: process.env.PGDATABASE,
  pgPassword: process.env.PGPASSWORD,
  pgPort: process.env.PGPORT
};
```

### docker-compose.yml

dockerfile들을 한번에 순서대로 만들기 위한 통합파일을 만들어준다. 위치는 root 에서 만든다. 

> docker-compose.yml

- `Postgres`

  ```ym
  version: '3'
  services:
    postgres:
      image: 'postgres:latest'
  ```

  ```
  > docker-compose up
  ```

  ![1541317907401](https://github.com/bear2u/til/raw/master/devops/docker-study5/1541317907401.png)

- `redis`

  ```
  redis:
      image: 'redis:latest'
  ```

  ```
  docker-compose up
  ```

  ![1541318365509](https://github.com/bear2u/til/raw/master/devops/docker-study5/1541318365509.png)


- `server`

  - **Specify build**

    ```
    build:
        dockerfile: Dockerfile.dev
        context: ./server
    ```

  - **Specify volumes**

    ```
    volumes:
          - /app/node_modules
          - ./server:/app
    ```

  - **Specify env variables**

    - 환경 설정시 `variableName` 지정을 안 하는 경우 현재 시스템에 있는 변수로 적용된다.

    ![1541318695353](https://github.com/bear2u/til/raw/master/devops/docker-study5/1541318695353.png)

    ```
        # Specify env variables
        environment:
          - REDIS_HOST:redis
          - REDIS_PORT:6379
          - PGUSER:postgres
          - PGHOST:postgres
          - PGDATABASE:postgres
          - PGPASSWORD:postgres_password
          - PGPORT:5432
    ```

- `Client `

  ```
    client:
      build:
        dockerfile: Dockerfile.dev
        context: ./client 
      volumes:
        - /app/node_modules
        - ./client:/app  
  ```

- `worker`

  ```
    worker:
      build:
        dockerfile: Dockerfile.dev
        context: ./client    
      volumes:
        - /app/node_modules
        - ./worker:/app 
  ```

전체 소스는 다음과 같다. 

> `docker-compose.yml`

```
version: '3'
services:
  postgres:
    image: 'postgres:latest'
  redis:
    image: 'redis:latest'  
  server:
    # Specify build   
    build:
      dockerfile: Dockerfile.dev
      context: ./server      
    # Specify volumes
    volumes:
      - /app/node_modules
      - ./server:/app
    # Specify env variables
    environment:
      - REDIS_HOST:redis
      - REDIS_PORT:6379
      - PGUSER:postgres
      - PGHOST:postgres
      - PGDATABASE:postgres
      - PGPASSWORD:postgres_password
      - PGPORT:5432
  client:
    build:
      dockerfile: Dockerfile.dev
      context: ./client    
    volumes:
      - /app/node_modules
      - ./client:/app    
  worker:
    build:
      dockerfile: Dockerfile.dev
      context: ./client    
    volumes:
      - /app/node_modules
      - ./worker:/app       
```



## nginx

![1541321649446](https://github.com/bear2u/til/raw/master/devops/docker-study5/1541321649446.png)

Proxy 설정을 해서 프론트와 백단을 분리를 해보자. 

`nginx docker` 이미지에 기본 설정을 `client`와 `server` 를 추가해서 proxy 해주도록 하자. 

> `nginx/default.conf`

![1541321896511](1541321896511.png)

```
upstream client {
    server client:3000;
}

upstream server {
    server server:5000;
}

server {
    listen 80;

    location / {
        proxy_pass http://client;
    }

    location /api {
        rewrite /api/(.*) /$1 break;
        proxy_pass http://server;
    }
}

```

- ` rewrite /api/(.*) /$1 break;` 는 `/api` 로 들어오는 경우 내부에서 다시 `/` 로변경해주기 위함
- client 는 3000 포트로 내부에서 proxy 되며
- server 는 5000 포트로 내부에서 proxy 된다. 
- 외부에서는 80 포트만으로 제어한다.

### DockerFile.dev 생성

> nginx/Dockerfile.dev

```
FROM nginx
COPY ./default.conf /etc/nginx/conf.d/default.conf
```

- `nginx` 도커 이미지를  가져온다.
- 기본 설정 `default.conf` 파일을 도커 이미지내 설정으로 덮어쓴다.

### docker-compose 에 nginx 추가

- `nginx` 폴더내 `Dockerfile.dev` 를 참조해서 생성한다. 
- `localhost`는 3050으로 설정하고 `nginx` 도커는 80 으로 바인딩 해준다. 
- 문제가 생길 경우 자동으로 재실행을 해준다.

```
  nginx:
    restart: always
    build:
      dockerfile: Dockerfile.dev
      context: ./nginx  
    ports:
      - '3050:80'  
```

## docker-compose 실행

이제 잘되는지 실행을 해보자. 모든 도커 파일을 다시 만들도록 하자. 

```
docker-compose up --build
```

서버가 정상적으로 실행된 것 확인하기 위해선 진입점을 확인해야 한다. 

이전에 nginx 서버는 3050포트와 바인딩된 상태이다. 

```
    ports:
      - '3050:80'
```

그럼 이제 확인해본다. 

```
http://localhost:3050
```

## React 서버 실행

정상적으로 React 서버가 뜨는 걸 볼수 있다. 

![1541326917598](https://github.com/bear2u/til/raw/master/devops/docker-study5/1541326917598.png)

하지만 개발자 도구에 콘솔을 열어보면 웹소켓 문제가 보인다. 

![1541326944986](https://github.com/bear2u/til/raw/master/devops/docker-study5/1541326944986.png)

실시간 연동이 안될 뿐이지 다시 새로고침을 해보면 정상적으로 저장된 걸 볼수 있다. 

- `5번째 피보나치 수는 8이다.`

![1541327068667](https://github.com/bear2u/til/raw/master/devops/docker-study5/1541327068667.png)

그러면 `nginx` 서버에서 `웹소켓`을 설정해서 실시간 반영이 되도록 해보자. 

> `nginx>default.conf`

```
location /sockjs-node {
    proxy_pass http://client;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "Upgrade";
}
```

`Dockerfile` 을 새로 빌드해서 `up` 을 해보자.

![1541327666979](https://github.com/bear2u/til/raw/master/devops/docker-study5/1541327666979.png)

정상적으로 보여지는 걸 볼수 있다. 

## 문제점

1. 만약 submit 을 했는데 실시간으로 변경이 안되는 경우

`client/Fib.js` 에 다음과 같은 코드를 넣어야 한다. 

```
componentDidMount() {
  setInterval(() => {
    this.fetchValues();
    this.fetchIndexes();
  }, 1000)
}
```

2. docker-compose up 을 할때 오류가 나면 다시 up 을 해주자. 
3. window에서 종종 오류가 나기 때문에 mac이나 리눅스에서 하길 추천한다. 

> 여기까지 소스는  [Github](https://github.com/bear2u/docker_study_examp1) 에서 받을 수 있다. 

이상으로 5주차 도커&쿠버네이트 수업 정리를 마치도록 한다.