# Static Filtering \(정적 필터링\)

* 결과값에 만약 변수를 안보이게 필터링을 하고 싶은 경우

만약 이러한 결과값이 있다고 가정하자.

```
[
    {
        "field1": "value1",
        "field2": "value2",
        "field3": "value3"
    },
    {
        "field1": "value11",
        "field2": "value22",
        "field3": "value33"
    }
]
```

소스는 다음과 같을 것이다.

```java
package com.example.restfulwebservices.filtering;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

public class SomeBean {
    private String field1;
    private String field2;    
    private String field3;

    public SomeBean(String field1, String field2, String field3) {
        super();
        this.field1 = field1;
        this.field2 = field2;
        this.field3 = field3;
    }


    public String getField1() {
        return field1;
    }
    public void setField1(String field1) {
        this.field1 = field1;
    }
    public String getField2() {
        return field2;
    }
    public void setField2(String field2) {
        this.field2 = field2;
    }
    public String getField3() {
        return field3;
    }
    public void setField3(String field3) {
        this.field3 = field3;
    }    
}
```

만약 field3를 결과에서 빼고 싶은 경우에는

```java
//    @JsonIgnore
    private String field3;
```

그리고 클래스자체에 필터링을 걸수도 있다.

```java
@JsonIgnoreProperties(value= {"field1","field2"}) //<-- 이 부분을 주의해서 보자.
public class SomeBean {
    private String field1;
    private String field2;

    //응답에서 무시된다. 
//    @JsonIgnore
    private String field3;

    public SomeBean(String field1, String field2, String field3) {
        super();
        this.field1 = field1;
        this.field2 = field2;
        this.field3 = field3;
    }


    public String getField1() {
        return field1;
    }
    public void setField1(String field1) {
        this.field1 = field1;
    }
    public String getField2() {
        return field2;
    }
    public void setField2(String field2) {
        this.field2 = field2;
    }
    public String getField3() {
        return field3;
    }
    public void setField3(String field3) {
        this.field3 = field3;
    }        
}

...........................

[
    {
        "field3": "value3"
    },
    {
        "field3": "value33"
    }
]
```





