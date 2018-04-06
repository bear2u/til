# 2018년 3월 9일자 확인됨

# 우분투에 서버 설치하기

시작하기전 기본 설정

1. EC2 기본형 올리기 \(프리티어급\)
2. express 노드 앱이 준비 되어 있다는 가정
3. putty나 맥을 통해서 터미널로 접속이 가능해야함 \(pem 파일 이미 등록된 상태\)

> DB는 AWS RDS 이용합니다.
>
> 파일 서버는 S3 이용합니다.

# **방화벽 설정하기**

aws ec2는 콘솔에서 설정가능

참고 링크 :

[https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-16-04 ](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-16-04)

```
ufw allow 80
```

# N**ginx 설치**

일반적으로 우분투에 설치시 최신버전을 설치를 안한다. 그래서 저장소 키를 가져와서 직접 최신걸로 업데이트를 할 수 있다.

참고내용 :

[https://websiteforstudents.com/install-nginx-latest-version-ubuntu-16-10-17-04/](https://websiteforstudents.com/install-nginx-latest-version-ubuntu-16-10-17-04/)

[https://bjornjohansen.no/install-latest-version-of-nginx-on-ubuntu](https://bjornjohansen.no/install-latest-version-of-nginx-on-ubuntu)

**사인키 등록**

    // get nginx key
    curl -O https://nginx.org/keys/nginx_signing.key

    // install
    apt-key add nginx_signing.key

    $ sudo echo -e "deb https://nginx.org/packages/mainline/ubuntu/ `lsb_release -cs` nginx\ndeb-src https://nginx.org/packages/mainline/ubuntu/ `lsb_release -cs` nginx" > /etc/apt/sources.list.d/nginx.list

```
sudo apt-get update
sudo apt-get install nginx
```

**업그레이드시**

```
sudo apt-get dist-upgrade
```

**버전 체크**

```
nginx -v
-> nginx version: nginx/1.10.3 (Ubuntu)
```

**명령어 모음**

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

FTP 권한 및 옵션 확인

```
echo 'allow_writeable_chroot=YES' >> /etc/vsftpd.conf //YES로 변경
chmod 777 /home/{유저이름}
```

```
sudo /etc/init.d/vsftpd restart //재가동
```

**기본 설정**

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

**노드 설치**

`nvm 으로 설치`

[https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-16-04)

**또는 정식 설치**

[https://nodejs.org/en/download/package-manager/](https://nodejs.org/en/download/package-manager/)

```
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs

or

curl -sL https://deb.nodesource.com/setup_9.x | sudo -E bash -
sudo apt-get install -y nodejs
```

**네이티브 애드온 설치**

```
sudo apt-get install -y build-essential
```

**노드를 백그라운드에서 돌릴수 있는 PM2 설치**

```
sudo npm install pm2 -g 

pm2 startup systemd //서버 재시동시 자동으로 올릴수 있도록

pm2 start ./bin/www //express app.js 실행
```

**그리고 pm2을 통해서 외부에서 모니터링 설치**

[https://app.keymetrics.io](https://app.keymetrics.io) 추천 \( 1개까진 무료 \)

```
//create bucket 후
pm2 link b3o4z****** rxio4******** //메뉴얼대로 진행
```

그러면 관리자화면이 자동으로 연동![](/assets/keymatrics.png)

이제 ngnix에서 내부 서버로 포워딩해야함

참고 사이트 :

[https://www.digitalocean.com/community/tutorials/how-to-set-up-a-node-js-application-for-production-on-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-node-js-application-for-production-on-ubuntu-16-04)

```
vi /etc/nginx/sites-available/default

..........

# / 루트로 오는 경우 3000번으로 포워딩처리
location / { 
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

.........
```

혹시 `site-avavailable` 폴더가 안만들어진 경우...

```
cd /etc/nginx/sites-enabled 
//없는 폴더일 경우

//1. sites-enabled 폴더를 만든다. 
//2. default(사이트이름가능) 만든다. 
vi /etc/nginx/sites-enabled/default
```

```
///etc/nginx/sites-enabled/default 내용

server {
    listen 80;

    server_name  {도메인주소};

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

**nginx sites-enabled에 symlink 생성하기**

이제`sites-available`에 있는 파일들에 대해서`sites-enabled`에 symlink를 추가합시다

```
mkdir /etc/nginx/sites-enabled
sudo ln -s /etc/nginx/sites-available/[아까 만든 default] /etc/nginx/sites-enabled/
```

그리고 문법에 이상없는 지 체크

```
nginx -t
-> nginx: configuration file /etc/nginx/nginx.conf test is successful
```

**그럼 ngnix 재시작**

```
// 재시작은 아래에 있는 명령어 중 하나로 실행하시면 됩니다.
$ sudo service nginx restart
$ sudo systemctl restart nginx
$ sudo /etc/init.d/nginx restart

-> [ ok ] Restarting nginx (via systemctl): nginx.service.
```

그러면 포스트맨이나 웹에서 접속 테스트를 해본다.

도메인은 router 53에서 A 레코드로 하나 추가함.

그리고 value 에 해당 아이피를 입력함

> 시간이 다소 걸릴 수 있음

도메인 변경후 접속 테스트를 해서 통과가 되는 경우 HTTPS 설치로 넘어가자.

**HTTPS 설치 - letsencrypt + certbot 이용**

[https://certbot.eff.org/](https://certbot.eff.org/)

으로 가서 맞는 환경을 선택...

우선 우분투 버전 체크

```
lsb_release -a

--------------------------

Distributor ID: Ubuntu
Description:    Ubuntu 16.04.3 LTS
Release:        16.04
Codename:       xenial
```

> certbot 환경선택 에서 우분투 16.04 + Nginx 선택

```
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python-certbot-nginx
```

그리고 certbot 인증 절차를 시작한다.

```
sudo certbot --nginx
// 이메일 등록하라고 함
// 기타 몇가지 설정하고 도메인이 꼭 설정이 되어 있어야 함
// 마지막은 http로 온걸 https로 리다이렉트할꺼냐 물어봄
```

그리고 `letencrypt`이용시 3개월마다 갱신해야 한다. 이걸 자동 등록할수 있다.

```
sudo certbot renew --dry-run
```

그리고 `https`로 접속 확인해본다.

마지막으로 제대로 되었는지 `SSL 등급 테스트`를 해보자

> [https://www.ssllabs.com/ssltest/](https://www.ssllabs.com/ssltest/)

![](/assets/ssl.png)

그럼 이제 완료가 되었다. 터미널 끄고 개발하면 된다..ㅠㅠ



추가적으로 nginx 에 파일업로드시 용량부족이 뜨는 경우가 있다. 

```
sudo nano /etc/nginx/nginx.conf

.............
http 부분에
client_max_body_size 2M; //2M 부분에 원하는 용량 적으면 된다. 
.............
sudo /etc/init.d/nginx restart
```



