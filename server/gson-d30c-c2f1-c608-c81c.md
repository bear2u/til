# Gson으로 특수 케이스 파싱해보기

만약 이름\(키값\)이 있는 배열로 받을 경우 처리 방법에 대해서 배워보자.

샘플 데이터는 다음과 같다.

```js
{
  "calendar": {
    "2016-06-10": [
      {
        "time": "10h00m",
        "title": "PROVA P2",
        "description": "LP / RED / ED.FIS - 80 E 90",
        "color": "#990000"
      }
    ],
    "2016-06-11": [
      {
        "time": "10h00m",
        "title": "SIMULADO",
        "description": "LOREM PSIUM DOLOR LOREM",
        "color": "#009900"
      },
      {
        "time": "11h00m",
        "title": "CONSELHO DE CLASSE",
        "description": "LOREM PSIUM DOLOR",
        "color": "#009900"
      }
    ]
  },
  "error": false
}
```

이걸 Gson으로 변경시

Resonse 형태는

```js
public class CalendarResponse {
  @SerializedName("calendar")
  Map<String, List<Entry>> entries;

  @SerializedName("error")
  private boolean error;
}
```

그리고 내부 Entry 이면

```js
public class Entry {
  @SerializedName("time")
  private String time;

  @SerializedName("title")
  private String title;

  @SerializedName("description")
  private String description;

  @SerializedName("color")
  private String color;
}
```

이렇게 map, list 형태로 파싱된다.

