# Golang 으로 CRUD Restfual Api 만들기 1부

최근 블록체인을 공부하면서 이더리움 코어를 보고 싶다는 생각이 자주 들었다. 그리고 하이퍼레저에서 스마트 계약 개발시 Go 로 짜고 있는 걸 보고 고랭을 배워야 겠다는 마음을 먹고 하나씩 보고 있습니다. 

언어를 제일 배우고 제일 먼저 해보는 건 무엇보다 **게시판** 하나 짜보는 거겠죠?

그래서 Restful Api 를 먼저 구성해보고 화면단을 만들어서 해보도록 합니다. 

TDD 방식으로 하나씩 짜보도록 하겠습니다. 

> 우선 Go 가 아직 설치가 안되신 분은 [https://golang.org/dl/](https://golang.org/dl/) 으로 가셔서 받으시길 바랍니다.

그리고 환경 설정을 해줍니다.

> 혹시 Gopath 와 Gopath bin 설정이 안되신 분은 [여기](https://github.com/arahansa/golkorea/wiki/01.-Go%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%B6%95_Windows-%ED%8E%B8) 에서 따라해보시면 됩니다.

그럼 우선 처음 시작은 `main.go` 로 구성해보죠. 

```go
# main.go

package main

import "fmt"

func main() {
	fmt.Println("Hello Go")
}

>>> Hello Go
```

```
> go run main.go //실행
```



