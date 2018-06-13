# Boltdb 연동

[공식 Github](https://github.com/boltdb/bolt)

빠르고 효율적인 저레벨 DB 중 하나인 bolt db 이다. key, value 로 되어있고 byte로 값을 넣을 수 있다는 장점이 있다. 

우선 설치는 

```
$ go get github.com/boltdb/bolt/...
```

```go
// DB 여는 작업
package main

import (
	"log"

	"github.com/boltdb/bolt"
)

func main() {
	// Open the my.db data file in your current directory.
	// It will be created if it doesn't exist.
	db, err := bolt.Open("my.db", 0600, nil)
	if err != nil {
		log.Fatal(err)
	}
	defer db.Close()

	...
}
```

다른 프로세스가 여기 DB를 열때 락이 걸려있어서 못 연다 그러면 기존 프로세스가 DB를 닫을 때까지 무한대기를 할수 밖에 없는 상황이 발생한다. 이럴때 시간이 지나면 자동으로 대기를 풀어버릴수 있다. 

```go
db, err := bolt.Open("my.db", 0600, &bolt.Options{Timeout: 1 * time.Second})
```

기본적으로 3가지 함수를 가진다. 

* 읽기-쓰기
* ```
  err := db.Update(func(tx *bolt.Tx) error {
  	...
  	return nil
  })
  ```
* 읽기전용
* ```
  err := db.View(func(tx *bolt.Tx) error {
  	...
  	return nil
  })
  ```
* 배치\(다중 업데이트\)
* ```
  err := db.Batch(func(tx *bolt.Tx) error {
  	...
  	return nil
  })
  ```

> # 버킷을 만드는 소스

`CreateBucketIfNotExists `은 버킷이 없는 경우 생성하는 함수이다. 

```go
package main

import (
	"fmt"
	"github.com/boltdb/bolt"
	"log"
)

func main() {
	fmt.Println("test")
	db, err := bolt.Open("my.db", 0600, nil)
	if err != nil {
		log.Fatal(err)
	}

	db.Update(func(tx *bolt.Tx) error {
		_, err := tx.CreateBucketIfNotExists([]byte("MyBucket"))
		if err != nil {
			return fmt.Errorf("Create bucker: %s", err)
		}
		fmt.Println("update done")
		return nil
	})

	defer db.Close()
}
```

그럼 값을 넣어보고 조회를 해보자. 

주의 할 내용은 모든 key / value 은 byte로 넣어야 한다. 

```go
package main

import (
	"fmt"
	"github.com/boltdb/bolt"
	"log"
)

func main() {
	fmt.Println("test")
	db, err := bolt.Open("my.db", 0600, nil)
	if err != nil {
		log.Fatal(err)
	}

	db.Update(func(tx *bolt.Tx) error {
		b := tx.Bucket([]byte("MyBucket"))
		err := b.Put([]byte("answer"), []byte("42"))
		return err;
	})

	db.View(func(tx *bolt.Tx) error {
		b := tx.Bucket([]byte("MyBucket"))
		v := b.Get([]byte("answer"))
		fmt.Printf("The answer is : %s\n", v)
		return nil
	})

	defer db.Close()
}

////////////
test
The answer is : 42
```

만약 키값을 시퀸스로 계속 올리고 싶다면??

`NextSequence`\(\) 을 사용하면 가능하다. 

```go
// CreateUser saves u to the store. The new user ID is set on u once the data is persisted.
func (s *Store) CreateUser(u *User) error {
    return s.db.Update(func(tx *bolt.Tx) error {
        // Retrieve the users bucket.
        // This should be created when the DB is first opened.
        b := tx.Bucket([]byte("users"))

        // Generate ID for the user.
        // This returns an error only if the Tx is closed or not writeable.
        // That can't happen in an Update() call so I ignore the error check.
        id, _ := b.NextSequence()
        u.ID = int(id)

        // Marshal user data into bytes.
        buf, err := json.Marshal(u)
        if err != nil {
            return err
        }

        // Persist bytes to users bucket.
        return b.Put(itob(u.ID), buf)
    })
}

// itob returns an 8-byte big endian representation of v.
func itob(v int) []byte {
    b := make([]byte, 8)
    binary.BigEndian.PutUint64(b, uint64(v))
    return b
}

type User struct {
    ID int
    ...
}
```

그 외에도 여러가지 옵션들이 있다. [공식 레포](https://github.com/boltdb/bolt) 에서 확인가능하다. 

