# 윈도우 상에서 도커를 이용해서 몽고 DB 세팅

윈도우상에서 몽고디비 설치시 가끔 오류 나는 부분이 있다. 그럼 설치도 안되고 개발도 안된다. 

그럴때 도커를 이용해서 빠르게 테스팅을 할수 있다. 

우선 도커를 켜서 다음 명령어를 하자. 

```
docker pull mongo
docker run --name database -d -p 27017:27017 mongo --noauth --bind_ip=0.0.0.0
```

27017 포트에 0.0.0.0 으로 바인딩하는 문구이다. 

-noauth 는 아이디랑 비번없이 들어갈 수 있다. 

만약 설정시 아래와 같이 가능하다. 

```
docker run --name some-mongo -d mongo --auth
docker exec -it some-mongo mongo admin

db.createUser({ user: 'jsmith', pwd: 'some-initial-password', roles: [ { role: "userAdminAnyDatabase", db: "admin" } ] });

................................
Successfully added user: {
    "user" : "jsmith",
    "roles" : [
        {
            "role" : "userAdminAnyDatabase",
            "db" : "admin"
        }
    ]
}
```

그러면 잘 설치가 되었는지 확인해보자. 

```
docker ps
```

![](/assets/docker050301.png)

윈도우 유저의 경우 virtucal box 에 방화벽을 열어야 한다. 

virutal box 열기

기본 머신 선택한 후에 셋팅 열기

설정에 네트워크 메뉴 열기

네트워크 옵션 하단에 포워딩 메뉴 열기

그리고 줄 하나 추가 한다. 

mongo / TCP / host port \( 임의로 &lt; 1111 &gt; \) / guest port \( 27017 \)

![](/assets/mongo050302.png)

그리고 클라이언트를 다운받는다. 

보통 주로 받는 건 

[https://robomongo.org/](https://robomongo.org/)

[https://www.mongodb.com/download-center?jmp=hero\#compass](https://www.mongodb.com/download-center?jmp=hero#compass) 

\(무료 커뮤니티 버전 선택\)

---

그리고 마지막으로 클라이언트 접속을 해본다. 

IP 는 localhost 또는 127.0.0.1 로 하고

포트는 임의로 정한 1111 로 해본다. 

그럼 admin 으로 접속된걸 볼수 있다. 

참고 사이트

[https://codehangar.io/mongodb-image-instance-with-docker-toolbox-tutorial/](https://codehangar.io/mongodb-image-instance-with-docker-toolbox-tutorial/)

[https://hub.docker.com/\_/mongo/](https://hub.docker.com/_/mongo/)

