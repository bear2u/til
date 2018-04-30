# Truffle webpack 설치시 await 및 arrow function 추가

```
truffle unbox webpack
```

실행시 arrow function 및 aysnc / await 가 지원이 안된다. 

그러면 플러그인으로 추가가 가능하다. 

우선 arrow function 의 경우

```
#  .babelrc
{
    "plugins": ["transform-es2015-arrow-functions"]
}
```

async await 추가시

```
npm i babel-plugin-async-to-promises
```

```
#  .babelrc
{
    "plugins": [
        "transform-es2015-arrow-functions",
        "async-to-promises"
    ]
}
```

참조링크

[https://skout90.github.io/2018/03/03/Frontend/2.%EC%9B%B9%ED%8C%A9-%EB%B0%94%EB%B2%A8\(babel\)-%EC%97%B0%EB%8F%99/](https://skout90.github.io/2018/03/03/Frontend/2.%EC%9B%B9%ED%8C%A9-%EB%B0%94%EB%B2%A8%28babel%29-%EC%97%B0%EB%8F%99/)



