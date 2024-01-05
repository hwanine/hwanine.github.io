---
toc: true
toc_sticky: true
categories:
  - Javascript
title: 자바스크립트와 멀티쓰레드 및 비동기 (w. 이벤트 루프, 웹 워커)
tags: [c++, registry]
excerpt: "자바스크립트와 동작방식과 멀티쓰레드와 연관된 이벤트 루프, 웹 워커 등 상세히 알아보자."
---

# 자바스크립트와 멀티쓰레드

## 자바스크립트는 멀티쓰레드로 동작이 될까

우선 내용을 살펴보기 전에 쓰레드와 비동기방식에 대해 이해할 필요가 있다.  
아래 포스팅 링크로 이동하여 참고하도록 하자.

[https://hwanine.github.io/android/MultThread/](https://hwanine.github.io/android/MultThread/)

위 링크에 자세하게 설명되어있으나, 간단히 아래 이미지를 통해 설명이 가능하다.  
메인 프로세스에서 여러개의 쓰레드를 생성하여 작업을 병렬처리 가능하도록하는 것이 멀티쓰레드 동작 방식이다.

![Alt text](/assets/images/240106_0.png)

<br>

그럼 자바스크립트는 멀티쓰레드로 동작을 할 수 있을까?  
그렇지 않다. 자바스크립트는 싱글쓰레드 스크립트 언어로서 싱글쓰레드로만 동작이 가능하다.

```javascript
setTimeout(() => {console.log("1")}, 0);
while(true) { console.log("2") };
console.log("3");
```

<br>

위의 코드를 실행한다고 하면 결과는 "1"의 반복으로 출력 될 것이다.  
"3"은 그렇다쳐도 Web API의 `setTimeout` 함수는 동작해야하는 것이 아닐까 생각할 수도 있다.

하지만 "1"은 출력되지않고 이것이 자바스크립트가 싱글쓰레드 언어라는 증거가 된다.

그럼 우리가 자바스크립트를 사용할 때 흔히 멀티쓰레드로 동작하는 것처럼 느껴지는 `setTimeout` 뿐만 아니라 `ajax`나 `콜백함수`, 여러 이벤트 수신 함수들은 어떻게 동작하는 걸까?  

<br>

## 자바스크립트의 동작방식

![Alt text](/assets/images/240106_1.png)

위 이미지는 자바스크립트가 동작하는 방식을 도식화 해놓은 이미지이다.  
정리하면 다음과 같다.

1. 자바스크립트는 Call Stack 1개만을 관리하여 호출된 요소들을 선입선출로 처리하고있다.
2. Web APIs의 Ajax나 Timeout 같은 함수나 콜백함수들은 호출되면 Callback Queue로 들어간다.
3. 이벤트 루프가 돌며 Callback Queue에 쌓인 요소들을 Call Stack에 넣어준다.

<br>

따라서 위에서 살펴본 해당 코드에서도 타임아웃 콜백함수가 동작을 하지않은 이유도 여기에 있다.  
Call Stack에는 등록이 되었으나, 이전 요소에 대한 처리가 끝나기를 무한정 대기하고있는 상황인 것이다.

```javascript
setTimeout(() => {console.log("1")}, 0);
while(true) { console.log("2") };
console.log("3");
```

<br>

처리 과정은 아래를 참고해보자.

![Alt text](/assets/images/240106_2.gif)

<br>

## Web APIs 처리방식

Web APIs 함수들이나 여러 콜백함수들이 이벤트 루프를 통해 Call Stack에 쌓이며 순차적으로 처리되는 것은 확인하였다.  
그런데 이런 의문이 생길 수 있다.

예를들어 실제로 Web APIs의 Ajax를 사용할 때, 서버와의 데이터 처리 시간이 상당히 긴 작업임에도 불구하고 타 호출요소들이 스택에서 처리되는것에 큰 영향이 없는 것을 확인 할 수 있다.

분명 싱글쓰레드는 맞는데 Ajax는 어떻게 처리되는 것일까?

<br>

Web APIs에서 제공하는 API함수들은 멀티쓰레드를 지원한다.  
즉, 브라우저 프로그램이 멀티 쓰레드로 동작을 하기 때문에 해당 API도 멀티쓰레드로 동작이 가능하게 되어 동시 처리가 가능하다.  

위 코드와 같은 상황이 발생하였을 때, Stack에서 대기하고 있음에도 사실 `setTimeout`에 처리는 브라우저 쓰레드에서 작업이 완료되었고 이후 CallStack으로 들어오게 된 것이다.

<br>

## Web APIs 처리방식의 한계

브라우저에서 멀티쓰레드를 지원하고 Web APIs에서 멀티쓰레드로 각 API가 동작한다고 하더라도, 처리가 완료되고 Call Stack으로 넘어오면 문제가 발생한다.  
자바스크립트로 넘어와서는 결국 남은 작업은 싱글쓰레드로 처리해야하기 때문이다.

결국 WebAPI에서 제공하는 핵심 기능처리를 브라우저단 멀티쓰레드로 동작하는 것이지 자바스크립트로 넘어와서 콜백함수 실행 코드로 넘어오면 싱글쓰레드로 동작해야하는 것이다.

<br>

## 자바스크립트의 멀티쓰레드

그럼 WebAPIs처럼 자바스크립트에서도 멀티쓰레드는 구현이 불가능한걸까?  
이러한 꾸준한 이슈로 인하여 한 가지 방법이 도입되었다.

바로 `Web workers`를 이용하는 것이다.  
웹 워커를 이용하면 워커에서 작성된 스크립트는 독립된 쓰레드에서 동작되며 무거운 작업을 메인 쓰레드에서 분리하여 리소스들을 최대한 효율적으로 활용할 수 있다.

![Alt text](/assets/images/240106_3.png)

<br>

이렇게 Call Stack으로 처리될 작업들을 외부 쓰레드에서 처리되도록 할 수 있다.

```javascript
// 웹 워커 스크립트 파일(worker.js)
self.addEventListener('message', function(e) {
  // 메인 스크립트로부터 메시지를 받으면 실행할 함수
  var result = "Hello " + e.data;
  self.postMessage(result); // 결과를 메인 스크립트로 전송
}, false);
```

```javascript
// 메인 스크립트에서 웹 워커 사용 예시
var worker = new Worker('./worker.js'); // 웹 워커 객체 생성

worker.addEventListener('message', function(e) {
  // 웹 워커로부터 메시지를 받으면 실행할 함수
  console.log(e.data); // 결과 출력
}, false);

worker.postMessage('World'); // 웹 워커에게 메시지 전송
```

<br>

그럼 왜 자바스크립트는 멀티쓰레드를 활용한 완벽한 병렬 처리를 지원하지않고 싱글쓰레드로 동작하고 `Web worker`를 활용하며 이렇게 동작할 수 밖에 없는 것일까.

<br>

## 자바스크립트는 왜 싱글쓰레드로 만들어졌을까

웹 페이지에서의 보조 기능을 수행하기 위해 간단한 프로그래밍 언어로 처음 도입되었다.  
멀티쓰레드 모델은 프로그래밍 난이도가 높기 때문에 이러한 방식을 채택하지 않았던 것이다.

실제로 멀티쓰레드로 구현된 서비스에서는 동시성 문제가 많이 발생하고 이를 해결하는 것이 만만치 않다. 현재도 그렇지만 과거에는 개발자들이 더욱 신경을 많이 쓰고 애도 많이 먹었기 때문에 복잡한 시나리오를 생각하지 않았던 것이다.

<br>

## 정리

이렇게 자바스크립트의 동작 방식과 싱글/멀티쓰레드 처리과정에 대하여 알아보았다.

흔히 사용하는 Native 프로그래밍 언어인 C, C++, 자바 등으로 개발된 시스템은 OS에서 할당된 프로세스를 갖는다.
그 프로세스에서 여러개의 멀티쓰레드를 직접 생성하고 활용하며 사용해왔다.

반면 자바스크립트는 브라우저 위에서 동작하는 스크립트 언어이기 때문에 개발 과정에서 제한되는 부분이 많다.  
하지만 웹 개발이 개발 세대의 황금기를 맞이하며 점차 Native를 처리하는 프레임워크도 나오고 지속적으로 발전을 거듭하고 있다.

앞으로 트렌드가 어떻게 바뀔지는 모르겠지만, 기술혁신으로 달라질 미래를 기대해본다.  

<br>

- 참고자료
    - [https://ssocoit.tistory.com/269](https://ssocoit.tistory.com/269)
    - [https://miracleground.tistory.com/entry/%EC%9E%90%EB%B0%](https://miracleground.tistory.com/entry/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%8A%94-%EC%99%9C-%EC%8B%B1%EA%B8%80-%EC%8A%A4%EB%A0%88%EB%93%9C%EB%A5%BC-%EC%84%A0%ED%83%9D%ED%96%88%EC%9D%84%EA%B9%8C-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EC%8A%A4%EB%A0%88%EB%93%9C-%EB%B9%84%EB%8F%99%EA%B8%B0-%EB%8F%99%EA%B8%B0-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%97%94%EC%A7%84-%EC%9D%B4%EB%B2%A4%ED%8A%B8%EB%A3%A8%ED%94%84)
    - [https://inpa.tistory.com/entry/%F0%9F%8C%90-js-async](https://inpa.tistory.com/entry/%F0%9F%8C%90-js-async)

	