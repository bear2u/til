# CSS

아톰 추천 플러그인

* emmet
* pigments
* minimap
* color-picker
* gitplus
* prettier

theme 추천 플러그인

* Glacier Dark UI Theme

왼쪽 Theme 클릭 후 Glacier 로 변경

왼쪽 Packages에 가서 prettier-atom 에 Setting -&gt; Format Files On Save 선택

# HTML

meta \( 헤더에 저장된다. \)

* charset -&gt; 인코딩
* description -&gt; 검색화면에서 content 설명이 나옴

semantic 의미가 있는 뜻 \(예를 들어 &lt;h1&gt;&lt;/h1&gt; \)

non-semantic 의미가 없음 \(&lt;div&gt; , &lt;span&gt;

# Css

`Box ( Margin, Border, Padding, Content )`

Block -&gt; 옆에 다른 엘리먼트가 위치할 수 없다.

Inline-Block -&gt; 옆에 올수 있다. 서로서로 붙는것

Inline -&gt; 박스의 모든 Property 의 모든 속성을 삭제를 한다. width,height 가 삭제됨

# position

static,

fixed 고정

absolute, \(부모가 relative 가 아닐 경우 최상위 body 위치를 잡음\)

relative

# PSEUDO-Selector \(class, id를 쓰지 않고 선택하는 방법\)

```
input[type=submit] {
    background-color: red;
}

......
<input type="submit">
```

```
.box:first-child 
.box:nth-child(2)
.box:nth-child(3n+2)
.box:last-child
```

# Css State

```
box:hover
box:active
box:focus
box:
```

# Transitions \(hover 와 비슷한 효과\)

Animation

Transformations

Media query \(브라우저 크기를 알아서 자동 조절함\)

천천히 바뀌는 애니메이션 효과를 말한다.

```css
<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title>trasition</title>
  </head>
  <style>
    .box{
      background-color: green;
      color:white;
      transition:all .5s ease-in-out;
    }
    .box:active{
      background-color: red;
      color: blue;
    }
  </style>
  <body>
    <span class="box">
      Text
    </span>
  </body>
</html>
```

# Transformations

도형을 만들어서 회원 하고 비틀고 그런 작업들을 뜻한다.

```
https://developer.mozilla.org/en-US/docs/Web/CSS/transform
```

```css
<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title>trasition</title>
  </head>
  <style>
    .box{
      width: 100px;
      height: 100px;
      background: red;
      /* transform: rotate(20deg); */
      /* transform: translateX(10px) rotate(10deg) translateY(5px); */
      transition: transform .5s ease-in-out;
    }
    .box:hover{
      transform: rotate(1turn);
    }

  </style>
  <body>

    <div class="box"></div>
  </body>
</html>
```

# Animations

from - to 2단계로도 사용이 가능하다.

```css
<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title>trasition</title>
  </head>
  <style>
    .box{
      width: 50px;
      height: 50px;
      background: red;
      animation: 1.5s scaleAndRotateSquare infinite ease-in-out;
    }
    @keyframes scaleAndRotateSquare {
      0%{ //from
        transform: none;
      }
      50%{
        transform: rotate(1turn) scale(.5, .5);
        color:white;
      }
      100%{ //to 로 대신해서 쓸수 있다.
        transform: none;
      }
    }

  </style>
  <body>

    <div class="box">11</div>
  </body>
</html>
```

# Media Queries

화면 크기에 맞춘 상태에 따라 정의 된 상태가 변할 수 있는 방법을 뜻한다. 

```
<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title>trasition</title>
  </head>
  <style>
    body{
      background-color: green;
    }
    @media screen and (min-width:320px) and (max-width: 640px){
      body{
        //화면을 늘리면 green 줄이면 blue로 변경
        background-color: blue;
      }
    }

  </style>
  <body>
  </body>
</html>
```



