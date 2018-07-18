# API Versioning 방법

만약 같은 엔드 포인트 주소이지만 버전에 따라 다르게 가져오고 싶다면?

예를 들어 다음과 같은 내용으로 만들었다. 

```
>> Name.java

public class Name {
	private String firstName;
	private String lastName;	
}

```

```
>> Version1


public class PersonV1 {
    private String name;		
}

```

```
>> Version2


public class PersonV2 {
    private Name name;		
}

```

* V1 에서는 String 으로 가져오고 있으며 V2에서는 Name 클래스에서 가져오고 있다. 





