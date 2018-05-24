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



