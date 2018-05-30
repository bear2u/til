# Golang 으로 CRUD Restfual Api 만들기 1부

최근 블록체인을 공부하면서 이더리움 코어를 보고 싶다는 생각이 자주 들었다. 그리고 하이퍼레저에서 스마트 계약 개발시 Go 로 짜고 있는 걸 보고 고랭을 배워야 겠다는 마음을 먹고 하나씩 보고 있습니다.

언어를 제일 배우고 제일 먼저 해보는 건 무엇보다 **게시판** 하나 짜보는 거겠죠?

그래서 Restful Api 를 먼저 구성해보고 화면단을 만들어서 해보도록 합니다.

어설픈 TDD 방식으로 하나씩 짜보도록 하겠습니다.

> 우선 Go 가 아직 설치가 안되신 분은 [https://golang.org/dl/](https://golang.org/dl/) 으로 가셔서 받으시길 바랍니다.

그리고 환경 설정을 해줍니다.

> 혹시 Gopath 와 Gopath bin 설정이 안되신 분은 [여기](https://github.com/arahansa/golkorea/wiki/01.-Go개발환경-구축_Windows-편) 에서 따라해보시면 됩니다.

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

그리고 App 파일을 만들어서 서버 관리를 위임하도록 합니다. 

`main.go` 에서 `App` 인스턴스를 생성해서 서버쪽을 실행하고 DB도 연결하도록 합니다.

```go
# app.go

package main

type App struct {
}

func (a *App) Initialize(serverIp, dbName, collectionName string) {

}
func (app App) Run(port string) {
	
}
```

그럼 main 에서는 어떻게 호출 하면 될까요? 

```go
package main

func main() {
	app := App{}
	app.Initialize(
		"192.168.99.100",
		"godb",
		"movies",
	)

	app.Run("8000")
}
```

* App 인스턴스를 만들고
* 초기화를 하고
* App Run 함수를 통해서 실행을 합니다. 

일단 제일 기본 뼈대는 진행된 것 같네요. 그럼 `TDD`로 먼저 테스팅을 작성해봅니다. 

> 참고로 저도 고랭 처음이기도 해서 아주 어설프게 진행하니 양해부탁드립니다. ^^



