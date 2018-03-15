---
layout: post
title:  "Django-AWS RDS[Mysql]"
date:   2018-01-20
excerpt: "Django-unicode file upload error"
tag:
- Django
- UTF8
comments: true
---


## Django - AWS RDS [Mysql]

[Github link](https://github.com/PyMySQL/mysqlclient-python)

### Install

Prerequisites

You may need to install the Python and MySQL development headers and libraries like so:
```
sudo apt-get install python-dev libmysqlclient-dev # Debian / Ubuntu
sudo yum install python-devel mysql-devel # Red Hat / CentOS
```

On Windows, there are binary wheel you can install without MySQLConnector/C or MSVC. 


### Note on Python 3:

if you are using python3 then you need to install python3-dev using the following command :

```
sudo apt-get install python3-dev # debian / Ubuntu
sudo yum install python3-devel # Red Hat / CentOS
```
### Install from PyPI

`pip install mysqlclient`

#### NOTE
Wheels for Windows may be not released with source package. You should pin version in your requirements.txtto avoid trying to install newest source package.


{% highlight python %}
# ./settings.py

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3')
        }
    }
{% endhighlight %}



#### change value

{% highlight python %}
# ./settings.py

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': '{dbname}',
        'USER': '{user}',
        'PASSWORD': '{password}',
        'HOST': '{address}',
        'PORT': '3306'
        }
    }
{% endhighlight %}

