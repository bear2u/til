# Pm2를 이용한 Spring boot deploy



EC2에 노드 서비스를 배포 함에 있어서 `forever`와 `pm2 `를 이용을 하는 편이다. 

`Pm2 `는 슬랙이나 외부 연동도 좋아서 자주 이용되는 편이다. 

그래서 이번에 Spring boot 도 배포를 하는데 pm2 를 이용할 수 있을까 찾아보니 당연히 가능하다. 

우선 스프링 부트를 배포를 해보자. 

```
mvn package
```

그럼 war or jar 파일이 나올것이다. 

나온걸 서버로 올려놓고. 노드를 설치를 한다. 

나는 주로 nvm 을 이용해서 설치를 하는 편이다. 

* [https://medium.com/@jjeaby/nvm-node-%EC%84%A4%EC%B9%98-a4bcab00a5ef](https://medium.com/@jjeaby/nvm-node-%EC%84%A4%EC%B9%98-a4bcab00a5ef)
* [https://github.com/creationix/nvm](https://github.com/creationix/nvm)

설치를 진행 한 다음 `pm2 `를 설치를 하자. 

```
npm install -g pm2@latest
```

그리고 패키징된 war 파일이 있는 곳에 json 파일을 하나 만들자. 

```
>> app.json

{
  "apps": [{
    "name": "War",
    "cwd": ".",
    "args": [
      "-jar",
      "/path/to/your.war" //여기에 war 파일 위치
    ],
    "env": {
    },
    "script": "/usr/bin/java",
    "node_args": [],
    "log_date_format": "YYYY-MM-DD HH:mm Z",
    "exec_interpreter": "none",
    "exec_mode": "fork"
  }]
}

```

마지막으로 pm2 을 이용해서 app.json 을 실행하자. 

```
pm2 start app.json
```

그럼 잠시 후 문제가 없을 경우 정상적으로 서비스형태로 도는 걸 볼수 있다. 

![](/assets/spring_boot_war1.png)

재 시작시

```
pm2 restart War
```

중지시

```
pm2 stop War
```

그리고 외부 서비스와 연동이 가능하다. \(슬랙등\)

여기에선 [https://app.keymetrics.io](https://app.keymetrics.io/) 을 사용해보겠다. 

![](/assets/spring_boot_pm2_2.png)

무료로 한개까진 사용이 가능하다. 

