# Golang 으로 CRUD Restfual Api 만들기 1부

최근 블록체인을 공부하면서 이더리움 코어를 보고 싶다는 생각이 자주 들었다. 그리고 하이퍼레저에서 스마트 계약 개발시 Go 로 짜고 있는 걸 보고 고랭을 배워야 겠다는 마음을 먹고 하나씩 보고 있습니다.

언어를 제일 배우고 제일 먼저 해보는 건 무엇보다 **게시판** 하나 짜보는 거겠죠?

그래서 Restful Api 를 먼저 구성해보고 화면단을 만들어서 해보도록 합니다.

어설픈 _**TDD**_ 방식으로 하나씩 짜보도록 하겠습니다.

> 우선 Go 가 아직 설치가 안되신 분은 [https://golang.org/dl/](https://golang.org/dl/) 으로 가셔서 받으시길 바랍니다.

그리고 환경 설정을 해줍니다.

> 혹시 Gopath 와 Gopath bin 설정이 안되신 분은 [여기](https://github.com/arahansa/golkorea/wiki/01.-Go개발환경-구축_Windows-편) 에서 따라해보시면 됩니다.

> 현재 DB는 몽고디비를 사용중이며 도커로 연동중입니다.

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

그럼 `main_test.go` 를 하나 생성해줍니다. 그리고 샘플 예제를 하나 넣어서 테스트를 해봅니다. 

> 혹시 유닛 테스팅 방법이 궁금하시면 [여기](http://golang.site/go/article/115-Go-%EC%9C%A0%EB%8B%9B-%ED%85%8C%EC%8A%A4%ED%8A%B8) 로 가셔서 참고하시길 바랍니다.

```go
package main_test

import (
	"fmt"
	"github.com/restapi"
	"os"
	"testing"
)

var a main.App

var ip, dbName, collectionName = "192.168.99.100", "godb", "movies"

func TestMain(m *testing.M) {
	a = main.App{}
	a.Initialize(
		ip,
		dbName,
		collectionName,
	)

	prevTesting()

	code := m.Run()

	postTesting()

	os.Exit(code)
}

func prevTesting() {
	fmt.Println("prevTesting....")
}

func postTesting() {
	fmt.Println("postTesting....")
}

func TestExam(t *testing.T) {

	fmt.Println("TestExam inside")
}

```

```
prevTesting....
=== RUN   TestExam
TestExam inside
--- PASS: TestExam (0.00s)
PASS
postTesting....
```

내용은 보시면 아무것도 없습니다. 

단지 테스팅을 하는데 `TestExam `을 실행시 항상 `TestMain `에서 전처리 후처리를 거쳐서 진행한다는 게 중요합니다.

자바로 생각하면 `before `와 `after `로 보시면 되겠네요. 보통 DB를 열어서 더미 데이트들을 넣어주고 마지막엔 지워주는 역할을 해도 되거나 DB연결을 도와줘도 됩니다. 

일단 저는 다음에 쓰도록 하고 먼저 CRUD 서비스를 개발을 해보겠습니다. 

이 예제는 크게 5가지 기능을 가집니다. 

* AddMovie
* GetMovie
* UpdateMovie
* DeleteMovie
* GetMovies

그럼 테스팅 클래스에 먼저 `TestAddMovie `함수를 정의 해봅시다. 영화를 추가하는 기능을 테스팅합니다. 

```
func TestAddMovie(t *testing.T) {
	//TODO 영화 더미 데이터 하나를 가져온다.
	//TODO String으로 Json을 이용해서 변환해준다.
	//TODO Body 에 넣어서 Post 로 보내본다.
	//TODO 그리고 결과가 정상적으로 StatusCreated 코드가 떨어지는 지 본다.
	//TODO 입력 값을 JSON 으로 출력해서 정상적으로 입력되었는지 하나씩 체크
}
```

그럼 실제 코드로 옮겨보면 다음과 같이 만들수 있겠네요. 

```go
func TestAddMovie(t *testing.T) {

	//TODO 영화 더미 데이터 하나를 가져온다.
	darkNight := getMovieDummy()

	//TODO String으로 Json을 이용해서 변환해준다.
	darkNightMarshel, err := json.Marshal(darkNight)

	if err != nil {
		panic(err)
	}

	//TODO Body 에 넣어서 Post 로 보내본다.
	req, _ := http.NewRequest("POST", "/movie", bytes.NewBufferString(string(darkNightMarshel)))
	response := executeRequest(req)
	
	//TODO 그리고 결과가 정상적으로 StatusCreated 코드가 떨어지는 지 본다.
	checkResponseCode(t, http.StatusCreated, response.Code)

	//TODO 입력 값을 JSON 으로 출력해서 정상적으로 입력되었는지 하나씩 체크
	var m main.Movie
	json.Unmarshal(response.Body.Bytes(), &m)

	if darkNight.Name != m.Name {
		t.Errorf("Its not name equal %v", m.Name)
	}

	if darkNight.Year != m.Year {
		t.Errorf("Its not Year equal %v", m.Year)
	}

	if !cmp.Equal(darkNight.Directors, m.Directors) {
		t.Errorf("Its not Directors equal %v", m.Directors)
	}

	if !cmp.Equal(darkNight.Writers, m.Writers) {
		t.Errorf("Its not Writers equal %v", m.Writers)
	}

	if !cmp.Equal(darkNight.BoxOffice, m.BoxOffice) {
		t.Errorf("Its not BoxOffice equal %v", m.BoxOffice)
	}
}
```

그리고 더미 데이터는 임의로 만들어서 리턴을 해줍니다. 

```go
func getMovieDummy() *main.Movie {
	return &main.Movie{
		Name:      "The Dark Knight",
		Year:      "2008",
		Directors: []string{"Christopher Nolan"},
		Writers:   []string{"Jonathan Nolan", "Christopher Nolan"},
		BoxOffice: main.BoxOffice{
			Budget: 185000000,
			Gross:  533316061,
		},
	}
}
```

이 데이터 방식은 몽고 디비 컬렉션에 들어갈 내용입니다. 

그러면 DB 연동 하고 실제 `AddMovie `함수를 만들어봐야 겠군요. 

app.go 에 DB 연동 부분을 추가를 해줍니다. 

```go
type App struct {
	Router     *mux.Router
	session    *mgo.Session
	collection *mgo.Collection
}

func (a *App) Initialize(serverIp, dbName, collectionName string) {
	session, err := mgo.Dial(serverIp)
	a.session = session
	a.collection = a.session.DB(dbName).C(collectionName)

	if err != nil {
		panic(err)
	}
	//defer session.Close()

	a.Router = mux.NewRouter()
	a.initializeRoutes()
}
```

하단에 라우팅을 추가하는 함수가 있네요. 

```go
func (a *App) initializeRoutes() {
	//영화 추가
	a.Router.HandleFunc("/movie", a.addMovie).Methods("POST")
}
```

그리고 지금 당장은 안쓰지만 후에 서버를 실행할려면 다음과 같이 하단에 넣어주셔야 합니다. 

```go
func (a *App) Run(port string) {
	log.Fatal(http.ListenAndServe(":"+port, a.Router))
}
```

현재 mux 와 몽고 디비 라이버리를 가져와서 사용을 하고 있습니다. 명령어를 통해서 소스를 받아와서 연동을 해주도록 합니다. 

```
go get github.com/gorilla/mux
go get gopkg.in/mgo.v2
```

그럼 영화 추가 로직을 넣기 위해선 `model.go` 을 만들어서 실제 DB핸들링 하는 건 다시 빼서 위임을 시켜보도록 하죠. 

```go
# model.go

type Movie struct {
	ID        bson.ObjectId `json:"id" bson:"_id,omitempty"`
	Name      string        `json:"name" bson:"name"`
	Year      string        `json:"year" bson:"year"`
	Directors []string      `json:"directors" bson:"directors"`
	Writers   []string      `json:"writers" bson:"writers"`
	BoxOffice BoxOffice     `json:"boxOffice" bson:"boxOffice"`
}

type BoxOffice struct {
	Budget uint64 `json:"budget" bson:"budget"`
	Gross  uint64 `json:"gross" bson:"gross"`
}
```

우선 `Movie `와 `BoxOffice `를 정의를 합니다. 

```go
# model.go

//영화 정보를 입력함
func (m *Movie) AddMovie(db *App) (string, error) {

	m.ID = bson.NewObjectId()
	var name = db.collection.Name
	fmt.Println(name)
	err := db.collection.Insert(m)
	if err != nil {
		return "", err
	}

	return m.ID.Hex(), nil
}
```

아이디를 생성해서 DB에 입력하는 로직입니다. 

그리고 추후 입력된 내용에 대해 ID 를 리턴해서 다시 핸들링 \(업데이트 및 삭제\) 하게끔 할수 있습니다. 

그럼 이 모델 로직을 가져와서 사용하는 곳을 만들어봐야겠네요. 

실제 라우팅되서 처리되고 모델에 있는 로직함수가 실행된다고 보면 됩니다. 

> Test -&gt; main - app - model

```go
# app.go

func (a *App) addMovie(w http.ResponseWriter, r *http.Request) {
	var movie Movie
	postBody, err := ioutil.ReadAll(r.Body)
	if err != nil {
		respondWithError(w, http.StatusBadRequest, "parsing error")
		return
	}
	json.Unmarshal(postBody, &movie)

	defer r.Body.Close()

	if _, err := movie.AddMovie(a); err != nil {
		respondWithError(w, http.StatusInternalServerError, err.Error())
		return
	}

	respondWithJSON(w, http.StatusCreated, movie)
}
```

응답에 대한 오류와 성공 처리는 `respond `를 통해서 하고 있습니다.

```go
# app.go

func respondWithError(w http.ResponseWriter, code int, message string) {
	respondWithJSON(w, code, map[string]string{"error": message})
}

func respondWithJSON(w http.ResponseWriter, code int, payload interface{}) {
	response, _ := json.Marshal(payload)

	w.Header().Set("Content-Type", "application/json")
	w.WriteHeader(code)
	w.Write(response)
}
```

이제 AddMovie 쪽이 완성이 되었네요. 그럼 영화정보를 가져와야겠네요. 

우선 테스팅 코드에 입력을 한다음

```go
# main_test.go

func TestGetMovie(t *testing.T) {
	//id := bson.NewObjectId()
	id := addDommyMovie(getMovieDummy())
	req, _ := http.NewRequest("GET", "/movie/"+id, nil)
	response := executeRequest(req)
	checkResponseCode(t, http.StatusOK, response.Code)

	var m main.Movie
	json.Unmarshal(response.Body.Bytes(), &m)
}
```

라우팅 처리하는 곳 가서 `GetMovie `함수를 추가해줍니다.

```go
func (a *App) getMovie(w http.ResponseWriter, r *http.Request) {
	vars := mux.Vars(r)

	movie := Movie{ID: bson.ObjectIdHex(vars["id"])}
	if err := movie.GetMovie(a); err != nil {
		switch err {
		case mgo.ErrNotFound:
			respondWithError(w, http.StatusNotFound, err.Error())
		default:
			respondWithError(w, http.StatusInternalServerError, err.Error())
		}

		return
	}

	respondWithJSON(w, http.StatusOK, movie)
}
```

로직을 담당하는 `model.go `에서 `GetMovie `를 추가해줍니다. 

```go
//영화를 가져오는 함수
func (m *Movie) GetMovie(db *App) error {

	//var movie Movie
	err := db.collection.Find(bson.M{"_id": m.ID}).One(&m)
	if err != nil {
		return err
	}

	return nil
}
```

네. 사실 한줄 씩 테스팅 코드를 짜는 게 맞지만 대략적인 흐름만 알려드렸네요. 

그럼 `업데이트, 삭제, 전체 리스트` 가져오기도 이런식으로 추가해서 진행할 수 있습니다. 

* [전체 소스 보기](https://github.com/bear2u/golang-api-server-mongo) \(추가, 삭제, 수정, 조회등등\)
* [Build And Testing a Rest Api](https://semaphoreci.com/community/tutorials/building-and-testing-a-rest-api-in-go-with-gorilla-mux-and-postgresql)

그럼 다음에는 이 기능들을 이용해서 프론트단\(React\) 개발 및 연동을 해보겠습니다. 

고랭 가즈아~~



