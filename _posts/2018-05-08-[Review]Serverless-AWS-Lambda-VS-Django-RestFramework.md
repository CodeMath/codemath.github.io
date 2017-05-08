---
layout: post
title:  "[Review]Serverless AWS Lambda VS Django-RestFramework"
date:   2018-05-08
excerpt: "[Review]Serverless AWS Lambda VS Django-RestFramework"
tag: [aws,serverless,server,lambda,review,post]
comments: true
---


새로운 테스팅 앱을 만들기 위해서 서버를 만들어야 했다.
사실 매번 EC2를 만들면서 환경설정을 직접 명령어로 쳐줘야했었다. 물론 도커로 그냥 필요할 떄마다 이미 저장된 컨테이너를 불러와도 되지만,
여전히 나에겐 귀찮은 존재.... “서버”

마침 “유행”?인듯 아닌 듯 “Serverless”라는 것이 화두되었고, 어짜피 테스팅 앱으로 만들 서버를 이번 기회에 Serverless하게 바꿔보기로 했다.

[ 참고사항 : Python 2.7 / 각 코드 및 설정마다 오류가 있을 수 있습니다. 단순히 테스팅 목적을 위한 서버라... ]


여튼 기존에 로컬에서 DRF로 만든 API를 함수형태로 바꿔서 정리하였다.

막상 doc를 보면서 코드를 올리고 테스트해보려 한 순간.... 라이브러리 문제다. 제대로 import 할 수 없다고 한다....
(.... 경로....) 파이참을 사용하고 있던 터라 가상환경의 라이브러리를 제대로 import 못한 것이다. (여기서 1차멘붕)

가상환경에 설치한 몇몇의 라이브러리들을 꺼내와 파이썬 파일과 합쳐서 업로드 하고 테스트하였다.

(get 이나 그냥 print 로 return 하는 부분은 쉬우니 넘어가도록 함.)
사실 상 2차 멘붕이 오기시작한 부분은 “RDS”에서 데이터를 가져오기 위해서는 '쿼리문'을 작성해야한다는 것이다. (오우쉣)

그동안  Django에서 얼마나 쉽게 데이터를 가져왔나? ㅠㅠ


```
# Django
User.objects.all()
Device.objects.filter(uuid= Is_uuid)

#mySQL
"select * from auth_user"
"select * from Device where uuid='%s'" %(Is_uuid)
```

그나마 파이썬으로 쿼리문 작성하니 그나마....그나마 편하지만, 그래도 어~~엄청 불편했다. (그러게 mysql 공부하기 싫다고 땡깡부리지 말랬지?ㅠ)

그래도 그나마 RDS 연결 관련해서는 AWS 문서에 예제로 잘 나와있다. (고마워요~)

함수 파일말고 라이브러리 처럼 import하는 방식으로 rds 정보를 입력해두고서, 메인 파일에서 불러와 사용한다.
예를 들어

```
import sys
import logging
import rds_config

#rds settings
rds_host  = "mysql-rds-host"
name = rds_config.db_username
password = rds_config.db_password
db_name = rds_config.db_name


logger = logging.getLogger()
logger.setLevel(logging.INFO)

try:
    conn = pymysql.connect(rds_host, user=name, passwd=password, db=db_name, connect_timeout=5, charset="utf8", use_unicode=True)
except:
    logger.error("ERROR: Unexpected error: Could not connect to MySql instance.")
    sys.exit()

logger.info("SUCCESS: Connection to RDS mysql instance succeeded")
```


이런식으로 코드를 먼저 돌리고 hadler처리를 하면 된다. (쉽당)

클라우드 왓치와 함께 로그를 저장하고 모니터링할 수 있어서 왠걸.... 걍.... 이지하다.


사실 좀 멘붕은 아니고 당황스러웠던 점은, Django에서는 Httpresponse로 json을 dumps시켜서 넘겨줬었는데 여기서는 그냥 dict 형태로 던져도 알아서 json으로 읽어올 수 있었다. (왜지?왜지?왜지?왜지?)
(사실,  response body에서 나오는 부분의 컨텐츠 타입을 API Gateway에서 json으로 처리해버린다는 것을 보기전까지만 해도 이해가 되지 않았다....ㅠ)

여튼 그냥 dict로 넘겨버릴 수 있다니.... python 사용자 입장에서는 정말 말 그대로 개이득이다.

테스팅으로 돌렸을 때,

{% highlight liquid %}
{% raw %}
{% capture images %}
	https://github.com/CodeMath/codemath.github.io/blob/master/assets/img/media/2018-05-08-%5BReview%5DServerless-AWS-Lambda-VS-Django-RestFramework-img-1.png?raw=true
{% endcapture %}
{% include gallery images=images caption="AWS Lambda output" cols=1 %}
{% endraw %}
{% endhighlight %}

음... 그렇다. 가장 적은 메모리로 설정하고 돌렸더니, 122.73.ms가 나온다. (lambda test)
그렇기 때문에 빌링은 200ms로 산정된다. (나쁜놈들 ㅠ 무조건 올리냐....쪼금만 바주....라...) ㅠㅠ

여튼 1차 테스트에서는 이정도 속도가 나오긴한다.

그리고 API Gateway에 물려서 API로 만들어 준다. (자세한 방법은 다음에....)

그리고 Rest client 앱으로 테스트 해봤다.

{% highlight liquid %}
{% raw %}
{% capture images %}
	https://github.com/CodeMath/codemath.github.io/blob/master//assets/img/media/2018-05-08-%5BReview%5DServerless-AWS-Lambda-VS-Django-RestFramework-img-2.png?raw=true
{% endcapture %}
{% include gallery images=images caption="AWS Lambda HTTP 200" cols=1 %}
{% endraw %}
{% endhighlight %}

으악.... 속도가 늘었다.... 분명 Lambda에서는 122였는데....  아마도 캐싱이 안된 데이터를 가져오다 보니 그런 듯 하다.
(실제 동일한 주소로 데이터를 불러오니 속도가 53ms로 확 줄었다.)

아마도 최종 빌링 값은 100 또는 200 (캐싱안되 있다면....300)으로 나올 것 같다.

**현재 AWS 프리티어로 Lambda를 사용할 경우, 가장 적은 메모리(128)을 사용하면 320만 ms를 무료로 사용할 수 있다. (물론 프리티어 끝나고도 쭈욱 이어진다고 한다.)**

AWS Lambda를 사용하면서 느낀 강점은 각 API를 잘게 쪼개 하나의 핸들러로 관리할 수 있다는 장점이다. (이게 어마어마 하게 편하다.)
각 기능이나 추가되는 부분에 대해서 핸들러의 연결성이 낮아져 관리와 업데이트가 참 용이하다는 것을 느꼈다.(으앙 지리뮤)

그동안 DRF를 이용하면서 Swagger를 만들고 테스팅하게끔하는 부분이 정말 귀찮을 만큼?! 짜증이 나긴했었는데.... 이건 뭐 엄청 편해졌다.
(사실 그동안 주의깊게 안봐서 그렇지 API Gateway도 진짜좋다....ㅎ)

