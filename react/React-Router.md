# React Router 사용

Redux 보일러플레이트 설치 주소

> [https://github.com/StephenGrider/ReduxSimpleStarter](https://github.com/StephenGrider/ReduxSimpleStarter)

라우터 설치

```
npm install --save react-router-dom@4.2.2
```

선언부분에

```
import { BrowserRouter, Route } from 'react-router-dom';
```

본문에

```
<BrowserRouter>
  <div>
    <Route path="/" component={PostsIndex} />
  </div>
</BrowserRouter>
```

ajax 와 redux promise 설치는

```
npm install --save axios redux-promise
```



