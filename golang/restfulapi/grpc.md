# Protobuf , GRPC 사용해보기



윈도우의 경우

[https://github.com/google/protobuf/releases/download/v3.3.0/protoc-3.3.0-win32.zip](https://github.com/google/protobuf/releases/download/v3.3.0/protoc-3.3.0-win32.zip)



혹시 protoc-gen-go 가 없을 경우

```
go get -u github.com/golang/protobuf/protoc-gen-go
```

일단 먼저 `person.proto` 를 작성해보자.

```go
syntax = "proto3";
package protofiles;

message Person {
  string name = 1;
  int32 id = 2;  // Unique ID number for this person.
  string email = 3;

  enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }

  message PhoneNumber {
    string number = 1;
    PhoneType type = 2;
  }

  repeated PhoneNumber phones = 4;
}

// Our address book file is just one of these.
message AddressBook {
  repeated Person people = 1;
}
```

패키지 부분 확인 

```
package protofiles;
```

```
// 컴파일
protoc --go_out=. person.proto // or (*.proto)
```

그럼 동일한 이름의 go 파일이 생긴다. 자동 컴파일되서 만들어짐

`person.pb.go`

그리고 다른 폴더를 만들어서 main.go 를 만들어서 참조를 해보자. 

```go
package main

import (
	"fmt"
	"github.com/golang/protobuf/proto"
	pb "../protofiles" //금방 컴파일 된 파일
)

func main() {
	p := &pb.Person{
		Id:    1234,
		Name:  "Roger F",
		Email: "rf@example.com",
		Phones: []*pb.Person_PhoneNumber{
			{Number: "555-4321", Type: pb.Person_HOME},
		},
	}

	p1 := &pb.Person{}
	body, _ := proto.Marshal(p)
	_ = proto.Unmarshal(body, p1)
	fmt.Println("Original struct loaded from proto file:", p, "\n")
	fmt.Println("Marshaled proto data: ", body, "\n")
	fmt.Println("Unmarshaled struct: ", p1)
}
```

출력 결과는 다음과 같다. 

```
Original struct loaded from proto file: name:"Roger F" id:1234 email:"rf@example.com" phones:<number:"555-4321" type:HOME >  

Marshaled proto data:  [10 7 82 111 103 101 114 32 70 16 210 9 26 14 114 102 64 101 120 97 109 112 108 101 46 99 111 109 34 12 10 8 53 53 53 45 52 51 50 49 16 1] 

Unmarshaled struct:  name:"Roger F" id:1234 email:"rf@example.com" phones:<number:"555-4321" type:HOME > 
```

![](/assets/grpc2.png)

하지만 2번째 줄에는 이진 바이트를 직렬화 해서 직관적으로 알아보기 힘든 상태이다. 

이 부분을 JSON 으로 바꿀수 있다. 

```go
package main

import (
	"fmt"
	"github.com/golang/protobuf/proto"
	pb "../protofiles" //금방 컴파일 된 파일
)

func main() {
  p := &pb.Person{
    Id:    1234,
    Name:  "Roger F",
    Email: "rf@example.com",
    Phones: []*pb.Person_PhoneNumber{
      {Number: "555-4321", Type: pb.Person_HOME},
    },
  }
  body, _ := json.Marshal(p)
  fmt.Println(string(body))
}
```

출력은 다음과 같다. 

```
{"name":"Roger F","id":1234,"email":"rf@example.com","phones":[{"number":"555-4321","type":1}]}
```

그럼 이 결과물을 다른 클라이언트에서 쉽게 로드하고 사용이 가능하다. 

> 그러면 JSON 대신 이 프로토콜버퍼\(`protobuf`\) 을 사용하면 어떤 이점이 있나?

프로토콜 버퍼는 두개이상의 백엔드 시스템이 적은 오버헤드로 서로 통신하기 위한 것입니다.

바이너리의 크기가 텍스트보다 작으므로 프로토콜 마샬링 된 데이터의 크기가 JSON 보다 작다는 것이다. 



#### 그러나 프로토콜 버퍼는 데이터 형식 일뿐입니다.

#### 우리가 의사 소통을하지 않으면 중요성이 없습니다.

#### 그래서 여기서 프로토콜 버퍼는 RPC의 형태로 두 최종 시스템간에 메시지를 전달하는 데 사용됩니다.

#### 우리는 RPC가 작동하는 방법을 보았고 이전 장에서 RPC 클라이언트와 서버를 만들었습니다.

#### 이제 우리는마이크로 서비스 통신을 확장하기 위해 프로토콜 버퍼와 함께

#### **Google 원격 프로 시저 호출**\(**GRPC**\)을 사용하기위한 지식을 확장하려고합니다 .

#### 서버와 클라이언트는이 경우 프로토콜 버퍼 형식으로 서로 통신 할 수 있습니다.



