---
layout: post
title:  "[Django] custom admin page settings"
date:   2018-05-08
excerpt: "How to DJango's custom admin settings"
tag: [python,django,custom,admin,settings,post]
comments: true
---

<center>#Django - custom admin page settings</center>

Django에서 custom으로 admin페이지를 설정하기 위해서는 아주 간단하다.

우선  settings.py에서 다음과 같은 코드와 설정을 추가하면 된다.

```
...

INSTALLED_APPS = [
...
'django.contrib.admin',
...
]
...
TEMPLATES = [
    {
        ...
           'DIRS': [os.path.join(BASE_DIR, 'templates')],

        ...
    }
]

```


여기서 주의해야한다. 즉, 우리가 수정할 django admin에 해당하는 html파일을 복사해서 수정을 해도 계속 예전것으로 나오는 문제가 생길 것이다. 구글링해서 찾긴했다. (http://stackoverflow.com/questions/25727687/django-1-7-updating-base-site-html-not-working)
짜증나는게 계속 바뀌지 않는 것.

그 이유를 들여다 보면, 우리가 custom admin html을 수정환 application이 settings.py의 INSTALLED_APPS 리스트에서 뒤쪽에 나와서 적용이 안되는 것이다. 즉, admin html을 사용할 놈이 django.contrib.admin 이 놈부터 적용되서 그런 것 이다.
따라서 맨 뒤로~ 맨 뒤로~ 보내주면 우리가 커스텀한 놈이 나온다!





