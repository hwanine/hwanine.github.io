---
toc: true
toc_sticky: true
categories:
  - Web
title: 웹 & HTTP & Docker
tags: [Web, HTTP, Docker]
excerpt_separator: " 웹 & HTTP & Docker의 관계"
---

* **연구주제** : 웹 & HTTP & Docker
* **연구목적** : 웹 프로그래밍 기초지식 습득
* **연구일시** : 2019년 12월 30일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Lightsail, Docker
* **관련연구** : AWS, Linux, Unix, Ubuntu, OS

&nbsp;

## 서론
21세기 우리는 흔히 인터넷을 사용하고 있다. 그럼 인터넷이 어떻게 이루어 지는지, 그 속의 네트워크와 웹의 개념은 무엇인지 알아보도록 한다. 그리고 Docker에 대해서도 짧게 학습한다.

&nbsp;

## 본론

Web
======

### 1. What is Web?

![](http://mblogthumb1.phinf.naver.net/20160711_72/heungmusoft_14682183958348nL9x_PNG/%B9%E8%B0%E62.png?type=w800)

 웹이란 무엇인가? WWW(World Wide Web)은 우리가 익히 알고있는 용어이다. 여기서 웹이라는 용어는 과연 무엇을 의미하는가? 우선 웹을 알기위해서는 먼저 알아야 할 것들이 있다.

&nbsp;
&nbsp;

> **네트워크**
**네트워크**란 Net + Work의 합성이로써 컴퓨터들이 통신 기술을 이용하여 **그물망**처럼 연결되어 물리적으로 다른 디바이스와 디바이스를 연결해주는 망이다. 쉽게 말하자면 "두 대 이상의 컴퓨터들을 연결하고 서로 통신(이야기)할 수 있는 것" 이것이 네트워크다.

&nbsp;

> **인터넷**
필자는 **인터넷**을 네트워크들의 집합체로 표현한다. 네트워크가 무수히 많아지면서 네트워크간의 통신의 필요성이 대두되었다. 그래서 탄생한 것이 인터넷이다.  TCP/IP 통신규약으로 네트워크와 네트워크를 연결하는, 즉 네트워크 끼리 연결하여 정보를 주고받는 것이 **인터넷**이다.

&nbsp;

>**웹**
그렇다면 다시 초기로 돌아가서 웹이란 무엇인가? 이처럼 네트워크가 형성되고 인터넷이 활성화되어 연결된 디바이스로 사람들이 정보를 공유할 수 있는 정보공간이 바로 **웹**이다.

&nbsp;

![](https://previews.123rf.com/images/ihorsvetiukha/ihorsvetiukha1808/ihorsvetiukha180800017/112241820-world-wide-web-global-network-structure-with-planet-map-on-background-vector-illustration.jpg)
&nbsp;

### 2. Characteristic of Web

웹은 인터넷 상에서 텍스트나 그림, 소리, 영상 등과 같은 멀티미디어 정보를 *하이퍼텍스트* 방식으로 연결하여 제공한다. 

 **하이퍼텍스트**(HyperText)란 문서 내부에 또 다른 문서로 연결되는 참조를 집어 넣음으로써 웹 상에 존재하는 여러 문서끼리 서로 참조할 수 있는 기술을 의미한다. 이 때 문서 내부에서 또 다른 문서로 연결되는 참조를 **하이퍼링크**(Hyperlink)라고 부른다.

&nbsp;

###  3. Configuration of Web
 웹에서 *HTML* 언어를 사용하여 작성된 하이퍼텍스트 문서를 웹 페이지라고 부른다. 이러한 웹 페이지들 중에서 서로 관련된 내용으로 작성된 웹 페이지들의 집합을 *웹 사이트*(Web site)라고 한다.
 
이렇게 작성된 수 많은 웹 페이지들은 하이퍼링크를 통해 서로 연결되어 구성된다.

여기서 하이퍼링크를 따라 다른 웹 페이지들로 이동하는 것을 *웹 서핑*(Web surfing), 사용하는 프로그램을 *웹 브라우저*(Web browser)라고 한다. 

 &nbsp;

HTTP
======
### 1. What is HTTP?

 http란 **H**yper**T**ext  **T**ransfer  **P**rotocol의 약자로 하이퍼텍스트 문서를 교환하기 위하여 사용된 통신 규약이다. 즉, 웹 서버와 클라이언트 간의 통신을 하기 위한 통신 규약이다. TCP/IP 기반으로 서버와 클라이언트의 요청과 응답을 전송한다.
 
 &nbsp;

### 2. How does HTTP work?
 &nbsp;
![](https://k.kakaocdn.net/dn/IU3pd/btqx7kUyY9R/mU3l9wkcuIwwqqyjjmhYwK/img.png)
 

HTTP는 위의 그림과 같이 동작한다. 즉, Client는 Server에게 request(요청)을 보낸다. 그 요청을 받은 Server는 (response)응답을 보낸다.  정리하면 다음과 같다.

* HTTP 메시지는 HTTP 서버와 HTTP 클라이언트에 의해서 해석된다.
* HTTP는 연결 상태를 유지하지 않는 비연결성 프로토콜이다.
* HTTP는 연결을 유지하지 않는 프로토콜이기 떄문에 요청/응답(request/response) 방식으로 동작한다.

 &nbsp;
 
Docker
======
### What is Docker?
![enter image description here](https://post-phinf.pstatic.net/MjAxODA5MjdfMTM5/MDAxNTM4MDI0NzQ4MDIy.QLvfes9oqS29-cAvQ2mLMwP8adbBbxxPBqxQqrWHIlgg.T0xO_BMaRR_aBhWX3PuKKtf6AaqqZSP8G0GnO_k86O4g.JPEG/%ED%88%AC%EC%9D%B4%ED%86%A1_%EB%8F%84%EC%BB%A4_180927.jpg?type=w1200)

**Docker**는 애플리케이션을 신속하게 구축, 테스트 및 배포할 수 있는 소프트웨어 플랫폼이다. 더 자세히 설명하면 리눅스의 컨테이너 기술을 이용하여 가상머신처럼 하드웨어 자원을 완전히 가상화 하지 않고 프로세스들만을 격리시켜 빠르게 애플리케이션 환경을 구축 및 배포할 수 있게 해주는 기술이다.

  &nbsp;

>도커를 왜 사용하까?

도커를 사용하는 중요한 세 가지 이유는 다음과 같다.
 1.  리눅스 커널을 제외한 배포판을 이용하여 각각의 독립된 환경인 컨테이너를 생성하고 관리하므로 가상머신보다 훨씬 가볍고 빠르다.
   
 2. 클라우드 환경에서 엄청난 양의 서버를 관리하기위해서는 조금 더 효율적인 방법이 요구되었다.
   
 3. 서비스 환경을 갖춘 뒤 그것을 이미지로 만들어서 배포하면 여러 서버에서는 그것을 실행하므로써 간단하게 서비스 운영환경이 갖추어 지기 때문이다.

  &nbsp;
  
    도커에서는 컨테이너와 이미지라는 개념을 사용한다. 
    컨테이너는 각각의 서비스를 의미한다면, 이미지는 컨테이너 실행에 필요한 파일이나 
    설정 값등을 의미한다고 볼 수 있다.

&nbsp;
  
>**Image**
어떠한 개발환경을 구축하기위해 필요한 라이브러리 및 패키지를 모아 하나의 파일로 만들어둔 것으로 운영체제의 프로그램과 유사하다.
이미지는 컨테이너와 별개이다. 즉 컨테이너에서 변경한 내용들을 Image에 적용되지 않는다.

  
>**Container**
이미지가 실행된 형태로 컨테이너에서 파일을 만들거나 서비스를 설치하면 컨테이너 레벨에 저장되고 이미지에는 영향이 미치지 않는다.
컨테이너가 종료되더라도 다시 실행하면 이전 상태가 유지된다.
운영체제의 프로세스와 유사하다.

&nbsp;

## 결론
지금까지 웹과 Docker에 대해 알아보앗다. 바로 위에서 설명했듯이 Docker는 Container 개념을 사용한다. 그와 비교되는 개념이 있는데 그것을 가상머신 이라고 한다. 다음에는 가상머신과 컨테이너의 차이점에 대해 알아보록 하겠다.

&nbsp;

## 향후과제
Docker가 사용하는 컨테이너와 가상머신의 차이점 연구

&nbsp;

## 참고자료

<https://ko.wikipedia.org/wiki/%EC%9B%B9> 
<https://dololak.tistory.com/350> 
&nbsp;

*Writer: Jae-Hwan Lee*