# HTML + CSS 스터디 1주차 스터디 정리내용 #2

> 부산에서 매주 진행되는 CSS 초중급 스터디입니다. 
>
> 더 많은 스터디는 [네이버 카페](https://cafe.naver.com/busandev)에서 확인 부탁드립니다. 

저번 시간은 [Flex](http://javaexpert.tistory.com/991) 에 대해서 공부했다. 

Flex로 화면 배치 하는데 불편함이 다소 있다. 그래서 Css Grid 라는 게 나왔다. 

CSS Grid 에 대해서 하나씩 정리해보도록 하겠다. 

이 내용은 [니콜라스님의 CSS 마스터 클래스](https://academy.nomadcoders.co)를 공부하고 정리한 글이다. 

## CSS Grid 가 필요한 이유?

우선 FlexBox 가 부족한 부분이 뭔지 살펴보자. 

만약 여러개의 박스가 들어올 경우 overflow 되면서 배치가 되기 때문에 보기가 안좋을 수 있다. 

`CSS Grid System` 이라고 하는 테이블같은 걸 쉽게 짤 수 있도록 도와준다. 

## CSS Grid Basic

`Grid 시스템`은 가로 세로를 지정해서 가능하다. 

예를 들어보자. 하나의 박스를 `가로 30px, 세로 30px` 으로 지정해서 만들수 있다. 

```html
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        .father {
            display: grid;
            grid-template-columns: 30px 50px;
            grid-template-rows: 30px 12px;
        }

        .box {
            background-color: #f1c40f;
        }
    </style>
</head>
<body>
    <div class="father">
        <div class="box"></div>
        <div class="box"></div>
        <div class="box"></div>
        <div class="box"></div>
        <div class="box"></div>
    </div>
</body>
</html>
```

![1541517517042](1541517517042.png)



## Grid 기능

### Grid-template-rows, Grid-template-columns

만약 여러개로 만든다고 하면 다음과 같이 가능하다. 

- `30x30`
- `50x30`
- `30x12`
- `50x12`

```html
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        .father {
            display: grid;
            grid-template-columns: 30px 50px;
            grid-template-rows: 30px 12px;
            grid-gap: 5px;
        }

        .box {
            background-color: #f1c40f;
        }
    </style>
</head>
<body>
    <div class="father">
        <div class="box"></div>
        <div class="box"></div>
        <div class="box"></div>
        <div class="box"></div>
        <div class="box"></div>
    </div>
</body>
</html>
```

![1541517898405](1541517898405.png)

### Grid-auto-rows

`grid-template-columns` 에 지정된 수를 초과하는 경우 높이를 지정할 수 있다. 

- `Grid-auto-columns` 반대되는 의미로 사용가능하다.

```css
        .father {
            display: grid;
            grid-template-columns: 30px 50px;
            grid-template-rows: 30px 12px;
            grid-gap: 5px;
            grid-auto-rows: 60px;
        }
```

![1541518352043](1541518352043.png)

### Grid-auto-flow

`grid-auto-columns` 나 `grid-auto-rows` 사용시 진행되는 배치를 뜻한다. row 나 column 을 지정 가능하다. 

`flex-direction` 과 비슷한 개념이라고 보면 된다. 

### Grid-template-ares **

문자열로 템플릿을 정한다음 하위 css에 속성을 하나씩 지정해줌

- 문자열로 템플릿 구성

```html
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        .father {
            display: grid;
            grid-auto-rows: 200px;
            grid-gap: 5px;
            grid-template-areas: "header header header"
                "content content sidebar"
                "content content sidebar"
                "footer footer footer"
        }

        .first {
            grid-area: header;
            background-color: #f1c40f;
        }

        .second {
            grid-area: sidebar;
            background-color: #27ae60;
        }

        .third {
            grid-area: footer;
            background-color: #3498db;
        }

        .fourth {
            grid-area: content;
            background-color: #d35400;
        }
    </style>
</head>

<body>
    <div class="father">
        <div class="first"></div>
        <div class="second"></div>
        <div class="third"></div>
        <div class="fourth"></div>
    </div>
</body>

</html>
```

![1541520032893](1541520032893.png)

