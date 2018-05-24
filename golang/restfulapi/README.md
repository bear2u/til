# 고랭 RestfulApi 개발 도전기

기본 서버 올려보기

```go
package main

import (
    "io"
    "log"
    "net/http"
)

func myServer(w http.ResponseWriter, req *http.Request) {
    io.WriteString(w, "hello world\n")
}

func main() {
    http.HandleFunc("/", myServer)
    log.Fatal(http.ListenAndServe(":8000", nil))
}

// localhost:8000
hello world
```

랜덤으로 보여주는 코드

```go
package main
import (
    "fmt"
    "math/rand"
    "net/http"
)
// CustomServeMux is a struct which can be a multiplexer
type CustomServeMux struct {
}
// This is the function handler to be overridden
func (p *CustomServeMux) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    if r.URL.Path == "/" {
        giveRandom(w, r)
        return
    }
    http.NotFound(w, r)
    return
}
func giveRandom(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Your random number is: %f", rand.Float64())
}
func main() {
    // Any struct that has serveHTTP function can be a multiplexer
    mux := &CustomServeMux{}
    http.ListenAndServe(":8000", mux)
}
```

주의 해야 볼 건 `CustomServeMux` 라는 `struct`를 만들어서 여기에서 라우팅 관리를 할 예정이다.

```go
package main

import (
    "fmt"
    "net/http"
    "math/rand"
)

// CustomServeMux is a struct which can be a multiplexer
type CustomServeMux struct {
}
// This is the function handler to be overridden
func (p *CustomServeMux) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    if r.URL.Path == "/" {
        giveRandom(w, r)
        return
    }
    http.NotFound(w, r)
    return
}
func giveRandom(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Your random number is: %f", rand.Float64())
}
func main() {
    // Any struct that has serveHTTP function can be a multiplexer
    mux := http.NewServeMux()
    mux.HandleFunc("/randomFloat", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintln(w, rand.Float64())
    })
    mux.HandleFunc("/randomInt", func(w http.ResponseWriter, r *http.Request){
        fmt.Fprintln(w, rand.Intn(100))
    })

    http.ListenAndServe(":8000", mux)
}
```

라우팅을 몇개 추가한 케이스이다.

```
curl -X GET http://localhost:8000/randomFloat
curl -X GET http://localhost:8000/randomInt
```

그럼 라우팅 처리를 라이트하게 만들어진 라이버러리를 가져와서 사용을 해보자.

```
go get github.com/julienschmidt/httprouter
```

라우팅을 붙인 소스이다.

서버에서 command를 직접 실행해서 output을 가져올 수 있다.

```go
package main

import (
    "net/http"
    "fmt"
    "os/exec"
    "bytes"
    "log"
    "github.com/julienschmidt/httprouter"
)

func getCommandOutput(command string, arguments ...string) string {
    cmd := exec.Command(command, arguments...)
    var out bytes.Buffer
    var stderr bytes.Buffer
    cmd.Stdout = &out
    cmd.Stdout = &stderr
    err := cmd.Start()
    if err != nil {
        log.Fatal(fmt.Sprint(err) + ":" + stderr.String())
    }
    err = cmd.Wait()
    if err != nil {
        log.Fatal(fmt.Sprint(err) + ":" + stderr.String())
    }
    return out.String()
}

func goVersion(w http.ResponseWriter, r *http.Request, params httprouter.Params) {
    out := getCommandOutput("C:\\Go\\bin\\go.exe", "version")
    fmt.Println("out: " + out)
    fmt.Fprintf(w, out)
}

func showParams(w http.ResponseWriter, r *http.Request, params httprouter.Params) {
    fmt.Fprintf(w, params.ByName("name"))
}

//func goVersion(w http.ResponseWriter, r *http.Request, params httprouter.Params) {
//    fmt.Fprintln(w, getCommandOutput("go", "version"))
//}

func main() {
    // Any struct that has serveHTTP function can be a multiplexer
    router := httprouter.New()
    router.GET("/api/v1/go-version", goVersion)
    router.GET("/api/v1/t/:name", showParams)

    log.Fatal(http.ListenAndServe(":8000", router))
}
```

```
/api/v1/go-version
/api/v1/t/:name
```



