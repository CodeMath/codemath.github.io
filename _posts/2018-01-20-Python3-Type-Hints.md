---
layout: post
title:  "Python3 Type Hints"
date:   2018-01-20
excerpt: "Python3 Type Hints"
tag:
- python3
- python
comments: true
---


## Python3 Type Hints

- PEP 484 -fuc annotations (Py 3.6)

{% highlight python %}
def add(a: int, b: int) -> int:
    return a + b
{% endhighlight %}


List 로 return 하기 위해서,

{% highlight python %}
from typing import List

def get_list(a:int, b:int) -> List[int]:
 	eturn List[a,b]
 
# from typing import Any
# def ~ -> List[Any]:
#	return ["a",a,123]

# py 3.6
def func(text:str) -> str:
	result : int = sime_complex_func(text)
	return str(result)
 
# class 정의 시, def __init__

class Person:
	def __init__(self, name: str) -> None:
	self.name: str = ~
 
# 형을 명확하게 정의해줌
from typing import List, Union

List[Union[int, str]] = [1,"1",2,"2"]

from typing import Optional, Union

assert Optional[int] == Union[int, None]
# -> True

# Dict/ Set 에 대해서 각 key,value의 형을 지정가능
# Tuple은 몇 개짜리 인지도 가능 / 각각 어떤 형인지 체크 가능

# Callable 가능


def run(f: Callable[..., int], *args: int) -> int:
	return f(args)

# value 값
def run(f: Callable[Dict[str, int], int], **kwargs: int) -> int:
	return f(kwargs)
 
결국은 annotation 을 변수로 떨궈서 사용가능

#
def fetch(_id: int, category: int) -> Transaction:
	pass 
=>
EntryID = int
def fetch(_id: EntryID, category: int) -> Transaction:
	pass 


# 파일 확장자 : .pyi 로 타입만 명시적으로 작성 가능
# math.py
def add(a,b):
 return a+b

# math.pyi
 def add(a: float, b:float) -> float: ...
 
# => 여기서 pyi 가 먼저 적용(최상위)
# typeshed (pyi 모아둔 곳) 

{% endhighlight %}