# GRPC2 입문과정

* GRPC는 바이너리 프로토콜을 사용하는 HTTP / 2를 사용
* HTTP/2 에선 헤더압축이 가능하므로 오버 헤드가 적음
* 하나의 연결에서 많은 요청을 다중화 가능
* 엄격한 데이터 입력을 위해 protobufs 사용함
* 요청 / 응답 트랜잭션 대신 요청 또는 응답 스트리밍 가능

아래 이미지는 대표적인 백앤드와 클라이언의 전송 과정을 보여준다. ![](/assets/grpc2_1.png)

그럼 개발 단계는 다음과 같다.

* 서비스 및 메세지에 대한 프로토콜 버퍼 파일을 만든다. 
* 프로토콜 버퍼 파일을 컴파일한다. 
* 생성된 Go 패키지를 사용하여 GRPC 서버를 생성
* 서버와 통신하는 GRPC 클라이언트를 만든다. 

# 개발 시작

우선 디펜시즈 설치 부터 하자.

```
go get google.golang.org/grpc
go get -u github.com/golang/protobuf/protoc-gen-go
```

우선 `protobuf`파일을 만들자.

> transaction.proto 파일 생성

```proto
syntax = "proto3";
package datafiles;

message TransactionRequest {
   string from = 1;
   string to = 2;
   float amount = 3;
}

message TransactionResponse {
  bool confirmation = 1;
}

service MoneyTransaction {
    rpc MakeTransaction(TransactionRequest) returns (TransactionResponse) {}
}
```

그리고 컴파일해서 go 파일로 만들어준다.

```
protoc -I datafiles/ datafiles/transaction.proto --go_out=plugins=grpc:datafiles
```

그럼 transaction.pb.go 파일이 생긴 걸 볼수 있다.

그럼 server/server.go 를 작성해서 내용을 작성해보자.

```go
package main

import (
    "log"
    "net"

    pb "github.com/grpc_example/datafiles"
    "golang.org/x/net/context"
    "google.golang.org/grpc"
    "google.golang.org/grpc/reflection"
)

const (
    port = ":50051"
)

// server is used to create MoneyTransactionServer.
type server struct{}

// MakeTransaction implements MoneyTransactionServer.MakeTransaction
func (s *server) MakeTransaction(ctx context.Context, in *pb.TransactionRequest) (*pb.TransactionResponse, error) {
    log.Printf("Got request for money Transfer....")
    log.Printf("Amount: %f, From A/c:%s, To A/c:%s", in.Amount, in.From, in.To)
    // Do database logic here....
    return &pb.TransactionResponse{Confirmation: true}, nil
}

func main() {
    lis, err := net.Listen("tcp", port)
    if err != nil {
        log.Fatalf("Failed to listen: %v", err)
    }
    s := grpc.NewServer()
    pb.RegisterMoneyTransactionServer(s, &server{})
    // Register reflection service on gRPC server.
    reflection.Register(s)
    if err := s.Serve(lis); err != nil {
        log.Fatalf("Failed to serve: %v", err)
    }
}
```

client/client.go 를 작성해보자.

```go
package main

import (
    "log"

    pb "github.com/grpc_example/datafiles"
    "golang.org/x/net/context"
    "google.golang.org/grpc"
)

const (
    address = "localhost:50051"
)

func main() {
    // Set up a connection to the server.
    conn, err := grpc.Dial(address, grpc.WithInsecure())
    if err != nil {
        log.Fatalf("Did not connect: %v", err)
    }
    defer conn.Close()
    c := pb.NewMoneyTransactionClient(conn)

    // Prepare data. Get this from clients like Frontend or App
    from := "1234"
    to := "5678"
    amount := float32(1250.75)

    // Contact the server and print out its response.
    r, err := c.MakeTransaction(context.Background(), &pb.TransactionRequest{From: from,
        To: to, Amount: amount})
    if err != nil {
        log.Fatalf("Could not transact: %v", err)
    }
    log.Printf("Transaction confirmed: %t", r.Confirmation)
}
```

그리고 서버 따로 클라이언 따로 돌려보자.

클라이언트쪽에서는

```
Transaction confirmed: true
```

서버쪽에서는

```
Amount: 1250.750000, From A/c:1234, To A/c:5678
```

을 나오는 걸 볼수 있다.

패키지 구조는 다음과 같다.

![](/assets/grpc2_2.png)

