---
layout: post
title:  "Jupyter notebook in django"
date:   2018-01-15
excerpt: "Jupyter notebook in django"
tag:
- Django
- Jupyter
- python
comments: true
---


## Jupyter notebook in django


로컬에서 장고쉘을 띄울 때, jupyter notebook 을 활용하면 편리합니다.

이는 `django-extensions` 의 `shell_plus --notebook` 명령을 통해 사용하실 수 있습니다.

[Install link](https://django-extensions.readthedocs.io/en/latest/)


#### 설치법
- `pip install django-extensions`
- 프로젝트/settings 내 INSTALLED_APPS 에 "django_extensions" 추가

```
python manage.py shell_plus --notebook
```

명령으로 notebook 서버 실행하기. 


이때 새 노트북 생성 시에 필히 `Django Shell-Plus` 선택.
이제 각종 장고 관련 리소스들 (모델, 템플릿 등) 을 jupyter notebook 을 통해서 실행하실 수 있습니다.


#### Anaconda 오류
그런데 Anaconda 윈도우 환경에서 CommandError: No notebook (Python) kernel specs found 오류를 만나실 수 있습니다. 이는 shell_plus 명령 내에서 python3 혹은 python 이름으로 kernel 을 찾기 때문입니다. 윈도우에서는 Kernel 명이 대개 "Python [Root]" 라는 이름을 가집니다.


가용한 kernel 목록은 다음 코드로 확인하실 수 있습니다.

{% highlight python %}
from nb_conda_kernels import manager
kernel_specs = manager.CondaKernelSpecManager().find_kernel_specs()
print(kernel_specs)
{% endhighlight %}

위에서 확인한 Kernel 목록 중에서 사용하고자 하는 Kernel 을 장고프로젝트/settings 내 NOTEBOOK_KERNEL_SPEC_NAMES 설정으로 추가하신 후에, shell_plus --notebook 명령을 입력하시면 잘 수행이 됩니다.

```
NOTEBOOK_KERNEL_SPEC_NAMES = ['Python [Root]']
```


