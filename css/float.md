# Float

`float:left` 를 하면 왼쪽기준으로 좀 띄워준다. 

다음 요소는 인라인 자식은 띄운 엘리먼트에는 접근 하지 못한다. 
![](2019-03-29-17-46-00.png)

![](2019-03-29-17-53-51.png)

```
<!DOCTYPE html>
<html>

<head>
  <title>4차 산업혁명의 시대, 소프트웨어 교육은 필수</title>
  <meta charset="utf-8">
  <link rel="stylesheet" href="css/styles.css">
  <link href="https://fonts.googleapis.com/css?family=Roboto" rel="stylesheet">
</head>

<body>
  <h1 class="title">4차 산업혁명의 시대, 소프트웨어 교육은 필수</h1>
  <div class="content">
    <img src="images/codeit.jpg" id="codeit"/>  
    <p>코드잇 강영훈 대표 '소프트웨어 강국되려면 컴퓨터적 사고방식 필요해'</p>

    <p>"누구나 프로그래밍을 배워야한다. 사고하는 방법을 가르쳐주기 때문이다." 故 스티브잡스 애플 창업자가 했던 말이다.</p>

    <p>소프트웨어는 4차 산업혁명의 흐름이다. 인공지능, IoT, 빅데이터, 3D프린팅, 가상 현실, 증강현실 등 모두 소프트웨어와 긴밀한 연관성을 갖고 있는 분야이다.</p>

    <p>미래의 흐름에 발맞춰 정부는 2018년부터 초, 중, 고등학교에서 소프트웨어 교육을 전면 실시할 계획이다. 이에 앞서 지난해와 올해 미래부, 교육부에서 900개의 연구기관 및 선도학교를 정해 교육하고 있다. 또한 정부는 올해 8개 소프트웨어 중심 대학을 선정해 연간 최대 20억원을 지원하는 등 산업혁명에 필요한 인력 양성에 주력하기로 했다.</p>

    <div class="link">
      <a href="http://www.edaily.co.kr/news/NewsRead.edy?SCD=JE41&newsid=02587926612750928&DCD=A00504&OutLnkChk=Y" target="_blank">
        인공지능의 발달, 어떻게 대처해야 하나
      </a>
    </div>
          
    <p>소프트웨어가 미래 산업에 필수적인 부분이지만, 현재 국내 환경에 적합한 소프트웨어 교육 콘텐츠는 많지 않다. 대부분의 학원이 자격증을 위한 주입식 소프트웨어 교육을 하기 때문이다.</p>

      <img src="images/youtoo.jpg" id="youtoo"/>  
    <p>이에 강영훈 대표는 한국 최고의 소프트웨어 교육을 제공하고자, 미국 실리콘밸리의 소프트웨어 신기술과 아이비리그 컴퓨터과학과의 교육 체계를 혼합해 '온라인 프로그래밍 스쿨'을 만들었다. 코드잇은 이러한 공로를 인정받아 창업진흥원과 스마트벤처창업학교의 교육 콘텐츠 부문에서 '우수 스타트업'으로 선정된 바 있다.</p>

    <p>코드잇은 컴퓨터적 사고력을 기르는 동영상 강의뿐만 아니라, 실력을 늘리기 위해 꼭 필요한 과제와 1:1 과제 첨삭 시스템까지 갖췄으며, 사이트에서 직접 코드를 실행하고 수강생끼리 코드를 비교해볼 수 있는 코드 공유 시스템도 제공하고 있다.</p>

    <p>한편, 강영훈 대표는 "한국이 소프트웨어 강국이 되려면 자격증이나 단순 주입식 암기와 같은 일차원적인 교육에서 벗어나, 스스로 컴퓨터적으로 사고하는 능력을 키워주는 높은 차원의 교육이 필요하다"고 전했다.</p>    
  </div>
</body>

</html>

```
```
body {
  margin: 0;
  font-family: 'Roboto', sans-serif;
}

/* 제목 */
.title {
  text-align: center;
  border-bottom: 1px solid #eee;
  margin: 0;
  padding-top: 25px;
  padding-bottom: 25px;
}

/* 기사 본문 */
.content {
  font-size: 18px;
  width: 70%;
  max-width: 900px;
  margin: 50px auto;
}

/* 링크 */
.link {
  border-top: 1px solid #eee;
  border-bottom: 1px solid #eee;
  font-weight: bold;
  padding: 15px 10px;
}

.link a {
  color: #222;
  text-decoration: none;
}

/* 코드잇 광고 */
#codeit {
    width:180px;
    float:left;
    margin-right:15px;
    margin-bottom:15px;
}

/* 야너두 광고 */
#youtoo {
    width:180px;
    float:right;
    margin-left:15px;
    margin-bottom:15px;
}
```

## 멀티 floating

![](2019-03-29-17-55-45.png)

공간을 하나씩 왼쪽(또는 오른쪽)부터 채워서 배치하는 레이아웃 방법

### Clear

`clear:left`로 처리

해당 엘리먼트의 왼쪽에 없도록 해서 왼쪽에 배치