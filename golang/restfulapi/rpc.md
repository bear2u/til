# JSON RPC 만들기

![](/assets/golang_rpc.png)

먼저 서버를 작성해보자. 

```go
package main
import (
    "log"
    "net"
    "net/http"
    "net/rpc"
    "time"
)
type Args struct{}
type TimeServer int64
func (t *TimeServer) GiveServerTime(args *Args, reply *int64) error {
    // Fill reply pointer to send the data back
    *reply = time.Now().Unix()
    return nil
}
func main() {
    // Create a new RPC server
    timeserver := new(TimeServer)
    // Register RPC server
    rpc.Register(timeserver)
    rpc.HandleHTTP()
    // Listen for requests on port 1234
    l, e := net.Listen("tcp", ":1234")
    if e != nil {
        log.Fatal("listen error:", e)
    }
    http.Serve(l, nil)
}
```

클라이언트 쪽은 다음과 같다. 

```go
package main
import (
    "log"
    "net/rpc"
)
type Args struct {
}
func main() {
    var reply int64
    args := Args{}
    client, err := rpc.DialHTTP("tcp", "localhost"+":1234")
    if err != nil {
        log.Fatal("dialing:", err)
    }
    err = client.Call("TimeServer.GiveServerTime", args, &reply)
    if err != nil {
        log.Fatal("arith error:", err)
    }
    log.Printf("%d", reply)
}
```

클라이언트가 서버접속시 rpc로 해당 함수를\( `TimeServer.GiveServerTime` \) 실행합니다. 

이 기능은 클라이언트와 서버쪽이 Go로 작성될 경우에만 가능하다. 

그래서 보통 여러서비스에서 RPC서버를 사용할려면 JSON Rpc over http 를 정의 해야 한다. 

그럼 클라이언트쪽에서 JSON 을 보내면 JSON을 가져올 수 있습니다.

