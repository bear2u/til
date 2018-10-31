도커 & 쿠버네이트 3주차 스터디 정리
================================

이번 시간에는 실제 운영되는 배포까지 실습해보겠다. 
기본적으로 프로세스는 다음과 같다. 

Development -> Testing -> Deployment -> Developm...

1. 개발자가 소스를 github feature 브랜치에 푸시
2. 그럼 마스터 브랜치에 PR을 요청하면
3. 그럼 Travis CI 에서 테스팅을 진행
4. 테스팅을 진행해서 문제가 없는 경우 마스터 브랜치에 머지
5. aws에 정상적으로 배포

그럼 실습을 진행해보자. 
우선 노드와 React를 이용할 예정이다. 
노드 버전을 체크 해보자. 

```cmd
node -v

v8.11.3
```

만약 설치가 안된 경우 os에 맞는 [노드](https://nodejs.org)를 설치하자. 


그럼 리엑트 프로젝트를 만들어보자. 

```cmd
> npm install -g create-react-app

> create-react-app frontend
```

그럼 해당 위치에 `frontend`가 만들어져 있을 것이다. 

명령어 정리
- npm run start : 개발 서버 실행
- npm run test : 테스트 진행
- npm run build : production 를 위해 번들링 진행

잘 되는지 보기 위해

```cmd
npm run start
```

화면이 잘 나오는지 체크 해보자. 
-------------------------

vscode 에디터를 열어서 `Dockerfile.dev`를 하나 만들자. 
이 파일은 개발 전용 도커를 열어서 내부에서 방금 만든 리엑트 서버를 실행하기 위한 테스트 용도 이다. 

- Dockerfile.dev
```cmd
FROM node:alpine

WORKDIR '/app'

COPY package.json .
RUN npm install

COPY . .

CMD ["npm", "run", "start"]
```

잠깐 설명을 해보자면

```cmd
FROM node:alpine
```
- alpine 리눅스 os으로 부터 이미지를 가져온다.

```cmd
WORKDIR '/app'
```
- 도커내 os에서 /app 위치를 작업폴더로 지정

```cmd
COPY package.json .
RUN npm install
```
- 로컬 package.json을 복사한다. 
그리고 npm install을 한다. 이렇게 하는 이유는 package.json 내용만 변경 될 경우 캐싱을 이용안하고 설치를 하기 위해서다. 
즉 package.json 파일이 변경이 안된 경우에는 따로 `npm install`을 진행 안한다.

```cmd
COPY . .

CMD ["npm", run", "start"]
```
- 로컬 소스폴더를 해당 도커 이미지내 /app 폴더로 복사
- 마지막으로 `npm run start` 를 통해 서버 실행

그럼 도커파일을 빌드를 해보자. 

```cmd
> docker build .

unable to prepare context: unable to evaluate symlinks in Dockerfile path: lstat ~/docker/frontend/Dockerfile: no such file or directory
```

하지만 오류가 나올거로 예상된다. 이유는 빌드파일을 개발용으로 해서 직접 지정을 해서 빌드를 해줘야 한다. 
일반적으로 docker build . 를 하는 경우 해당 위치에서 `Dockerfile` 를 찾는다. 

다시 파일이름을 지정해서 빌드해보자. 
- Docerfile.dev 파일을 현재 위치에서 찾는다.
```
> docker build -f Dockerfile.dev .

Sending build context to Docker daemon  138.9MB
Step 1/6 : FROM node:alpine
 ---> 7ca2f9cb5536
Step 2/6 : WORKDIR '/app'
 ---> Using cache
 ---> 0fbd7e754211
Step 3/6 : COPY package.json .
 ---> Using cache
 ---> 0f93f7b66b3b
Step 4/6 : RUN npm install
 ---> Using cache
 ---> 5248cc4d46df
Step 5/6 : COPY . .
 ---> daa3db343355
Step 6/6 : CMD ["npm", run", "start"]
 ---> Running in c0e8e7a66d50
Removing intermediate container c0e8e7a66d50
 ---> b87605719148
Successfully built b87605719148
```

정상적으로 만들어 졌지만 처음 만들어질때 용량이 큰걸 확인할 수가 있다. 

```
Sending build context to Docker daemon  138.9MB
```

로컬내 `node_modules` 폴더가 같이 포함된 경우로 보여진다.
이제 삭제하고 다시 실행해보자. 

```
> docker build -f Dockerfile.dev .

Sending build context to Docker daemon  678.9kB
Step 1/6 : FROM node:alpine
 ---> 7ca2f9cb5536
Step 2/6 : WORKDIR '/app'
 ---> Using cache
 ---> 0fbd7e754211
Step 3/6 : COPY package.json .
 ---> Using cache
 ---> 0f93f7b66b3b
Step 4/6 : RUN npm install
 ---> Using cache
 ---> 5248cc4d46df
Step 5/6 : COPY . .
 ---> 226600115590
Step 6/6 : CMD ["npm", run", "start"]
 ---> Running in 948b682ec173
Removing intermediate container 948b682ec173
 ---> 2211f808c9dc
Successfully built 2211f808c9dc
```

삭제하고 다시 실행해보면 용량이 줄어든 걸 볼 수 있다. 

> 이제 서버를 실행
- 방금 빌드 한 이미지 아이디를 이용한다. 

```
> docker run 2211f808c9dc


> frontend@0.1.0 start /app
> react-scripts start

Starting the development server...

```

정상적으로 서버가 올라가는 걸 볼수 있다. 
하지만 로컬에서 접속시 안될것이다. 이유는 포트를 도커쪽과 바인딩을 해줘야 하기 때문이다. 

그럼 다시 바인딩을 해보자. 

```cmd
> docker run -p 3000:3000 2211f808c9dc

> frontend@0.1.0 start /app
> react-scripts start

Starting the development server...
....

```

정상적으로 올라갔다. 브라우저를 통해서 접속시 제대로 올라가는 걸 볼수 있다. 

> 로컬 서버 소스를 변경시??

그럼 만약 로컬 소스를 변경시 자동으로 도커내 이미지 소스와 연동되서 변경이 되는 걸까?

테스트를 위해서 `src/app.js`를 변경 해보자.

```
...
Hello World
...

```

그런데 아직 도커 웹 서버는 변경이 안되는 것 같다. 
이유는 도커를 빌드시 해당 소스에 대한 스냅샷을 만들어서 이미지를 올리기 때문이다. 

> 그럼 변경때마다 올려줘서 새로운 이미지로 만들어 줘야 하는 걸까?

> 윈도우 기반 앱 개발시 변경되는 시기가 늦는 경우가 있다. 이럴 경우 `.env` 파일을 루트에 만들고 `CHOKIDAR_USEPOLLING=true` 추가를 하자. 
그 이유에 대해서는 [링크](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#npm-start-doesnt-detect-changes) 를 참고하자. 

지금까지 구조에서는 로컬 폴더내 소스들이 그대로 스냅샷을 만들어서 복사해서 사용하는 구조이다. 하지만 로컬 서버내 소스가 그대로 변경사항을 적용하기 위해서는 reference 을 만들어서 소스 폴더들을(`app`) 매핑하는게 좋아보인다. 

> 소스 폴더를 레퍼런스로 매핑하자

- 다시 이미지를 빌드
- docker run with volume

```
$ docker build -f Dockerfile.dev .

Sending build context to Docker daemon  679.9kB
Step 1/6 : FROM node:alpine
 ---> 7ca2f9cb5536
Step 2/6 : WORKDIR '/app'
 ---> Using cache
 ---> 0fbd7e754211
Step 3/6 : COPY package.json .
 ---> Using cache
 ---> 0f93f7b66b3b
Step 4/6 : RUN npm install
 ---> Using cache
 ---> 5248cc4d46df
Step 5/6 : COPY . .
 ---> d88f67529cd4
Step 6/6 : CMD ["npm", "run", "start"]
 ---> Running in 12cbb9466cce
Removing intermediate container 12cbb9466cce
 ---> 9df085e7b11b
Successfully built 9df085e7b11b
```
- docker run with volume
```
$ docker run -p 3000:3000 -v $(pwd):/app 9df085e7b11b

> frontend@0.1.0 start /app
> react-scripts start

sh: react-scripts: not found
npm ERR! file sh
npm ERR! code ELIFECYCLE
npm ERR! errno ENOENT
npm ERR! syscall spawn
npm ERR! frontend@0.1.0 start: `react-scripts start`
npm ERR! spawn ENOENT
npm ERR! 
npm ERR! Failed at the frontend@0.1.0 start script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.
npm WARN Local package.json exists, but node_modules missing, did you mean to install?

npm ERR! A complete log of this run can be found in:
npm ERR!     /root/.npm/_logs/2018-10-21T20_59_02_912Z-debug.log
```

에러가 발생했다. 디펜시즈들을 찾을수가 없다고 한다. 
이유는 `app` 폴더를 레퍼런스를 만들었지만 처음에 `node_modules` 폴더를 삭제를 했었다. 그래서 그 안에 레퍼런스를 찾을수가 없다. 

`node_moduels` 레퍼런스를 다시 설정해주자.

```
$ docker run -p 3000:3000 -v /app/node_modules -v $(pwd):/app 9df085e7b11b

> frontend@0.1.0 start /app
> react-scripts start

Starting the development server...

Compiled successfully!

You can now view frontend in the browser.

  Local:            http://localhost:3000/
  On Your Network:  http://172.17.0.2:3000/

Note that the development build is not optimized.
To create a production build, use yarn build.
```

정상적으로 실행되는 걸 볼수 있다. 
정리하자면
```
docker run -p 3000:3000 -v /app/node_modules -v $(pwd):/app 9df085e7b11b
```
을 통해서 `/app/node_modules` 과 `$(pwd):/app` 을 통해서 레퍼런스를 만들고 바인딩을 해주고 있다. 

> `pwd` 는 현재 내 위치를 뜻한다. 

그럼 src/app.js 소스를 다시 수정해보자. 

```
...
Good React
...
```

정상적으로 리로딩 되서 화면에 나오는 걸 볼수 있다. 

> docker run ~~ 뭐시기... 아 너무 길어

- `docker run -p 3000:3000 -v /app/node_modules -v $(pwd):/app 9df085e7b11b` !!
- 명령어가 너무 길다. 이걸 편하게 쓸수 있지 않을까?
- docker-compose.yml 을 이용할 수 있다. 

- docker-compose.yml
```
version: '3'
services:
  web:
    build: . 
    ports:
      - "3000:3000"
    volumes:
      - /app/node_modules
      - .:/app 
```

>  `docker-compose up` or `docker-compose up --build`

- 두 명령어의 차이점은 새로 빌드를 하는 경우  `--build`를 통해서 올릴수 있다.

```
$ docker-compose up --build
Building web
ERROR: Cannot locate specified Dockerfile: Dockerfile
```

그런데 실행해보면 빌드 오류가 나올것이다. 이유는 처음에 빌드시 설명했듯이 `Dockerfile`을 찾을수가 없기 때문이다. 참고로 현재 폴더에는 `Dockerfile.dev`로 되어 있다.

다시 해당 도커 파일을 지정해보자. 

```yml
version: '3'
services:
  web:
    build:
      context: .
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    volumes:
      - /app/node_modules
      - .:/app  
```

```yml
    build:
      context: .
      dockerfile: Dockerfile.dev
```

해당 폴더내 `Dockerfile.dev` 를 지정해줄 수 있다.

그럼 다시 up을 해보자. 

```
$ docker-compose up
Recreating frontend_web_1 ... done
Attaching to frontend_web_1
web_1  | 
web_1  | > frontend@0.1.0 start /app
web_1  | > react-scripts start
web_1  | 
web_1  | Starting the development server...
web_1  | 
web_1  | Compiled successfully!
web_1  | 
web_1  | You can now view frontend in the browser.
web_1  | 
web_1  |   Local:            http://localhost:3000/
web_1  |   On Your Network:  http://172.18.0.2:3000/
web_1  | 
web_1  | Note that the development build is not optimized.
web_1  | To create a production build, use yarn build.
web_1  | 

```

정상적으로 웹에서 테스팅도 해보면 실행되는 걸 확인할 수 있다. 
그리고 `/src/app.js` 소스도 수정해보자. 

```
...
Good Choice React
...
```

> 테스트를 설정해보자. 

우선 위에 과정들에서 테스팅을 한다고 했을 경우

- 우선 docker build를 다시 하자. 
```

docker build -f Dockerfile.dev .

```
- docker run test를 진행
```
$ docker run ac00898b924c npm run test

 
> frontend@0.1.0 test /app
> react-scripts test

PASS src/App.test.js
  ✓ renders without crashing (32ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        2.138s
Ran all test suites.



```

일단 테스팅이 되긴 하는데...명령어를 더이상 입력못하는 상태가 되었다. <br/>
그럼 `-it` 옵션을 붙여서 직접 sh로 들어가서 실행해보자. 

```
docker run -it ac00898b924c npm run test

 PASS  src/App.test.js
  ✓ renders without crashing (5ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.095s, estimated 1s
Ran all test suites.

Watch Usage
 › Press f to run only failed tests.
 › Press o to only run tests related to changed files.
 › Press p to filter by a filename regex pattern.
 › Press t to filter by a test name regex pattern.
 › Press q to quit watch mode.
 › Press Enter to trigger a test run.
```

명령어도 직접 입력도 되고 정상적으로 테스팅이 되는 걸 볼수 있다. 

> 하지만 테스팅 소스 수정하면??

로컬에 있는 테스팅 소스를 수정시 정상적으로 업데이트가 안되는 걸 볼수 있다. 

테스팅을 2개 한다고 가정해보자.
기존에는 한개였음

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

it('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<App />, div);
  ReactDOM.unmountComponentAtNode(div);
});

it('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<App />, div);
  ReactDOM.unmountComponentAtNode(div);
});

```
테스팅을 1개에서 2개로 수정한 후 다시 docker 에서 실행해보자. 

```cmd
 PASS  src/App.test.js
  ✓ renders without crashing (26ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        1.643s
Ran all test suites.

Watch Usage
 › Press f to run only failed tests.
 › Press o to only run tests related to changed files.
 › Press p to filter by a filename regex pattern.
 › Press t to filter by a test name regex pattern.
 › Press q to quit watch mode.
 › Press Enter to trigger a test run.
```
여전히 1개만 테스팅이 되는 걸 볼수 있다. 

해결 방안 중 하나는 docker run을 한 상태에서 다른 콘솔에서 다시 테스팅을 진행하는 방법이다. 

```
$ docker compose-up
....
$ docker ps

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
2c673669e1d6        frontend_web        "npm run start"     20 minutes ago      Up 20 minutes       0.0.0.0:3000->3000/tcp   frontend_web_1

$ docker exec -it 2c673669e1d6 npm run test
...
```

exec 명령어를 통해서 실행되어 있는 서버에 라이브로 붙어서 테스팅을 할 수 있다. 

하지만 이건 좋은 프렉티스가 아니다. 

> docker-compose 에 새로운 명령어를 추가하자. 

- docker-compose.yml
- tests 콘테이너를 추가

```yml
version: '3'
services:
  web:
    build:
      context: .
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    volumes:
      - /app/node_modules
      - .:/app  
  tests:
    build:
      context: .
      dockerfile: Dockerfile.dev
    volumes:   
      - /app/node_modules
      - .:/app   
    command: ["npm", "run", "test"]  
```

그럼 실행해보자

```
$ docker-compose up --build

Building web
Step 1/6 : FROM node:alpine
 ---> 7ca2f9cb5536
Step 2/6 : WORKDIR '/app'
 ---> Using cache
 ---> 0fbd7e754211
Step 3/6 : COPY package.json .
 ---> Using cache
 ---> 5df741ed1932
Step 4/6 : RUN npm install
 ---> Using cache
 ---> 667dcc1e7cff
Step 5/6 : COPY . .
 ---> 4d785a4cbb04
Step 6/6 : CMD ["npm", "run", "start"]
 ---> Running in c8a14cb7bbf7
Removing intermediate container c8a14cb7bbf7
 ---> 7e86b10b989a
Successfully built 7e86b10b989a
Successfully tagged frontend_web:latest
Building tests
Step 1/6 : FROM node:alpine
 ---> 7ca2f9cb5536
Step 2/6 : WORKDIR '/app'
 ---> Using cache
 ---> 0fbd7e754211
Step 3/6 : COPY package.json .
 ---> Using cache
 ---> 5df741ed1932
Step 4/6 : RUN npm install
 ---> Using cache
 ---> 667dcc1e7cff
Step 5/6 : COPY . .
 ---> Using cache
 ---> 4d785a4cbb04
Step 6/6 : CMD ["npm", "run", "start"]
 ---> Using cache
 ---> 7e86b10b989a
Successfully built 7e86b10b989a
Successfully tagged frontend_tests:latest
Recreating frontend_tests_1 ... done
Recreating frontend_web_1   ... done
Attaching to frontend_tests_1, frontend_web_1
tests_1  | 
tests_1  | > frontend@0.1.0 test /app
tests_1  | > react-scripts test
tests_1  | 
web_1    | 
web_1    | > frontend@0.1.0 start /app
web_1    | > react-scripts start
web_1    | 
web_1    | Starting the development server...
web_1    | 
tests_1  | PASS src/App.test.js
tests_1  |   ✓ renders without crashing (39ms)
tests_1  | 
tests_1  | Test Suites: 1 passed, 1 total
tests_1  | Tests:       1 passed, 1 total
tests_1  | Snapshots:   0 total
tests_1  | Time:        3.597s
tests_1  | Ran all test suites.
tests_1  | 
web_1    | Compiled successfully!
web_1    | 
web_1    | You can now view frontend in the browser.
web_1    | 
web_1    |   Local:            http://localhost:3000/
web_1    |   On Your Network:  http://172.18.0.2:3000/
web_1    | 
web_1    | Note that the development build is not optimized.
web_1    | To create a production build, use yarn build.
web_1    | 
```

그런 후에 test에 하나의 테스트를 더 추가를 해보자.

- App.test.js

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

it('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<App />, div);
  ReactDOM.unmountComponentAtNode(div);
});

it('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<App />, div);
  ReactDOM.unmountComponentAtNode(div);
});

it('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<App />, div);
  ReactDOM.unmountComponentAtNode(div);
});


```

그럼 도커내에서 자동적으로 테스팅이 추가되서 실행되는 걸 볼수 있다.

```cmd
tests_1  | PASS src/App.test.js
tests_1  |   ✓ renders without crashing (29ms)
tests_1  |   ✓ renders without crashing (3ms)
tests_1  |   ✓ renders without crashing (3ms)
tests_1  | 
tests_1  | Test Suites: 1 passed, 1 total
tests_1  | Tests:       3 passed, 3 total
tests_1  | Snapshots:   0 total
tests_1  | Time:        2.647s, estimated 3s
tests_1  | Ran all test suites.
tests_1  | 

```

컨테이너가 2개가 올라간걸 볼수 있다. 

다른 콘솔을 열어서 실행해보자.
```
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
0ba2a75bd96a        frontend_web        "npm run start"     5 minutes ago       Up 5 minutes        0.0.0.0:3000->3000/tcp   frontend_web_1
2a40cff30f4f        frontend_tests      "npm run test"      5 minutes ago       Up 5 minutes                                 frontend_tests_1

```

> Nginx 웹서버 추가 등록

- 이제 프로덕션 하자.
- Dockerfile 파일내

1. React 프로젝트를 빌드
2. `npm run build`
3. 최종 나온 빌드 소스를 nginx html 폴더 복사
4. 그리고 빌드 된걸 run을 해서 서버를 실행하자. 

- Dockerfile 파일 생성

```
FROM node:alpine as builder
WORKDIR '/app'
COPY package.json .
RUN npm install
COPY . .
RUN npm run build

FROM nginx
COPY --from=builder /app/build /usr/share/nginx/html

```

```console
$ docker build .


Sending build context to Docker daemon    683kB
Step 1/8 : FROM node:alpine as builder
 ---> 7ca2f9cb5536
Step 2/8 : WORKDIR '/app'
 ---> Using cache
 ---> 0fbd7e754211
Step 3/8 : COPY package.json .
 ---> Using cache
 ---> 0f93f7b66b3b
Step 4/8 : RUN npm install
 ---> Using cache
 ---> 5248cc4d46df
Step 5/8 : COPY . .
 ---> 8a445e0ba651
Step 6/8 : RUN npm run build
 ---> Running in 56d7fc2d10e8

> frontend@0.1.0 build /app
> react-scripts build

Creating an optimized production build...
Compiled successfully.

File sizes after gzip:

  32.4 KB  build/static/js/1.6105a37c.chunk.js
  763 B    build/static/js/runtime~main.229c360f.js
  689 B    build/static/js/main.7d28dc35.chunk.js
  510 B    build/static/css/main.6e4ad141.chunk.css

The project was built assuming it is hosted at the server root.
You can control this with the homepage field in your package.json.
For example, add this to build it for GitHub Pages:

  "homepage" : "http://myname.github.io/myapp",

The build folder is ready to be deployed.
You may serve it with a static server:

  yarn global add serve
  serve -s build

Find out more about deployment here:

  http://bit.ly/CRA-deploy

Removing intermediate container 56d7fc2d10e8
 ---> a5886e8cd892
Step 7/8 : FROM nginx
 ---> dbfc48660aeb
Step 8/8 : COPY --from=builder /app/build /usr/share/nginx/html
 ---> b85d40ae190b
Successfully built b85d40ae190b
```

정상적으로 빌드 된 걸 볼수 있다. 

이제 마지막으로 실행해보자. 

```
docker run -p 8080:80 b85d40ae190b
```

`http://localhost:8080/` 으로 실행하면 nginx 서버로 접속되는 걸 볼수 있다.

이상으로 도커 & 쿠버네이트 3주차 스터디 한 내용이었다. 

