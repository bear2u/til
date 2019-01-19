# NodeJS 입문 강좌

> 부산에서 매주 진행되는 `노드 입문 강좌 스터디`입니다.
>
> 부산에서 다른 스터디 내용을 보실려면 [카페](https://cafe.naver.com/busandev) 에서 보실수 있습니다.
>
> 이 강좌는 [제로초님의 인프런 강좌](https://www.inflearn.com/course/node-js-%EA%B5%90%EA%B3%BC%EC%84%9C/)를 보고 요약해놓은 내용입니다. 
>
> - `챕터 2` - ES2018
> - `챕터 3` - 노드 기능 알아보기

## const & let

### const

const 는 객체가 할당된 경우 변경이 불가능하지만 오브젝트에 대한 내부 값은 변경가능하다. 

```javascript
const item = {a: 1, b: 2, c: 3};

item.a = 3;

item => {a: 3, b: 2, c: 3}
```

### let

`구문적인 변수 영역 규칙`을 지원한다. 

{} 블록내에 변수 영역만 처리된다. 

예를 들어 보자. 

`var` 로 할 경우 밖의 변수까지 변경되는 걸 볼수 있다. 

```javascript
var test = "test";

if (test) {
    var test = "test2";
    console.log('블록내 : ',test);
}

console.log('블록밖', test);

.....
블록내 : test2
블록밖 test2
```

하지만 `let` 으로 할 경우 글로벌 변수의 값을 보호할 수 있다. 

```javascript
var test = "test";

if (test) {
    let test = "test2";
    console.log('블록내 : ',test);
}

console.log('블록밖 : ', test);

......
블록내 :  test2
블록밖 :  test
```

### 템플릿 문자열

템플릿 문자열은 문자열 연결 대신 사용할 수 있다. 중간에 변수를 삽입할수 있다.  

>  ${변수}, ` 로 감싼다. 

```
console.log(test + " " + test2); //기존 방식

console.log(`${test} ${test2}); //템플릿 문자열
```

### 객체 리터럴



### 화살표 함수 (중요)

화살표를 사용하면 모든 함수 정의를 한줄로 끝낼수 있다. function 키워드를 없앴고, 화살표가 어떤 값을 반환하는지 지정해주기 때문에 return 도 없다. 만약 파라미터가 하나만 받는 경우 괄호도 생략이 가능하다. 

화살표 함수는 this를 새로 바인딩 하지 않는다. 예를 들어 다음 코드에서 this는 `city`객체가 아니다. 

```javascript
const city = {
    areas: ["서울","부산"],
    print: function(delay = 1000) {
        setTimeout(function() {
            console.log(this.areas.join(','))
        }, delay);
    }
}

city.print();

....error
Cannot read property 'join' of undefined
```

이 경우 this는 window 객체이기 때문에 city 는 `undefined` 이다. 

화살표 함수는 함수 내부의 this를 외부의 this와 같게 만든다. 

```javascript
const city = {
    areas: ["서울","부산"],
    print: (delay = 1000) => {
        setTimeout( () => {
            console.log(this.areas.join(','))
        }, delay);
    }
}

city.print();

....error
Cannot read property 'join' of undefined
```

위 코드에서 this도 결국에는 window 의 객체이기 때문에 오류가 난다. 

그래서 상단의 `print` 함수를 `function` 으로 만들어주면 오류가 발생되지 않는다. 

```
const city = {
    areas: ["서울","부산"],
    print: function(delay = 1000){
        setTimeout( () => {
            console.log(this.areas.join(','))
        }, delay);
    }
}

city.print();

.....error
서울,부산
```

### 구조분해 ( destructuring )

구조분해를 사용하면 객체 안에 있는 필드 값을 원하는 변수에 대입할 수 있다. 

> 비구조화 할당 시 this가 의도와 다르게 동작하는 현상이 있을수 있는 점 유의하자.

```javascript
var city = {
    area: ['busan','seoul'],
    latlng: ['123:456', '789:321']
}

const { area, latlng } = city;

console.log(area[0], latlng[0]);
```

```javascript
...

const [ busan, seoul ] = area;

console.log( busan, seoul );
```

위 코드 처럼 배열도 비구조화 할당이 가능하다. 

### 스프레드 연산자

> 기존 배열의 값은 변경하지 않고 새로 만들어서 리턴한다.

파라미터 갯수를 동적으로 받을수 있다. 

```javascript
const x = (a, ...b) => console.log(a,b);

x(1,2,3,4,5);

........
1 [ 2, 3, 4, 5 ]
```

### Promise

> 비동기적인 동작을 잘 다루기 위한 방법 중 하나이다.

```javascript
const promise = new Promise((resolve, reject) => {
    const a = 1;
    const b = 1;
    if(a + b <= 2) {
        resolve(a + b);
    } else {
        reject(a + b);
    }
});

promise
    .then(( success ) => {console.log(success)})
    .catch((error) => {console.log(error)});


.... 성공
> 2
```

또한 여러개의 `then`으로 사용가능하다. 콜백안에 다시 콜백이 있을 경우 여러개의 `then` 으로 이어진다. 그러기 위해선 `resolve`로 리턴을 해야줘야 한다. 

```javascript
const promise = new Promise((resolve, reject) => {
    const a = 1;
    const b = 1;
    if(a + b <= 2) {
        resolve(a + b);
    } else {
        reject(a + b);
    }
});

promise
    .then(( success ) => {
        return new Promise((resolve, reject) => {
            resolve("result : " + success);
        })
    })
    .then((success2) => console.log(success2))
    .catch((error) => {console.log("reject:" + error)});

....
result : 2
```

### Promise API

> 무조건 성공 또는 실패 하는 경우일 경우 더 줄여서 가능하다. 

```javascript
const success = Promise.resolve('success');    
const failed = Promise.reject('failed');
```

> `Promise` 함수들을 모아서 하나의 `Promise` 로 실행할수 있다. 
>
> 전부 성공시 `success` 로 넘어가지만 한개라도 실패시 `catch` 로 들어가는 점 유의하자.

```javascript
Promise.all([promise, success])
    .then((results) => {
        console.log(results);
    })
    .catch((error) => {
        console.log(error);
    })
```

### Async / Await

> `Async` 도 결국 내부에는 `Promise` 기반이므로 `Promise`를 꼭 습득하고 가자. 

Promise의 단점은 결국 콜백함수내에서 이루어지기 때문에 외부 코드들과 연동성이 비동기로 이루어진다. 그래서 그걸 극복하게 동기형으로 바꿔주는 게 `Async/Await` 이다. 

```javascript
check = async () => {
    console.log('await result: ',await promise);
}

check();
```

오류 처리는 `try / catch` 문으로 한다. 

```javascript
check = async () => {
    try {
        console.log('await result: ',await promise);
    } catch(e) {
        console.error('error: ',e)
    }
}

check();
```

### 모듈

자바스크립트 모듈은 자바스크립트 파일에서 쉽게 불러와서 활용할 수 있는 재사용 가능한 코드 조각들을 말한다. 즉 쪼개서 불러와서 사용이 가능하다는 것이다. 

자바스크립트는 각각의 모듈을 별도의 파일로 저장한다. 모듈을 만들고 외부에 익스포트 하는 방법에는 한 모듈에서 여러 자바스크립트 객체를 외부에 노출시키는 방법과 모듈당 하나의 자바스크립트 객체를 노출시키는 방법이 있다. 

#### 여러 객체를 외부에 노출 시키는 방법

```javascript
>> helper.js

export const print = (message) => log(message, new Date())

export const log = (message, timestamp) => console.log(`${timestamp.toString()}: ${message}`)

module.exports = {
    print,
    log
}
```

```javascript
const { print } = require('./txt_helper');

print("good");
```

#### 하나의 객체를 외부에 노출 시키는 방법

```javascript
const print = (message) => console.log(message, new Date())

export default print;
```

```javascript
import print from './txt_helper_only_one.mjs';

print("good");
```

### global

> 노드의 전역 객체

노드 실행환경내 전역에서 공통적으로 사용되는 객체이다. 

```javascript
> B.js

module.exports = () => global.message;
```

```javascript
> C.js

const B = require('./B');

global.message = 'ok';

console.log(B());

>> ok 출력
```

### console

console 객체네는 디버깅을 도와주는 많은 메소드가 준비되어 있다. 

- `console.time`('인자') & `console.timeEnd`('인자') - 전체 걸리는 시간을 측정해준다. (인자는 똑같아야 한다)
- `dir`: 오브젝트 객체를 로깅할때 사용한다. `colors` 는 색상 지정, `depth` 는 깊이 지정
- `trace`: 에러 발생시 오류 경로를 추척해서 보여준다. 

### setTimeout, setInterval, setImmediate

> https://nodejs.org/ko/docs/guides/timers-in-node/

- `setTimeout` : 일정시간 후에 콜백이 실행된다. 
  - `clearTimeout` 로 취소 가능
- `setInterval` : 일정 시간마다 콜백이 실행된다. 
  - `clearInterval`로 취소 가능
- `setImmediate` : 이벤트 루프 주기 끝에 코드를 실행한다. 
  - `clearImmediate`로 취소 가능

### __filename, __dirname

- `__filename` 은 현재 파일 위치까지 위치를 리턴한다. 
- `__dirname` 은 디렉토리까지 위치를 리턴한다. 

### process

현재 실행중인 노드 프로그램 정보가 들어있다. 

- `process.version` : 노드 설치 버전
- `process.arch` : 32비트인지 64비트인지 체크
- `process.platform` : 운영체제
- `process.pid` : 프로세스 아이디 ( 문제가 발생시 강제 종료시 아이디 지정 가능 )
- `process.uptime` : 프로세스 실행되고 얼마나 지났는지 체크
- `process.cwd` : 노드 프로그램이 실행되고 있는 위치
- `process.exePath` : 노드 설치 위치
- `process.cpuUsage` : cpu 사용량
- `process.exit` : 프로세스 종료

### OS

운영체제 관련된 모듈

- `os.cpu` 의 경우는 멀티 프로세싱에서 자주 쓰인다.

### Path

> 경로 관련된 모듈.  **중요한 모듈중 하나이다. **

- `path.delimiter` : 환경변수 패스 출력
- `path.sep` : 경로 구분자 표시 . 윈도우는 `\\` 이다. 
- `path.dirname` : 경로 폴더 
- `path.extname` : 확장자
- `path.basename` : 파일 이름
- `path.parse` : 구조로 분해
- `path.format` : 분해된 구조를 합쳐준다.
- `path.normalize` : 잘못된 경로를 자동으로 정상적으로 만들어준다. 
- `path.isAbsoulte` : 경로가 절대경로인지 상대경로인지 체크
- `path.join` : 절대 경로 무시하고 조각나있는 경로를 합쳐준다. 
- `path.resolve` : 절대 경로 고려해서 합친다. 루트는 `C:\`

### Url

> 도메인이 생략된 경우는 `parse` 를 통해서 처리

- `parse`
- `format`
- `searchParams` : 파라미터 내용을 오브젝트 형태로 가져온다.
  - 파라미터들을 추가,수정,삭제등 할수 있다. (`append`,`set`,`delete`,`get`)

![url êµ¬ì¡°ì ëí ì´ë¯¸ì§ ê²ìê²°ê³¼](https://raw.githubusercontent.com/lifeisgouda/img/master/programming/nodejs/url.png)

### querystring

- `url` 모듈과 같이 사용한다. 
- `url.parse` ->`querystring.parse` 로 이용
- `stringify` 는 파싱된 걸 다시 합쳐준다. 

### crypto 

##### 단방향 암호화(해시)

- 비밀번호를 암호화할때 필요하다. 

- 복호화는 안된다. 

- `createHash` 는 비밀번호가 짧을 수록 같은 값이 나올수 있다. 

- 좀 더 나은 암호화 방식인 `pbkdf2` 함수를 사용

  - `salt` 라는 문자열을 비밀번호에 추가해서 좀 더 암호화한다. 
  - salt 는 암호화된 비밀번호와 같이 저장
  - iteration 은 1초정도가 걸릴때까지 올려주면 좋음
  
- 실무에선 `bcrypt` 등이 사용된다.

```javascript
crypto.createHash('sha512').update('password').digest('base64');
```

##### 양방향 암호화 (복호화 가능)

- 암호화시 `createCipher` 함수 사용
- `aes-256` 또는 `aes-256-cbc` 를 많이 사용한다.
- 복호화시 `createDecipher` 함수 사용
- `key` 값을 알아야 복호화를 할 수 있다. 
- `update` 함수 사용시 `base64`,`utf8` 인자가 암호화와 복호화시 바뀐다. 

### Util

- `callbackify` : promise 함수를 거꾸로 callback 함수로 바꿔준다. 
- `promisify`: `callback` 함수를 `promise` 로 바꿔주는 유틸 함수 (**중요함**)
- `deprecate` : 폐기되는 함수임을 지정한다. 
  - 오류 메세지도 인자로 넣을 수 있다. 
  - 결과에 `Warning` 이 나온다. 

### Fs (File System)

- 비동기 방식으로 콜백으로 결과로 받는다. 
- 여러개의 콜백을 지정시 순서가 일정하지가 않다. 

```javascript
const fs = require('fs');
fs.readFile('./readme.txt', (err, data) => {
    if(err) {
        throw err;
    }

    console.log(data); //바이너리 파일로 리턴된다. 
    console.log(data.toString());
});

.........
<Buffer ed 85 8c ec 8a a4 ed 8a b8 31 0d 0a ed 85 8c ec 8a a4 ed 8a b8 32>
테스트1
테스트2
```

- 동기 방식으로는  `sync` 방식도 제공한다. 
  - `readFileSync`

#### 기타 함수

- `access`
- `mkdir`
- `open`
- `rename`
- `unlink` : 파일 삭제
- `rmdir` : 폴더 삭제

#### promise

- 노드 10에서 `promise`사용가능

### 버퍼 ( Buffer ), 스트림 ( Stream )

- 데이터를 보낼때 일정량의 저장 공간에(버퍼) 담아서 보낸다. 
- 버퍼들의 통로를 뜻함

```javascript
const fs = require('fs');
const readStream = fs.createReadStream('./readme.txt', { highWaterMark: 8});
const data = [];

readStream.on('data', (chunk) => {
    data.push(chunk);
    console.log('data', chunk, chunk.length);
});

readStream.on('end', () => {
    console.log('end', Buffer.concat(data).toString());
});

readStream.on('error', (error) => {
    console.log('error', error);
})
```

```
data <Buffer ed 85 8c ec 8a a4 ed 8a> 8
data <Buffer b8 31 ed 85 8c ec 8a a4> 8
data <Buffer ed 8a b8 32 ed 85 8c ec> 8
data <Buffer 8a a4 ed 8a b8 31 ed 85> 8
data <Buffer 8c ec 8a a4 ed 8a b8 32> 8
data <Buffer ed 85 8c ec 8a a4 ed 8a> 8
data <Buffer b8 31 ed 85 8c ec 8a a4> 8
data <Buffer ed 8a b8 32 ed 85 8c ec> 8
data <Buffer 8a a4 ed 8a b8 31 ed 85> 8
data <Buffer 8c ec 8a a4 ed 8a b8 32> 8
data <Buffer ed 85 8c ec 8a a4 ed 8a> 8
data <Buffer b8 31 ed 85 8c ec 8a a4> 8
data <Buffer ed 8a b8 32 ed 85 8c ec> 8
data <Buffer 8a a4 ed 8a b8 31 ed 85> 8
data <Buffer 8c ec 8a a4 ed 8a b8 32> 8
data <Buffer ed 85 8c ec 8a a4 ed 8a> 8
data <Buffer b8 31 ed 85 8c ec 8a a4> 8
data <Buffer ed 8a b8 32 ed 85 8c ec> 8
data <Buffer 8a a4 ed 8a b8 31 ed 85> 8
data <Buffer 8c ec 8a a4 ed 8a b8 32> 8
data <Buffer ed 85 8c ec 8a a4 ed 8a> 8
data <Buffer b8 31 ed 85 8c ec 8a a4> 8
data <Buffer ed 8a b8 32 ed 85 8c ec> 8
data <Buffer 8a a4 ed 8a b8 31 ed 85> 8
data <Buffer 8c ec 8a a4 ed 8a b8 32> 8
```

청크별로 날라와서 `end 이벤트 등록시` 그 버퍼를 합쳐서 하나의 완전품으로 만든다. 

#### WriteStream

```javascript
const fs = require('fs');

const writeStream = fs.createWriteStream('./readme.txt');
writeStream.on('end', () => {
    console.log('완료')
})
writeStream.write('쓰기 완료1');
writeStream.write('두번째 줄');
writeStream.end();
```

#### Pipe

내부적으로 읽기와 쓰기를 연결해준다. 

- copy 함수가 따로 새로 만들어져서 사용가능하다. 

#### zlib

압축을 할수 있다. 

### Event

미리 이벤트를 만들어 놓고 어떤 행동 발생시 콜백이 발생한다. 

- `addListener` 
- `on`  : addListener 와 같지만 여러번 등록가능하다. 
- `once` : 한번만 실행된다. 
- `listenerCount` : 이벤트가 몇개 달려 있는지 갯수 확인

```javascript
const EventEmitter = require('events');
const myEvent = new EventEmitter();

myEvent.addListener('visit', () => {
    console.log('welcome');    
});

myEvent.on('exit', () => {
    console.log('good bye');
});

myEvent.on('exit', () => {
    console.log('good bye please');
});

myEvent.once('special', () => {
    console.log('special event')
});

myEvent.emit('visit');
myEvent.emit('exit');
myEvent.emit('special');
myEvent.emit('special');

..........
welcome
good bye
good bye please
special event <-- 한번만 실행된걸 볼 수 있다. 
```

#### 이벤트 취소

- `removeAllListeners(이름)` 여러번 등록 했을 경우 다 제거
- `removeListener(이름, callback)` 하나만 제거 가능하다. 어떤 걸 제거 해야 할지 알수 없으므로 콜백을 지정

### 예외처리

- 노드는 `싱글 쓰레드`이므로 오류 발생시 전체에 영향을 끼칠수 있으므로 신중하게 처리
- `throw new Error`
- `try / catch` 로 처리 가능
- `async / await` 도 `try / catch `
- 내장 함수들은 `error` 발생해도 시스템이 중지되진 않는다. 
- `process.on` 으로 시스템 에러가 발생시 중지가 되지 않게 할 수 있다. 
  - `uncaughtExcepetion` 으로 모든 에러를 잡을수 있다. 
  - `process.on` 이벤트 실행이 꼭 보장되지 않는다. 



