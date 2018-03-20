# 하루쿠 서버 체크

[https://devcenter.heroku.com/articles/getting-started-with-nodejs\#deploy-the-app](https://devcenter.heroku.com/articles/getting-started-with-nodejs#deploy-the-app)

동적 포트 체크

1. engines 등록

2. package.json 에 엔진등록

3. \`\`\`

   # package.json

3. 스크립트 체크

```
engines": {
       "node": "8.1.1",
       "npm": "5.0.3"
     },
```

# package.json

"scripts": {  
    "test": "node index.js"  
},

```
4. gitignore 등록
```

node\_modules

```
Express Prot 동적 체크
```

const PORT = process.env.PORT  


