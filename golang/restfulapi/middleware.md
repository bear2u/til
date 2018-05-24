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

# 그럼 미들웨어 실전으로 들어가보자.

다음의 조건들을 가진 미들웨어을 만들것이다.

* POST 메소드의 경우에만 실행된다. 
* 그리고 컨덴츠 유형이 JSON 인지 확인 
* 응답 쿠기에 Server Time 타임 스탬프를 추가

```go
package main

import (
    "fmt"
    "net/http"
    "encoding/json"
)

type city struct {
    Name string
    Area uint64
}

func mainLogic(w http.ResponseWriter, r *http.Request) {
    if r.Method == "POST" {
        var tempCity city
        decoder := json.NewDecoder(r.Body)
        err := decoder.Decode(&tempCity)
        if err != nil {
            panic(err)
        }

        defer r.Body.Close()
        fmt.Printf("Got %s city with area of %d sq miles\n", tempCity.Name, tempCity.Area)
        w.WriteHeader(http.StatusOK)
        w.Write([]byte("201 - Created"))
    } else {
        w.WriteHeader(http.StatusMethodNotAllowed)
        w.Write([]byte("405 - Method Not Allowed"))
    }
}

func main() {
    //mainLogicHandler := http.HandleFunc(mainLogic)
    http.HandleFunc("/city", mainLogic)
    http.ListenAndServe(":8000", nil)
}
```

```
curl -H "Content-Type: application/json" -X POST http://localhost:8000/city -d '{"name":"New York", "area":304}'

curl -H "Content-Type: application/json" -X POST http://localhost:8000/city -d '{"name":"Boston", "area":89}'
```

위에 내용을 실행해보면

콘솔에는 아래와 같이 찍힐것이다.

```
Got New York city with area of 304 sq miles
Got Boston city with area of 89 sq miles
```

웹에는

```
201 - Created
201 - Created
```

그럼 타임스탬프 찍는 코드는 다음과 같다.

```go
package main

import (
    "encoding/json"
    "log"
    "net/http"
    "strconv"
    "time"
)
type city struct {
    Name string
    Area uint64
}
// Middleware to check content type as JSON
func filterContentType(handler http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        log.Println("Currently in the check content type middleware")
        // Filtering requests by MIME type
        if r.Header.Get("Content-type") != "application/json" {
            w.WriteHeader(http.StatusUnsupportedMediaType)
            w.Write([]byte("415 - Unsupported Media Type. Please send JSON"))
            return
        }
        handler.ServeHTTP(w, r)
    })
}
// Middleware to add server timestamp for response cookie
func setServerTimeCookie(handler http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        handler.ServeHTTP(w, r)
        // Setting cookie to each and every response
        cookie := http.Cookie{Name: "Server-Time(UTC)", Value: strconv.FormatInt(time.Now().Unix(), 10)}
        http.SetCookie(w, &cookie)
        log.Println("Currently in the set server time middleware")
    })
}
func mainLogic(w http.ResponseWriter, r *http.Request) {
    // Check if method is POST
    if r.Method == "POST" {
        var tempCity city
        decoder := json.NewDecoder(r.Body)
        err := decoder.Decode(&tempCity)
        if err != nil {
            panic(err)
        }
        defer r.Body.Close()
        // Your resource creation logic goes here. For now it is plain print to console
        log.Printf("Got %s city with area of %d sq miles!\n", tempCity.Name, tempCity.Area)
        // Tell everything is fine
        w.WriteHeader(http.StatusOK)
        w.Write([]byte("201 - Created"))
    } else {
        // Say method not allowed
        w.WriteHeader(http.StatusMethodNotAllowed)
        w.Write([]byte("405 - Method Not Allowed"))
    }
}
func main() {
    mainLogicHandler := http.HandlerFunc(mainLogic)
    http.Handle("/city", filterContentType(setServerTimeCookie(mainLogicHandler)))
    http.ListenAndServe(":8000", nil)
}
```

```
http.Handle("/city", filterContentType(setServerTimeCookie(mainLogicHandler)))
// 이부분이 조금 체인형식이 복잡하다. 
```

체이닝이 좀 다소 복잡하게 들어가는 편이다. 

그래서 alice 를 사용해서 나눌수 있다. 

```go
go get github.com/justinas/alice
```

```go
import (
    "encoding/json"
    "github.com/justinas/alice"
    "log"
    "net/http"
    "strconv"
    "time"
)

....


func main() {
    mainLogicHandler := http.HandlerFunc(mainLogic)
    chain := alice.New(filterContentType, setServerTimeCookie).Then(mainLogicHandler)
    http.Handle("/city", chain)
    http.ListenAndServe(":8000", nil)
}
```



