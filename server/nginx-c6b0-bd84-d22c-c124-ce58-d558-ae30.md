# **nginx 설치하기**

```
sudo apt-get install nginx
```

버전 체크

```
nginx -v
-> nginx version: nginx/1.10.3 (Ubuntu)
```

명령어 모음

```
// 시작
$ sudo service nginx start
$ sudo systemctl start nginx
$ sudo /etc/init.d/nginx start

// 재시작
$ sudo service nginx restart
$ sudo systemctl restart nginx
$ sudo /etc/init.d/nginx restart

// 중지
$ sudo service nginx stop
$ sudo systemctl stop nginx
$ sudo /etc/init.d/nginx stop

// 상태
$ sudo service nginx status
$ sudo systemctl status nginx

// 설정 reload
$ sudo service nginx reload
$ sudo systemctl reload nginx
$ sudo nginx -s reload

```

**FTP 설치**

```
sudo apt-get install vsftpd
```

**설정페이지**

```
nano /etc/vsftpd.conf
```

기본 설정

```
# chroot 적용 
# 아래와 같은 설정을 할 경우 사용자들은 자신의 계정에서 상위 디렉토리로 이동할수 없게된다. 
chroot_local_user=YES

# 수정 가능
write_enable=YES

출처: http://webdir.tistory.com/199 [WEBDIR]
```

그리고 유저를 추가

```
adduser test
//비번 입력창 나오면 알아서 입력
```

```
sudo /etc/init.d/vsftpd restart //재가동
```



기본 설정

```
vsftpd.conf 와 관련된 내용



1. FTP의 포트를 바꾸고 싶으면 어떻게 해야 할까?



  listen=YES 라고 써 있는 부분 아래에

  listen_port=21022 (원하는 포트번호로 넣어줌) 적어준다





2. 비계정(guest)의 접속을 허용하게 하려면


  anonymous_enabled=NO 를 YES로 변경해 준다.





3. 업로드 가능하게 하려면?

  write_enable=YES에 걸려있는 주석을 제거해야 한다.





4. 파일 업로드 했을 때 파일의 권한을 설정하려면 어떻게 해야 하나?

  local_umask=022 (마스킹 처리가 되기 때문에 022로 하면 해당파일은 755 라고 설정된다)에 걸려있는 주석을 제거한다.





5. 계정사용자가 상위디렉토리(root 디렉토리)에 접근하는 것을 허용하려면?

  chroot_local_user=YES

  chroot_list_enable=YES

  chroot_list_file=/etc/vsftpd.chroot_list

  최초에 설치하면 주석이 걸려있는데 이것은 상위 디렉토리로 이동하는것을 막기 위함이다.

  이 주석을 제거하고 난 후에 chroot_list_file 항목에 적혀있는 경로로 파일을 만들고 그 안에 계정이름을 적으면 상위로 이동하 것이 허용된다.



  $sudo vi /etc/vsftpd.chroot_list

   user1

   user2



 이렇게 하면 user1과 user2는 상위 디렉토리로 이동이 가능해진다.



6. root 계접 접속을 허용하려면?

  $sudo vi /etc/ftpusers

  root 계정에 앞에 주석처리 해준다 (또는 삭제)

```

노드 설치

nvm 으로 설치

[https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-16-04)

```
npm 에서 네이티브 에드온 컴파일 하고 싶으면 

sudo apt-get install -y build-essential
```



