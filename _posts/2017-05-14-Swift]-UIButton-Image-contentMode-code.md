---
layout: post
title:  "[Swift] UIButton Image contentMode code"
date:   2017-05-14
excerpt: "UIButton Image re-sizing code"
tag: [Swift,UIButton,ios,xcode,post]
comments: true
---


Image를 클릭하면 확대하는 View를 만들어주는 [라이브러리](https://github.com/wxxsw/GSImageViewerController) 를 사용하던 중, 버튼에 들어간 이미지의 비율이 맞지 않은 경우가 있었다.

특히나 아이폰 5,6,6S와 같이 비율이 다르기 때문에 이미지 자체를 비율고정값으로 해줘야한다.
이때 해당 버튼의 이미지 비율을 바꾸는 코드는 다음과 같다.

```
ImageButton.imageView?.contentMode = .scaleAspectFit
```

이렇게 해주면 버튼의 이미지 비율을 바꿀 수 있다.