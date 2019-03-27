배경 이미지에는 여러가지 설정이 가능합니다. 아래의 세 가지 속성을 좀 더 자세히 살펴봅시다.

# background-repeat

`background-repeat`는 이미지를 반복시킬 것인지 아닐 것인지, 그리고 반복시킨다면 어떤 방식으로 반복시킬 것인지 정해주는 속성입니다. 여기에는 우리가 배운 `repeat`, `no-repeat` 외에도 다양한 옵션이 있습니다.

```css
/* 반복하지 않음 */
background-repeat: no-repeat;

/* 가로 방향으로만 반복 */
background-repeat: repeat-x;

/* 세로 방향으로만 반복 */
background-repeat: repeat-y;

/* 가로와 세로 모두 반복 */
background-repeat: repeat;

/* 반복할 수 있는 만큼 반복한 뒤, 남는 공간은 이미지 간의 여백으로 배분 */
background-repeat: space;

/* 반복할 수 있는 만큼 반복한 뒤, 남는 공간은 이미지 확대를 통해 배분 */
background-repeat: round;
```

- HTML
- CSS

```html
<!DOCTYPE html>
<html>
<head>
  <title>background-repeat</title>
  <meta charset="utf-8">
  <link rel="stylesheet" href="css/styles.css">
</head>
<body>

    <div id="div1">
    </div>

    <div id="div2">
    </div>

    <div id="div3">
    </div>

    <div id="div4">
    </div>

    <div id="div5">
    </div>

    <div id="div6">
    </div>

</body>
</html>
```

결과 확인

# background-size

`background-size`는 배경 이미지의 사이즈를 정해주는 속성입니다.

```css
/* 원래 이미지 사이즈대로 출력 */
background-size: auto;

/* 화면을 꽉 채우면서, 사진 비율을 유지 */
background-size: cover;

/* 가로, 세로 중 먼저 채워지는 쪽에 맞추어서 출력 */
background-size: contain;

/* 픽셀값 지정 (가로: 30px, 세로: 50px로 설정) */
background-size: 30px 50px;

/* 퍼센트값 지정 (가로: 부모 요소 width의 60%, 세로: 부모 요소 height의 70%로 설정) */
background-size: 60% 70%;
```

- HTML
- CSS

```html
<!DOCTYPE html>
<html>
<head>
  <title>background-size</title>
  <meta charset="utf-8">
  <link rel="stylesheet" href="css/styles.css">
</head>
<body>

    <div id="div1">
    </div>

    <div id="div2">
    </div>

    <div id="div3">
    </div>

    <div id="div4">
    </div>

    <div id="div5">
    </div>

    <div id="div6">
    </div>

    <div id="div7">
    </div>

</body>
</html>
```

결과 확인

# background-position

`background-position`은 배경 이미지의 위치를 정해주는 속성입니다.

```css
/* 단어로 지정해주기 (가로: left, center, right, 세로: top, center, bottom) */
/* 아래와 같은 총 9개의 조합이 가능 */
background-position: left top;
background-position: left center;
background-position: left bottom;
background-position: right top;
background-position: right center;
background-position: right bottom;
background-position: center top;
background-position: center center;
background-position: center bottom;

/* 퍼센트로 지정해주기 (가로: 전체 width의 25% 지점, 세로: 전체 height의 75% 지점 ) */
background-position: 25% 75%;

/* 픽셀로 지정하기 (가로: 가장 왼쪽 가장자리에서부터 오른쪽으로 100px 이동한 지점, 세로: 가장 상단 가장자리에서 아래로 200px 이동한 지점) */
background-position: 100px 200px;
```

- HTML
- CSS

```html
<!DOCTYPE html>
<html>
<head>
  <title>background-position</title>
  <meta charset="utf-8">
  <link rel="stylesheet" href="css/styles.css">
</head>
<body>

    <div id="div1">
    </div>

    <div id="div2">
    </div>

    <div id="div3">
    </div>

    <div id="div4">
```