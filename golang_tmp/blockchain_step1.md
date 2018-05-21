기
본 문법 정리
Go는 c, c++ 와 같은 컴파일 언어이다. 하지만 자바나 닷넷 같은 VM 위에서 도는게 아니라 바이트 코드를 바로 만들어 내서 속도가 빠른 장점이 있다.

그리고 Go 언어는 VM 기반이 아니지만 가비지 컬렉터를 가지고 있어서 메모리 관리를 지원해줍니다.

Go언어는 언어차원에서 병행성(Concurrentcy, 동시성) 을 지원합니다. 쓰레드 여러개가 시간을 쪼개어 순차적으로 실행되는 방법을 뜻한다. 동시적으로 보일순 있다.

고루틴이라는 방법으로 만들수 있는데 ( go 명령어를 통해서 ) 이 고루틴끼리 통하는 방법은 채널이라고 칭한다.

패키지 의존성 설치는import 키워드를 통해서 지정한 후 go get, go install 을 사용해서 자동으로 소스코드를 가져올수 있다.

블록체인에 적합한 언어로 추천을 하고 있다.

설치 진행 : https://golang.org/dl/

준비내용
해당 OS 다운로드
GOPATH 환경변수에 패스 지정 (폴더위치 중요)
bin
pkg
src
test
test.go
main.go 파일을 생성 및 실행
package main

import "fmt"

func main() {
    fmt.Println("Hello Golang");
}
go run main.go //실행
godoc fmt Println //고 함수 문서 확인 가능하다. 

use 'godoc cmd/fmt' for documentation on the fmt command

func Println(a ...interface{}) (n int, err error)
    Println formats using the default formats for its operands and writes to
    standard output. Spaces are always added between operands and a newline
    is appended. It returns the number of bytes written and any write error
    encountered.
변수 선언
package main

import "fmt"

func main() {
    var title = "Hello"
    value := "Golang"
    fmt.Println(title + " " + value)
    fmt.Println(title, value)
}
.........................
Hello Golang
Hello Golang
상수는 const 지원
여러개의 변수를 정의도 가능하다.

var (
    a = 5
    b = 10
    c = 15
)
간단한 입력프로그램 작성

package main

import "fmt"

func main() {
    fmt.Print("숫자를 입력해주세요.")
    var input float64
    fmt.Scanf("%f", &input)

    output := input * 2
    fmt.Println(output)
}

...............
숫자를 입력해주세요.100
200
for, if, switch 문은 기존 언어와 별 차이가 없음
var x [5]int
배열
package main

import "fmt"

func main() {
    var x [3]int
    x[2] = 100
    fmt.Println(x)
}
...........
[0 0 100] //3번째 값에 들어간다.



main.go

```go
package main

import (
    "fmt"
)

func main() {
    bc := NewBlockchain()

    bc.AddBlock("Send 1 BTC to Ivan")
    bc.AddBlock("Send 2 more BTC to Ivan")

    for _, block := range bc.blocks {
        fmt.Printf("Prev. hash: %x\n", block.PrevBlockHash)
        fmt.Printf("Data: %s\n", block.Data)
        fmt.Printf("Hash: %x\n", block.Hash)
        fmt.Println()
    }
}
```

block.go

```go
package main

import (
    "bytes"         //바이트 핸들링
    "crypto/sha256" //암호화
    "fmt"
    "strconv" //String to Int, Int to String
    "time"    //시간
)

type Block struct {
    Timestamp     int64
    Data          []byte
    PrevBlockHash []byte
    Hash          []byte
}

// SetHash calculates and sets block hash
func (b *Block) SetHash() {
    timestamp := []byte(strconv.FormatInt(b.Timestamp, 10))
    headers := bytes.Join([][]byte{b.PrevBlockHash, b.Data, timestamp}, []byte{})
    hash := sha256.Sum256(headers)

    b.Hash = hash[:]
}

// NewBlock creates and returns Block
func NewBlock(data string, prevBlockHash []byte) *Block {
    block := &Block{time.Now().Unix(), []byte(data), prevBlockHash, []byte{}}
    block.SetHash()
    return block
}

// NewGenesisBlock creates and returns genesis Block
func NewGenesisBlock() *Block {
    return NewBlock("Genesis Block", []byte{})
}
```

blockchain.go

```go
package main

// Blockchain keeps a sequence of Blocks
type Blockchain struct {
    blocks []*Block
}

// AddBlock saves provided data as a block in the blockchain
func (bc *Blockchain) AddBlock(data string) {
    prevBlock := bc.blocks[len(bc.blocks)-1]
    newBlock := NewBlock(data, prevBlock.Hash)
    bc.blocks = append(bc.blocks, newBlock)
}

// 제네시스 블록을 생성 한 후 블록 슬라이스 생성해서 BlockChain 자료형 리턴
func NewBlockchain() *Blockchain {    
    return &Blockchain{[]*Block{NewGenesisBlock()}}
}
```



