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

배열 및 슬라이스

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

슬라이스 추가

```go
package main

import "fmt"

func main() {
	a := []int{1, 2, 3, 4, 5}

	a = append(a, 6, 7, 8)

	fmt.Println(a)

	b := []int{11, 12, 13, 14, 15}

	a = append(a, b...)

	fmt.Println(a)
}
..............
[1 2 3 4 5 6 7 8]
[1 2 3 4 5 6 7 8 11 12 13 14 15]
```

# 포인터

```go
package main

import "fmt"

func setX(x int) {
	x = 0
}

func setX2(x *int) {
	*x = 0
}

func main() {
	testOne() //변경안됨
	testTwo() //변경됨
	testThree()
}

func testOne() {
	x := 10
	setX(x)
	fmt.Println(x)
}

func testTwo() {
	x := 10
	setX2(&x)
	fmt.Println(x)
}

func testThree() {
	x := new(int)
	setX2(x)
	fmt.Println(*x)
}

................
10
0
0
```



