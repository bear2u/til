# Golang 테스팅

go는 테스트시 testing 을 따로 임포트 해서 사용할 수 있다. 

그럼 시작해보자. 

우선 계산하는 걸 해보고자 한다. 

> calc.go

```go
package uint

func Sum(a ...int) int {
	sum := 0
	for _,i := range a {
		sum += i
	}

	return sum
}
```

테스팅시 패키지명도 다르게 한다. 

> calc\_test.go

```go
package uint_test

import (
	calc "github.com/uint_test"
	"testing"
)

func TestSum(t *testing.T) {
	s := calc.Sum(1, 2, 3)

	if s!= 6 {
		t.Error("Wrong result")
	}
}
```

마지막으로 테스팅을 해본다. 

```
>>> go test
PASS
ok      github.com/uint_test    0.040s
```



