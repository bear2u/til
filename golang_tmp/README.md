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



