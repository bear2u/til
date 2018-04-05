# Docker에 wordpress 와 mysql 빠른 설치

우선 작업 할 폴더를 생성하자.

```
mkdir wordpress
cd wordpress
```

`docker compose` 를 이용한 설치를 할 예정이다.

yml \(.yaml\) 생성 해서 내용을 넣어보자. wordpress 와 mysql 설치하는 것이다.

```
nano docker-compose.yml
```

```
version: '3.3'

services:
   db:
     image: mysql:5.7
     volumes:
       - dbdata:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
volumes:
    dbdata:
```

db\_data 라는 볼륨이 만들어졌다. 더 자세히 알고 싶으면 여기 [클릭](https://docs.docker.com/engine/admin/volumes/volumes/)

docker-compose up 으로 실행을 해보자.

```bash
$ docker-compose up -d
Creating network "my_wordpress_default" with the default driver
Pulling db (mysql:5.7)...
5.7: Pulling from library/mysql
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
...
Digest: sha256:34a0aca88e85f2efa5edff1cea77cf5d3147ad93545dbec99cfe705b03c520de
Status: Downloaded newer image for mysql:5.7
Pulling wordpress (wordpress:latest)...
latest: Pulling from library/wordpress
efd26ecc9548: Already exists
a3ed95caeb02: Pull complete
589a9d9a7c64: Pull complete
...
Digest: sha256:ed28506ae44d5def89075fd5c01456610cd6c64006addfe5210b8c675881aff6
Status: Downloaded newer image for wordpress:latest
Creating my_wordpress_db_1
Creating my_wordpress_wordpress_1
```

잘 올라갔는지 확인해보자.

```
& docker-compose ps
        Name                       Command               State          Ports
-------------------------------------------------------------------------------------
wordpress_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
wordpress_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:8000->80/tcp
```

mysql 은 3306 포트에 wordpress는 80 포트에 바인딩 된 걸 볼수 있다.

그럼 이제 아이피주소를 확인해보자.

```
$ docker-machine ip
192.168.99.100
```

그럼 브라우저에 가서 `192.168.99.100:8000` 으로 열어보자.

참고 페이지 :

* [https://wpguide.usefulparadigm.com/posts/257](https://wpguide.usefulparadigm.com/posts/257)
* [https://docs.docker.com/compose/wordpress/\#bring-up-wordpress-in-a-web-browser](https://docs.docker.com/compose/wordpress/#bring-up-wordpress-in-a-web-browser)



