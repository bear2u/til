# Http Server

- `80`,` 443` 포트는 기본 포트이므로 생략 가능
- createServer는 기본 이벤트가 등록되어 있다.
- `req` : request
- `res` : response

```javascript
const http = require('http');

http.createServer((req, res) => {
    console.log('서버 실행');
    res.write("Hello world");
    res.end();
}).listen(8080, () => {
    console.log('8080번 포트에서 시작됩니다.');
});
```

## html 파일 읽어 오기

- fs 모듈을 사용해서 읽어올 수 있다. 

```javascript
const http = require('http');
const fs = require('fs');

const server = http.createServer((req, res) => {
    console.log('서버 실행');
    fs.readFile('./index.html', (err, data) => {
        if (err) {
            throw err;
        }
        res.end(data);
    });
}).listen(8080);

server.on('listening', () => {
    console.log('8080번 포트에서 서버 대기중')
});

server.on('error', (error) => {
    console.error(error);
});
```

