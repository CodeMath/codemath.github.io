---
layout: post
title:  "[Solidity]이더리움 기반 Dapp 개발기(1) - 블록체인과 탈중앙화"
date:   2018-03-30
excerpt: "[Solidity]이더리움 기반 Dapp 개발기(1) - 블록체인과 탈중앙화"
project: true
tag:
- Solidity
- blockchain
- ethereum
- Decentralized
comments: true
---
## 스터디 안내

```
블록체인에 대해 공부하고, 이더리움 네트워크 기반의 Dapp 개발을 하는데 목표를 두고 있습니다.
```

#### 자료 공개
- SlideShare 에 PPT가 공개됩니다.
	* embaded 형식으로 프로젝트 게시글에 남겨집니다.
- 코드들은 Github 에 공개됩니다.
	* 각 회차에 해당하는 코드가 있을 경우 남겨집니다.

### 슬라이드쉐어(Slideshare)

<iframe src="//www.slideshare.net/slideshow/embed_code/key/3mfZbf2j7qo8BB" width="510" height="420" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/xyrho123/blockchain-study1" title="Blockchain Study(1) - What is Blockchain?" target="_blank">Blockchain Study(1) - What is Blockchain?</a> </strong> from <strong><a href="//www.slideshare.net/xyrho123" target="_blank">DongHyeok Lee</a></strong> </div>

* 슬라이드쉐어에 공유된 자료에서 부족한 내용을 이곳에 남깁니다.


### 탈중앙화(Decentralized)

```
대표적인 예로 토랜트 서비스를 이해하면 된다.
```

토랜트는 P2P 네트워크를 이용한 서비스이다. 중앙서버가 없기 때문에 각각의 사용자 컴퓨터가 노드가 되어 자료를 받아온다. 전체 파일을 조각내어 부분적으로 받는다. 즉, 기존 인터넷 서비스 모델인 '중앙' 서버로 부터 데이터를 받는게 아니기 때문에 서버 점검, 다운, 해킹등으로부터 안전하게 데이터를 공유/저장할 수 있다.


<figure>
	<img src="https://cdn-images-1.medium.com/max/767/1*SSPN9rwkjYw6H9ytByL8QQ.png">
</figure>

어떻게 안전하게 저장하는지에 대해서는 본격적으로 블록체인에 대해서 공부하게 되면, 자연스럽게 이해가 될 것이다.

블록체인을 공부하는데 '탈중앙화'라는 개념이 들어오게 된 것은 "조작 불가능"이라는 장점 때문일 것이다. 어떻게 "조작 불가능"이라는 것을 증명하는지 또한 블록체인에 대해 자세히 공부하면 나온다.


### 블록체인(Blockchain)

먼저 블록체인에 대한 개념을 알아보면,

```
A blockchain, originally block chain, is a continuously growing list of records, called blocks, which are linked and secured using cryptography. Each block typically contains a cryptographic hash of the previous block, a timestamp and transaction data. By design, a blockchain is inherently resistant to modification of the data. It is "an open, distributed ledger that can record transactions between two parties efficiently and in a verifiable and permanent way". For use as a distributed ledger, a blockchain is typically managed by a peer-to- peer network collectively adhering to a protocol for validating new blocks. Once recorded, the data in any given block cannot be altered retroactively without the alteration of all subsequent blocks, which requires collusion of the network majority.
```

라고 한다. [위키피디아](https://en.wikipedia.org/wiki/Blockchain)

영어를 전체 해석하면 좋겠지만, 핵심적인 단어만 알아보자.

```
blockchain 
continuously growing list
Each block typically cotains previous block
resistant to modification of the data
managed by a peer-to-peer network
a protocol for validating new block
```

위키에서 설명한 내용중 핵심적인 내용을 추출했다. 

* 블록체인은 지속적으로 나열되는 리스트이다.
* 각각의 블록은 이전 블록을 포함한다.
* 데이터 수정에 대한 저항성이 있다.(비가변성)
* P2P 네트워크로 관리된다.
* P2P 네트워크 프로토콜을 통해 새로운 블록에 대한 검증을 한다.



### 자료저작권
* 본 자료에서 사용된 이미지 및 로고는 해당 기업 및 저작자에게 있습니다.(요청시 삭제)
* 비상업적 용도로 사용되어야 합니다.
* 이용에 대한 문의는 텔레그램 `@brownowls` 으로 문의주시면 됩니다.