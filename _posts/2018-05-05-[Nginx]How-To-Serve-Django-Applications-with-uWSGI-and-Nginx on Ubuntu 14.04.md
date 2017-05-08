---
layout: post
title:  "[Nginx]How To Serve Django Applications with uWSGI and Nginx on Ubuntu 14.04"
date:   2018-05-05
excerpt: "AWS EC2 Ubuntu - Seeting"
tag: [Nginx,django,ubunt,aws,uwsgi,tutorial,post]
comments: true
---

# AWS EC2 Ubuntu - Seeting

[참조사이트1](https://www.digitalocean.com/community/tutorials/how-to-serve-django-applications-with-uwsgi-and-nginx-on-ubuntu-14-04)
[참조 사이트2](http://codingdojang.com/scode/364)



1) 기본 python 환경설정 및 가상환경 설치

```
sudo apt-get update
sudo apt-get install python-pip
```


우분투에 필수 환경설정을 한다. python-pip 설치 까지 한 후, 가상환경 라이브러리를 설치하면 된다.


```
sudo pip install virtualenv virtualenvwrapper
```

가상환경까지 설치하였다면, 가상환경에 대한 PATH를 설정한다.


```
echo "export WORKON_HOME=~/Env" >> ~/.bashrc
echo "source /usr/local/bin/virtualenvwrapper.sh" >> ~/.bashrc
```

bashrc에 위 두 명령어를 입력하게 되고,

```
source ~/.bashrc
```

최종적으로 완료하면 가상환경까지 설치되었다.


2) 가상환경에서 장고를 설치하자.

```
mkvirtualenv firstsite
```

가상환경에 대한 폴더를 생성하고,


```
pip install django
```

장고를 pip를 이용하여 설치한다. (이때 각 프로젝에 필요한 라이브러리들을 설치하면 된다.)

```
django-admin.py startproject firstsite
```

장고 프로젝트를 하나 만든다.

```
python manage.py migrate
```

우선 migrate를 이용하여 최신화한다.

```
python manage.py createsuperuser
```

DB의 슈퍼유저를 생성한다.


```
nano firstsite/settings.py
```

먼저 만든 프로젝트의 setting.py에 들어간다.


```
STATIC_ROOT = os.path.join(BASE_DIR, "static/")
```

STATIC 파일에 대한 경로를 설정한다.


```
python manage.py collected_static
```

실제 프로젝트를 배포하기 위한, STATIC들을 한 곳에 모아둔다.


3) uWSGI Application Server 만들기


```
sudo apt-get install python-dev
```

uWSGI를 설치하기 위해, 가상환경에서 나와서 글로벌하게 설치해준다.

```
sudo pip install uwsgi
```



```
uwsgi --http :8080 --home /home/ubuntu/Env/talblro_api --chdir /home/ubuntu/talboro_api -w talboro_api.wsgi
```

TEST하기 위해서 위의 명령어를 입력하지만, 여기서 경로에 대한 값은 언제나 바뀔 수 있다는 것을 명심해야한다.
(Env에 대한 경로와  chdir의 경로를 잘 파악해서 입력해준다.)




4) Configuration Files Setting


```
sudo mkdir -p /etc/uwsgi/sites
cd /etc/uwsgi/sites
```

앞서 만든 uwsgi에 대한 설정파일을 만든다.


```
sudo nano firstsite.ini
```



```
[uwsgi]
project = firstsite
base = /home/ubuntu

chdir = %(base)/%(project)
home = %(base)/Env/%(project)
module = %(project).wsgi:application

master = true
processes = 5

socket = %(base)/%(project)/%(project).sock
chmod-socket = 664
vacuum = true
env = LANG=en_US.UTF-8
```

항상 처음 시작은 [uwsgi]로 시작한다.
그리고 중복적으로 사용될 변수 (project / base)와 같은 것들은 앞에서 선언해주고 재사용하면 된다.

chdir : 앞서 만든 장고프로젝트의 경로
home: 가상환경 Env에 대한 경로
module: 프로젝트 wsgi 파일 

master: 관리자
processes: 쓰레드라고 생각하면 된다. (일하는 놈)

다음으로 네트워크 포트를 설정하기 위해서 Unix소켓을 이용할 예정이다.
HTTP가 아닌, uWSGI의 uwsgi프로토콜을 이용할 것이다. (더 빠르고 쉽다. / 여기서는 nginx를 이용한다.)

여기서 vacuum이라는 것은, 소켓 파일에 대한 삭제이다.
즉, 서비스가 멈췄을 때, 소켓 파일을 자동으로 삭제할 것인가? 에 대한 것이다.


설정파일 만든 것을 최신화시켜주자.

```
sudo nano /etc/init/uwsgi.conf
```




```
description "uWSGI application server in Emperor mode"

start on runlevel [2345]
stop on runlevel [!2345]

setuid ubuntu
setgid www-data

exec /usr/local/bin/uwsgi --emperor /etc/uwsgi/sites
```


5) Nginx as  a Reverse Proxy


```
sudo apt-get install nginx
```

역 프록시를 하기 위해서(HTTP가 아닌) nginx를 설치해준다. (글로벌)


```
sudo nano /etc/nginx/sites-available/talboro
```

아파치와 비슷하게 설정파일을 셋팅한다.


```
server {
    listen 80;


    client_max_body_size 5M;
    
    location = /favicon.ico { access_log off; log_not_found off; }
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error1.log;
    
    location /static {
        autoindex    on;
        alias /`home/`ubuntu``/`firstsite/`collected_static;

    }
    
    location /media {
        autoindex on;
        alias /home/```ubuntu```/``firstsite``/media/;
    }
    
    location / {
        include         uwsgi_params;
        uwsgi_pass      unix:/home/ubuntu/firstsite/firstsite.sock;
    }
}
```

80포트로 들어오게 하고
server_name에는 도메인 값을 써준다.(없다면 말고)

우선 파비콘 오류를 제외해준다. (로그가 쌓이니까…)
static폴더 위치값을 설정해준다. 
(root /home/`ubuntu`/firstsite; 이거 드럽게 오류남....)

그리고 uwsgi_pass에 대한 소켓 경로를 설정해준다.

6) 에러/오류 로그 쌓기
각각의 로그파일들에 대한 경로를 설정해주면 된다.




```
`sudo ln -s /etc/nginx/sites-available/firstsite /etc/nginx/sites-enabled`
```

아파치처럼 available에 만든 nginx를 enabled로 복사한다.



```
sudo service nginx configtest
```

설정파일 테스트를 한다. 
여기서 OK라고 뜨면 완성된 것이다. Fail이라고 뜨면 아까 만든 파일을 다시 확인해 볼 것.


```
sudo service nginx restart
```

전체 nginx를 재시작


```
`sudo service uwsgi start`
```

uwsgi 스타트 한다. (최초 1번)



**주의할 점**

1) AWS EC2 Instance를 사용할 경우, security group에서 HTTP 포트를 열어줘야한다.
2) Nginx의 default파일을 삭제해줘야한다. 안그러면 귀찮다.
3) 프로젝트 파일 수정후, 적용하려면 현재 서비스를 멈추고 나서 해야 적용된다.


```
sudo service uwsgi stop
```

서비스를 멈추고 나서, 

```
sudo service uwsgi start
```

재시작을 하면 자연스럽게 적용된다.


현재 conf 파일 확인

```
sudo nginx -t
```


[참조할 사이트](https://uwsgi.readthedocs.io/en/latest/tutorials/Django_and_nginx.html)




* EC2에서 S3 접근을 위한 AWS IAM configur

ubuntu에 먼저 설치할 것

```
sudo apt-get install awscli
```



```
`$ aws `configure`
AWS Access Key ID [None]: 
AWS Secret Access Key [None]: 
Default region name [None]: ``ap-northeast-2``
Default output format [None]: `ENTER``
```




**uWSGI**

application container server(application server)의 일종이다. application으로 향하는 길목의 (**WSGI** 형식의) interface 역할을 맡는다. 클라이언트의 HTTP 요청을 (우리의 경우 **Python** Web Framework인 Django로 application을 개발했으므로) application이 처리할 수 있는 Python 호출로 번역하는 역할을 맡는다.


**Nginx**

web server의 일종이다. 클라이언트와 uWSGI 사이에 위치하여 클라이언트의 요청을 uWSGI에게 Reverse Proxy 해주는 역할을 맡는다. **Reverse Proxy**에 대해서 자세히 모르지만, 위상적으로 생각해보면 앞단에 들어오는 여러 다발(클라이언트는 여러명)의 요청을 한다발(Nginx와 uWSGI를 사이의 연결은 하나)로 묶어서 뒷단에 전달해준다고 생각해볼 수 있겠다. 또한 정적 컨텐츠를 처리할때는 Nginx의 performance가 더 뛰어나고, security feature 면에서도 뛰어나기 때문에 Nginx가 잘하는 부분은 Nginx에게 맡기는 것이다. 

