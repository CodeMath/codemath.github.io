---
layout: post
title:  "[Python-Flask WEB SSE]-1"
date:   2017-07-18
excerpt: "Flask를 이용한 SSE & openCV로 데이터 전송하기"
tag: [python,opencv,sse,web,post]
comments: true
---

전반적인 프로젝트 flow는 다음과 같다.

<figure>
	<img src="https://github.com/CodeMath/codemath.github.io/blob/master/assets/img/media/SSE_and_openCV.png?raw=true">
</figure>

* * *

이번 프로젝트에서는 openCV를 이용해서 QR code 좌표값을 읽어서 웹에서 보여주는 것을 해보았다.

먼저 opencv를 이용해서 QR code를 읽어서 중심좌표를 구하는 것을 만들어 보았다.

```
https://github.com/zplab/zbar-py
위 github 에서 코드를 이용하였다.
```

### QR-Code 인식방법

(1) 이미지 프로세싱

QR-code 이미지 자체를 별도로 저장하여 opencv로 해당 이미지를 읽어서 웹캠으로 해당 이미지를 보여줄 때 트래킹이 되게끔하는 것이다. 
위 방법으로 먼저 테스트 빌드를 해보니, 상당히 느렸다. 일단 웹캠에서 각각 화면 마다 위에서 읽은 이미지를 보여질 경우 거리에 비례하여 트래킹하는 것이다. 즉, QR-code 이미지의 사이즈가 가깝거나 멀어져도 동일하기 인식하는 것이다. (물론 웹캠의 능력과 노트북 CPU 한계 때문에 그 성능은....)

여튼 위 방법으로 하다보니, 이미지 인식자체는 정확하지만 상당히 느리게 작동하여 다른 방법을 생각해봐야했다.


(2) QR-code와 같은 바코드 형식을 읽자

위 방법과 조금 다르게 바코드(QR-code 포함)를 자체 인식하는 것으로 해보았다. zbar 라이브러리를 이용해서 QR-code나 바코드 형식이기만 하면, 해당 코드의 정보와 위치값을 뽑을 수 있었다. 아마도 for 문을 이용해서 해당 정보나 위치값을 뽑는 것을 보면 멀티값이 적용 되는 듯.


### Redis 연동

(1) Redis로 구독채널을 연결하여 데이터를 SSE로 하기 위해서는, Redis에 연결해야했다. 따라서 매 인식마다 request로 업데이트를 하는 방식을 사용했다. redis에 publish를 이용해서 해당 데이터를 json형식으로 저장했다. (이미 보낼 때 json 이다.)

(2) redis에서 저장한 데이터를 SSE를 이용해서 불러오기(Stream).

```

def event_stream():
	pubsub = db.pubsub()
	pubsub.subscribe('stream')
	for msg in pubsub.listen():
		yield u"data: %s\n\n" %msg["data"]


@app.route('/stream')
def stream():
	return Response(event_stream(), mimetype="text/event-stream"

```




### SSE 연동

SSE에서 해당 주소값에 해당하는 EventSource로 접근한다. 그리고 addEventListener로 'message'로 데이터를 받아온다.


```

var source = new EventSource('/stream')
source.addEventListener('message', function(e){
	console.log(e.data);
});


```
이런식으로 연결하면 된다.




