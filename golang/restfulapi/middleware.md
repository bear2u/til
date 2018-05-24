# 미들웨어 만들어보기

우선 기본 클로저 형태 먼저 보도록 하자.

```go
package main

import (
    "fmt"
)

func main() {
    numGenerator := generator()
    for i:= 0; i < 5; i++ {
        fmt.Println(numGenerator(), "\t")
    }
}

func generator() func() int {
    var i = 0
     return func() int { // 함수안에 또 함수를 만들고 밖에 변수를 캡쳐해서 사용하고 있다.
        i++
        return i
    }
}

.............
1     
2     
3     
4     
5
```

그럼 미들 웨어를 구현해보자.

```go
package main

import (
    "fmt"
    "net/http"
)

func middleware(handler http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        fmt.Println("Executing middleware before request phase!") //선처리
        // Pass control back to the handler
        handler.ServeHTTP(w, r)
        fmt.Println("Executing middleware after response phase!") //후처리
    })
}

func mainLogic(w http.ResponseWriter, r *http.Request) {
    fmt.Println("Executing mainHandler...")
    w.Write([]byte("OK"))
}

func main() {
    mainLogicHandler := http.HandlerFunc(mainLogic)
    http.Handle("/", middleware(mainLogicHandler))
    http.ListenAndServe(":8000", nil)
}

....................
Executing middleware before request phase!
Executing mainHandler...
```

* http.HandlerFunc\(\) 을 통해 mainLogic을 전달
* ServeHttp 를 사용시 mainLogic 함수를 처리 할 수 있다. 
* http.Handle 함수는 미들웨어를 가져와서 처리

![](/assets/golang_middleware.jpg)

