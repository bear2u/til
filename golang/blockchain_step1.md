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



