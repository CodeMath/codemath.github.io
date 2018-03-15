---
layout: post
title:  "Django-unicode file upload error"
date:   2018-01-20
excerpt: "Django-unicode file upload error"
tag:
- Django
- UTF8
comments: true
---


## Django - unicode file upload error

사실 django 문제가 아니라 uwsgi 에 환경 언어 설정을 추가해주면 된다.

`wsgi.ini` 에 `env = LANG=en_US.UTF-8` 추가하고 uwsgi 재시작.
