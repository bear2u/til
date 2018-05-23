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



