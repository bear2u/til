# 기본 인증 구현

pom.xml 에 시큐리티 디펜시즈를 추가한다.

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

```
Using generated security password: de0224a6-ecf6-4c1b-b026-c988275e7786
```

재 시작시 다음과 같은 암호가 나온다.

그러면 포스트맨에서 호출시 Basic Auth 를 선택하고 아이디는 `user`로 하고 비번은 위의 암호를 지정한다.

![](/assets/spring_step27_1.png)

만약 수동으로 아이디와 비번을 주고 싶을땐 다음과 같이 준다. 

```
managment.security.enabled=false
spring.security.user.name=username
spring.security.user.password=1234
```







