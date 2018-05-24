# Sqlite3 와 CRUD 서비스 만들기

```go
package main

import (
    "github.com/emicklei/go-restful"
    "net/http"
    "io"
    "fmt"
    "time"
)

func main() {
    //Create a web service
    webservice := new(restful.WebService)
    //Create a route and attach it to handler in the service
    webservice.Route(webservice.GET("/ping").To(pingTime))
    // Add the service to application
    restful.Add(webservice)
    http.ListenAndServe(":8000", nil)
}

func pingTime(req *restful.Request, resp *restful.Response) {
    // Write to the response
    io.WriteString(resp, fmt.Sprintf("%s", time.Now()))
}
```

```
http://localhost:8000/ping
-> 2018-05-24 17:52:22.048409 +0900 KST m=+9.043500001
```

우선 몽고 디비를 설치를 해보자. 

설치가 너무 귀찮으므로 docker로 가져오자. 

[https://hub.docker.com/\_/mongo/](https://hub.docker.com/_/mongo/)

그리고 설치한 다음

```
docker run --name mongo -p 27017:27017 -d mongo
```

포트 열면 가상머신\(오라클 VM\)도 같이 열어줘야 되는 경우도 있으므로 체크하자. 

```
docker-machine ip
>> 192.168.99.100
```

```go
package main

import (
	"fmt"
	"log"

	mgo "gopkg.in/mgo.v2"
	"gopkg.in/mgo.v2/bson"
)

// Movie holds a movie data
type Movie struct {
	Name      string   `bson:"name"`
	Year      string   `bson:"year"`
	Directors []string `bson:"directors"`
	Writers   []string `bson:"writers"`
	BoxOffice `bson:"boxOffice"`
}

// BoxOffice is nested in Movie
type BoxOffice struct {
	Budget uint64 `bson:"budget"`
	Gross  uint64 `bson:"gross"`
}

func main() {
	session, err := mgo.Dial("192.168.99.100")
	if err != nil {
		panic(err)
	}
	defer session.Close()

	c := session.DB("godb").C("movies")

	// Create a movie
	darkNight := &Movie{
		Name:      "The Dark Knight",
		Year:      "2008",
		Directors: []string{"Christopher Nolan"},
		Writers:   []string{"Jonathan Nolan", "Christopher Nolan"},
		BoxOffice: BoxOffice{
			Budget: 185000000,
			Gross:  533316061,
		},
	}

	// Insert into MongoDB
	err = c.Insert(darkNight)
	if err != nil {
		log.Fatal(err)
	}

	// Now query the movie back
	result := Movie{}
	// bson.M is used for nested fields
	err = c.Find(bson.M{"boxOffice.budget": bson.M{"$gt": 150000000}}).One(&result)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Println("Movie:", result.Name)
}
```

일단 접속해서 DB를 쓸수 있다. 

![](/assets/mongo_db.png)



