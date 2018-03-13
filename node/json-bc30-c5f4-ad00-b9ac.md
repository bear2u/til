# 배열을 JSON 형태로 변경처리 삽질

모바일\(안드 또는 아퐁\) 에서 서버쪽 JSON 을 호출시 결과문 형태가 다음과 같이 불러오길 원한다.

우선 요구사항은 다음과 같다고 한다.

```js
{ code: 200,
  stars:
   { '-2147290607': //ID
       //각각의 array value 값...
      { DefinitionId: 193041,
        ResourceId: -2147290607,
        AssetId: 193041 },
     '-2147290603': //ID
      { DefinitionId: 193042,
        ResourceId: -2147290603,
        AssetId: 193042 } 
   } 
}
```

흠..쉬워 보이는 형태이다. 그래서 점심먹고 아무 생각없이 막코딩으로 짜본다.

```js
var results = [];
results.push({code : 200});

var data =
    [
        {
            "DefinitionId": 193041,
            "ResourceId": -2147290607,
            "AssetId": 193041
        },
        {
            "DefinitionId": 193042,
            "ResourceId": -2147290603,
            "AssetId": 193042
        }
    ];

results.push(data);
console.log(results);

============================================

[ { code: 200 },
  [ { DefinitionId: 193041,
      ResourceId: -2147290607,
      AssetId: 193041 },
    { DefinitionId: 193042,
      ResourceId: -2147290603,
      AssetId: 193042 } ] 
]
```

흠..두번째에 배열마다 아이디를 부여를 해야 한다..

```js
var array = [];

data.forEach( val => {
    array[val.ResourceId] = val;
});

console.log(array);
======================
[ '-2147290607': { DefinitionId: 193041,
    ResourceId: -2147290607,
    AssetId: 193041 },
  '-2147290603': { DefinitionId: 193042,
    ResourceId: -2147290603,
    AssetId: 193042 } ]
```

그리고 최종 성공 code 까지 넣으면 다음과 같다.

```js
var results = {code : 200};

var array = [];

data.forEach( val => {
    array[val.ResourceId] = val;
});

results['stars'] = array;

console.log(results);

===========================
{ code: 200,
  stars:
   [ '-2147290607': { DefinitionId: 193041,
       ResourceId: -2147290607,
       AssetId: 193041 },
     '-2147290603': { DefinitionId: 193042,
       ResourceId: -2147290603,
       AssetId: 193042 } ] }
```

하지만 이제 화면 출력할려면 JSON형태로 출력해야 한다.

```js
console.log(JSON.stringify(results));

==================================
{"code":200,"stars":[]} //안에 있던 내부 객체들이 안보인다...
```

왜 그런지 검색해보니 나 말고도 자주 이런 오류를 겪나 보다.

[https://stackoverflow.com/questions/4425289/javascript-associative-array-to-json](https://stackoverflow.com/questions/4425289/javascript-associative-array-to-json)

즉 배열에는 숫자키만 있는 항목만 있어야 한다고 한다. 즉 배열을 JSON으로 변경시 프로세스는 숫자속성만 고려한다나 어쩐다나..

뭐 암튼 안되는 것 같다.

그래서 JSON 으로 변경 한 후에 해보니 정상적으로 출력은 된다.

최종 소스는 다음과 같다.

```js
var data =
    [
        {
            "DefinitionId": 193041,
            "ResourceId": -2147290607,
            "AssetId": 193041
        },
        {
            "DefinitionId": 193042,
            "ResourceId": -2147290603,
            "AssetId": 193042
        }
    ];

var array = {};
data.forEach(val => array[val.ResourceId] = val);

//JSON으로 변경
//이렇게 해야하는 걸까?? 흠...

var obj = JSON.parse(JSON.stringify(array)); 

var results = {code : 200};

results["stars"] = obj;

console.log( JSON.stringify(results) );
=============================================================================
{"code":200,"stars":{"-2147290607":{"DefinitionId":193041,"ResourceId":-2147290607,"AssetId":193041},"-2147290603":{"DefinitionId":193042,"ResourceId":-2147290603,"AssetId":193042}}}
```



