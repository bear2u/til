Golang 임시 자료

for 문 기본

```go
package main

import "fmt"

var array = [5]float64{
    1,
    2,
    3,
    4,
    5,
}

func main() {    
    for i := 0; i < len(array); i++ {
        value := array[i]
        fmt.Println(i,">>",value);
    }
}

....................
0 >> 1
1 >> 2
2 >> 3
3 >> 4
4 >> 5
```

for 문을 range 로 만들수 있다.

```go
import "fmt"

var array = [5]int{
    1,
    2,
    3,
    4,
    5,
}

func main() {    
    total  := 0
    for _, value := range array {
        total += value
    }

    fmt.Println(total / len(array))
}
```

배열

```go
var a []int
var b[]int = make([]int, 5)
var c = make([]int, 5)
d := make([]int, 5)

e := []int{1, 2, 3, 4, 5}

f := []int{
	1,
	2,
	3,
	4,
	5, //마지막줄에는 항상 콤마를 붙여야 됨
}

s := make([]int, 5, 10)

fmt.Println(len(s)) //5 길이
fmt.Println(cap(s)) //10 용량

fmt.Println(s[4]) //0으로 출력
fmt.Println(s[5]); //panic: runtime error: index out of range
```



