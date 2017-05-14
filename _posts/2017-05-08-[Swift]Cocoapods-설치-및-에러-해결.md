---
layout: post
title:  "[Swift]Cocoapods 설치 및 에러 해결"
date:   2017-05-08
excerpt: "Cocopods 설치 및 에러 해결"
tag: [Swift,cocoapods,ios,xcode,post]
comments: true
---

설치 코드는 터미널에서

```
sudo gem install cocoapods
```

(오래걸린다.)

그리고 위 코코아팟을 다 설치하고 나면, 해당 프로젝트 경로로 가서
touch podfile
으로 초기화 파일을 만든다.

그리고 해당 podFile을 열어서 라이브러리를 설치해준다.

그런데 간혹 가다가 이상한 에러가 발생한다.

```
Pod installation complete! There is 1 dependency from the Podfile and 1 total pod installed.

[!] The `ZuphaGo [Debug]` target overrides the `OTHER_LDFLAGS` build setting defined in 
`Pods/Target Support Files/Pods-ZuphaGo/Pods-ZuphaGo.debug.xcconfig'. 
This can lead to problems with the CocoaPods installation 
- Use the `$(inherited)` flag, or 
- Remove the build settings from the target.

```

무슨 말인지 설명하면, 코코아팟을 이용해서 라이브러리를 오버라이드할 때 환결 설정이 문제가 된 것이다.
따라서 저 말에 따른 해결방법으로

(1) 프로젝트/타겟 → Build Settings에서 “ALWAYS_EMBED_SWIFT_STANDARD_LIBRARIES”를 검색
그리고 디벅그와 릴리스 둘 다, “$(inherited)”를 추가해준다.

그래도 해결이 안될 것이다.

두번째로 해결해야하는 부분이

(2) Build Settings에서 Other Linker Flags에 들어간다. 그리고 기존에 -ObjC로 되어있는  부분을 삭제 한다.


그러면 pod으로 프레임워크 라이브러리는 설치가 된 것이다.


하지만 여기서 문제, 설치가 되었다고 다 열리는게 아니란다....
즉, 해당 모듈을 가져올 수 없다는 어처구니없는 오류가 나온다.

우리가 설치한 프레임워크를 따로 추가해줘야한다. (귀찮)

(1) Build Phases 에서  Link Binary With Libraries 에 해당 프레임워크를 추가한다. (여기서 기존의 ###.xcodeproj 로 하면 안될 수 있으니 추가로 만들어진 ###.xcworkspace 파일을 열어 추가한다.

(2) 그리고 혹시나.... 해서 하는데, 만약 추가해도 에러 표시가 난다면 한 번 빌드를 해주자. 그러면 아무런 문제가 없을 것이다~

