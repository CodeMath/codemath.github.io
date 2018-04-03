---
layout: post
title:  "[Solidity]이더리움 기반 Dapp 개발기(2) - Solidity 언어 정리"
date:   2018-04-03
excerpt: "[Solidity]이더리움 기반 Dapp 개발기(2) - Solidity 언어 정리"
project: true
tag:
- Solidity
- blockchain
- ethereum
- Decentralized
comments: true
---

```
Solidity 언어 정리. (^0.4.19 기준)

1) 기본내용 추가(18.4.3)
```


### 구조체/배열/매핑

##### 구조체
`struct`는 구조체라고 한다. 쉽게 이해하려면 파이썬이나 swift의 `class` 라고 보면 된다.

```
struct Person {
  uint age;
  string name;
}
```

Person이라는 구조체를 만들고서, 필요할 때 마다 새롭게 가져다 쓸 수 있는 것이다.

##### 배열
배열에는 두 가지로 정적 배열, 동적 배열이 있다. 

* 정적 배열
정적배열은 배열의 길이가 고정된 것이다.
```
uint[2] fixedArray;
```

* 동적 배열
동적배열은 배열의 길이가 가변적인 것이다. 고정된 크기가 없기 때문에 무한정 커질 수 있다.
```
uint[] dynamicArray;
```


앞서 구조체에서 만든 `Person`을 가져다, 동적배열을 이용해 데이터를 저장할 수 있다.

```
Person[] people
```

`people` 이라는 동적배열을 이용해 데이터를 저장할 수 있게 했다. 동적배열 내부에는 `Person`이라는 구조체를 사용한 것이다.


`public` 배열을 이용해 getter 매소드를 자동 생성할 수 있게한다.

`Person[] public people` 이라고 작성하면 된다. (물론 퍼블릭이기 때문에 다른 사람들도 볼 수는 있다.)

앞서 만든 `public` 배열에 데이터를 추가해보자.

```
Person satoshi = Person(32, "satoshi");

people.push(satoshi);

// or

people.push(Person(32, "satoshi"));
```

`push`는 마지막에 원소를 추가한다.

##### 매핑
`mapping` 은 구조화된 데이터를 저장하는 구조체와 배열과 다른 또 다른 방법.

```
mapping (address => uint) public accountBalance;
```

파이썬의 dict와 비슷(?)한 느낌. `key-value` 저장소를 말함. 데이터를 저장/검색할 수 있다. 즉, 위의 예시에서는 `address` 가 `key`가 되고 `uint`인 값이 저장된다.



### 함수

#### 기본 함수

솔리디티에서 함수는 자바스크립트와 매우 유사하다.

```
function createPerson(string _name, uint _age){

}
```


#### private/public function

기본적으로 솔리디티의 함수는 public. 숨길 함수는 private로 지정하면 된다.

```
function createPerson(string _name, uint _age) private{

}
```


#### returns 

함수에서 반환값을 받을려면 다음과 같이 한다.

```
string hi = "Hello World!";
function sayHello() public retuns(string){
	return hi;
}
```

특이하게도 반환값의 타입을 지정해줘야한다.


#### view/pure 

위 함수처럼 `상태 변화`가 없는 함수에는 `view`를 붙인다. 즉, 함수는 데이터를 보기만하고 변경하지 않는다는 뜻이다.
```
function sayHello() public view retuns(string){ }
```


`pure` 함수는 앱에서 어떤 데이터도 접근하지 않는 것을 의미한다.

```
function _multiply(uint a, uint b) private pure returns (uint) {
  return a * b;
}
```

위 곱셈 함수를 보면, 앱에서 데이터를 받는게 아니라 직접 인자를 받아 처리하기 때문에 `pure`를 선언해주면 된다.


#### 접근 제어자

서로 다른 컨트렉트에 있는 함수 호출 시, 접근 제어를 걸 수 있다.

`internal` 은 함수가 정의된 컨트렉트를 상속하는 컨트렉트에서도 접근 가능. (private와 비슷)

`external` 은 함수가 컨트랙트 바깥에서만 호출될 수 있다. 컨트랙트 내의 다른 함수에 의해 호출 될 수 없다.

```
contract Sandwich {
  uint private sandwichesEaten = 0;

  function eat() internal {
    sandwichesEaten++;
  }
}

contract BLT is Sandwich {
  uint private baconSandwichesEaten = 0;

  function eatWithBacon() public returns (string) {
    baconSandwichesEaten++;
    // eat 함수가 internal로 선언되었기 때문에 여기서 호출이 가능하다 
    eat();
  }
}
```


#### 다중 반환값 처리
```
function multipleReturns() internal returns(uint a, uint b, uint c) {
  return (1, 2, 3);
}
```

순서대로 처리 된다. 만약 빈값을 중간에 떨구려면 `(,2,3)` 이런식으로 하면 된다.


### keccak256/형변환


#### keccak256
이더리움은 SHA3의 한 버전인 keccak256를 내장 해시 함수로 가지고 있다. 해시 함수는 기본적으로 입력 스트링을 랜덤 256비트 16진수로 매핑한다.


#### 형변환

솔리디티에서 형변환 시 주의해야한다. `uint` 와 `uint8` 사칙연산 시 오류발생한다. 동일한 타입으로 변형시켜줘야한다.

```
uint8 a = 5;
uint b = 6;

uint8 c = a * b;  // 오류발생

uint8 c = a * uint8(b); // 정상
```



### 이벤트

#### 이벤트

특정 이벤트가 발생하는지 체크하는 것이다.

```
event IntegerAdded(uint x, uint y, uint result);

function add(uint _x, uint _y) public {
  uint result = _x + _y;
  IntegersAdded(_x, _y, result);  // 여기서 이벤트를 실행해서 add 함수가 실행됨을 알림
  return result;
}
```



### msg.sender

#### msg.sender

솔리디티의 전역변수. 여기서 `msg.sender` 는 현재 함수를 호출한 사람(계정 주소)을 가르킨다. 

앞서 설명한 매핑을 이용해 호출한 사람이 누구인지 저장할 수 있다.

```
mapping (address => uint) Number;

function getNumber(uint _Number) public {
  Number[msg.sender] = _Number;
}
```

매핑을 만들어 저장한다. 이때 매핑에 들어갈 키값은 `msg.sender`로 함수를 호출한 계정주소값에 해당한다.


### Require

#### Require

^0.4.8 버전에서 사용했던 `throw`가 없어지고, 생겨난 것. 특정조건에 해당하는 값(참)이 아닌 경우 에러발생시킨다.

```
function sayHi(string _name) public returns (string) {

  require( _name == "satoshi");

  return "Hi!";
}

```

위 함수의 인자로 들어온 값이 "satoshi"가 아니면 에러가 발생한다.


### 상속/다른 파일 가져오기/상호작용

#### 상속

자바 클래스 상속처럼 생각하면 된다. (쉽다)

```
contract Dog {
  function catchphrase() public returns (string) {
    return "So Wow CryptoDoge";
  }
}

contract BabyDoge is Dog {
  function anotherCatchphrase() public returns (string) {
    return "Such Moon BabyDoge";
  }
}
```

`BabyDoge` 가 `Dog`를 상속한다. 즉, `BabyDoge` 컨트렉트 에서 `catchphrase` 함수에 접근할 수 있다는 뜻이다.


#### 다른 파일 가져오기
파이썬과 비슷하다. 

`import "./other.sol" ` 이라고 맨 위에 가져오면 된다.


#### 상호작용
블록체인 상에 있으면서 우리가 소요하지 않은 컨트랙트와 우리 컨트랙트가 상호작용을 하려면 우선 인터페이스를 정의해야한다.

```
contract LuckyNumber {
  mapping(address => uint) numbers;

  function setNum(uint _num) public {
    numbers[msg.sender] = _num;
  }

  function getNum(address _myAddress) public view returns (uint) {
    return numbers[_myAddress];
  }
}
```

위 컨트랙트는 계정 주소를 이용해 특정 수를 저장할 수 있다. 예를 들어 `getNum` 함수를 이용하여 이 컨트랙트에 있는 데이터를 읽고자 하는 `external` 함수가 있다고 가정하자. 먼저 컨트랙트의 인퍼에시를 정의하면

```
contract NumberInterface {
  function getNum(address _myAddress) public view returns (uint);
}
```

인터페이스를 정의하는 것은 컨트랙트 정의와 비슷. 다만, 다른 컨트랙트와 상호작용하고자 하는 함수만을 선언할 뿐.

다른 함수나 상태 변수를 언급하지 않음.

```
contract OddNumber {

	NumberInterface numbercontract = NumberInterface(msg.sender);
	...
}
```

`OddNumber` 컨트랙트에서 `NumberInterface`의 `getNum`을 사용하기 위해 인터페이스를 사용.



### 스토리지/메모리

#### 스토리지
Storage는 블록체인 상에 영구적으로 저장되는 변수를 의미한다. Like as 하드디스크.

#### 메모리
Memory는 말 그대로 임시 저장되는 변수. 컨트렉트 종료 시 날라감.


#### 언제 사용할까?

함수 내의 구조체와 배열 처리시 사용.

```
// (1)
contract SandwichFactory {

  struct Sandwich { // (2)
    string name;
    string status;
  }

  Sandwich[] sandwiches; // (3)

  function eatSandwich(uint _index) public { // (4)
    
    Sandwich storage mySandwich = sandwiches[_index]; // (5)
    
    mySandwich.status = "Eaten!"; // (5)
    
    Sandwich memory anotherSandwich = sandwiches[_index + 1]; // (6)
    
    anotherSandwich.status = "Eaten!"; // (6)
    
    sandwiches[_index + 1] = anotherSandwich; // (7)
    
  }
}

```

(1)`SandwichFactory`라는 컨트렉트 내부에, (2)구조체인 `Sandwich`가 있다.

그리고 `Sandwich` 구조체를 이용한 (3)`sandwich` 배열을 만들었다.

(4) `eatSandwich` 함수를 살펴보면,


```
Sandwich storage mySandwich = sandwiches[_index]; // (5)
    
mySandwich.status = "Eaten!"; // (5)
```

이 부분은 `sandwiches`의 배열에서 `_index`에 해당하는 원소의 `status` 값을 영구적으로 변경시킨다.

`mySandwich` 는 저장된 `sandwiches[_index]`를 카르키는 포인터라고 생각하면 된다.

* * *

```
Sandwich memory anotherSandwich = sandwiches[_index + 1]; // (6)
    
anotherSandwich.status = "Eaten!"; // (6)
```

단순히 복사를 하고자 한다면 memory를 이용. `anotherSandwich` 은 메모리에 데이터를 복사해둔 것.

그런데 `anotherSandwich.status` 는 `anotherSandwich` 값을 변경하는 것이므로, 원래 변경하려고 했던 `sandwiches[_index + 1]` 에는 아무런 영향을 끼치지 않는다. 따라서 추가적으로 코드를 작성해줘야한다.

```
sandwiches[_index + 1] = anotherSandwich; // (7)
``` 

(7) 코드를 작성해주면, 임시 변경한 내용을 블록체인 저장소에 저장할 수 있다.


> 물론 솔리디티 상에서 명시적으로 친절하게 알려준다. 걱정말라한다.



### if 문

#### if 문

솔리디티의 if문은 자바스크립트와 동일하다.

```
function eat(string sandwich) public {
	if (sandwich == "tomato"){
		eat();
	}
}
```




