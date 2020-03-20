---
layout: post
title: VScode liveshare를 활용한 협업
feature-img: "assets/img/pexels/computer.jpeg"
thumbnail: "https://user-images.githubusercontent.com/57826388/73716645-ef8f5c00-475a-11ea-9070-1a19cce90f82.png"
tags: [vscode, Visual studio code, Live share]
author: Jae-Hwan Lee
excerpt_separator: <!--more-->
---

# VScode liveshare를 활용한 협업
<!--more-->
* **연구주제** : VScode liveshare를 활용한 협업
* **연구목적** : VScode상에서의 협업
* **연구일시** : 2020년 1월 29일 13:00~18:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR
* **관련연구** : VScode

<br/>

## 서론

![image](https://user-images.githubusercontent.com/57826388/73716645-ef8f5c00-475a-11ea-9070-1a19cce90f82.png)

우리는 평소에 워드로 문서작업을 한다. 하지만 개발자들은 워드를 사용하는 경우도 많지만 md를 활용하여 문서작업을 많이 한다. 이에 가장 효율적으로 사용할 수 있는 작업 툴이 바로 VS Code다. 

하지만 VS Code를 활용할 때, 협업을 할 경우 불편한 점이 존재했다. 워드의 경우에는 공유 기능이 존재하여 상대방과 같은 문서에서 작업하며 즉각적인 동기화가 되어 협업에 굉장히 편리하다. 하지만 VS Code에는 그런 기능이 기본적으로 있지 않다.

그래서 찾아본 결과 확장 프로그램으로 `Live share`을 다운로드하여 사용하면 협업을 할 수 있다는 것을 발견하여 본 글을 작성하게 되었다.

<br/>

## 본론

### **사용 방법**

1. 설치

![image](https://user-images.githubusercontent.com/57826388/73716738-409f5000-475b-11ea-8230-87cf06be53cf.png)

참여하는 사용자들 모두 설치하여야 공유가 가능하다.

![image](https://user-images.githubusercontent.com/57826388/73716878-9bd14280-475b-11ea-9226-732e6df59ecc.png)

다음과 같이 설치가 된 것을 확인할 수 있다.

<br/>

2. 참여

- 로그인

우선 사용하기 위해서는 로그인을 해야한다.  
github / MS / guest 계정으로 로그인 할 수 있는데, guest의 경우에는 읽기 전용으로 프로젝트에 참가만할 수 있다.

![image](https://user-images.githubusercontent.com/57826388/73716940-c58a6980-475b-11ea-90b1-8734d660144b.png)

Live share을 클릭하면 다음과 같은 화면이 나오는데  
- Join collaboration session...: 다음 사람의 프로젝트 접근
- 공동 작업 세션 시작...: 내 프로젝트 공유
- Start read-only collaboration session...: 읽기전용으로 프로젝트 공유

사용자는 원하는 기능을 이용할 수 있다.

<br/>

#### 나의 프로젝트 공유

프로젝트를 열고 좌측 하단에 'Live Share'를 클릭한다.

![image](https://user-images.githubusercontent.com/57826388/73717361-edc69800-475c-11ea-9c3a-fbe6a992016d.png)

클릭하게 되면 로그인 알림을 받고 로그인을 하게 된다.

그 후, 자신이 공유하려는 폴더, 프로젝트, 솔루션을 연다.  
이제 다시 아래의 'Live Share'를 클릭하면 초대 링크가 자동으로 복사된다.  
그럼 이 코드를 작업을 공유할 상대방에게 전송하여 접속하게 한다.

![image](https://user-images.githubusercontent.com/57826388/73717542-7ba28300-475d-11ea-84dd-a701701a854f.png)

상대방은 이러한 알림을 받게 되고 공유를 승인하게되면 공동작업이 시작된다!

<br/>

#### 상대방의 프로젝트 조인

프로젝트를 공유할 때와 마찬가지로 Live Share 확장을 설치하고 아래 버튼을 눌러 로그인을 한다.

![image](https://user-images.githubusercontent.com/57826388/73717361-edc69800-475c-11ea-9c3a-fbe6a992016d.png)

이후 공유한 사용자의 초대 링크 알림을 받아 이것을 열게되면

![image](https://user-images.githubusercontent.com/57826388/73717717-e9e74580-475d-11ea-9cc6-d29566a5a2f3.png)

이러한 웹으로 이동하게 된다.  
별다른 오류가 없을 경우, 자동으로 VSCode 협업 창이 실행되며 공동작업을 시작할 수 있다.

<br/>

### **Live Share의 기능**

이 밖에도 다양한 기능이 존재한다. 다음은 VS Code Live Share 페이지에서 가져온 내용들이다.

![image](https://user-images.githubusercontent.com/57826388/73717966-93c6d200-475e-11ea-99cb-6d86de965ac3.png)

![image](https://user-images.githubusercontent.com/57826388/73718080-d8526d80-475e-11ea-8677-e497f08e0358.png)

![image](https://user-images.githubusercontent.com/57826388/73718145-09cb3900-475f-11ea-94df-87ea8b64f4ae.png)

<br/>

## 결론

이렇듯 VS code의 Live Share를 활용하면 단순 VS Code상에서의 협업 그 이상의 서비스를 이용할 수 있다. 굉장히 직관적이고 편리하다. 아직 나온지 1년도 안된 만큼 앞으로의 행보가 기대된다.

<br/>

## 향후과제

VS Code의 다양한 기능 활용해보기

<br/>

## 참고자료

<https://docs.microsoft.com/ko-kr/visualstudio/liveshare/>  
<https://pang2h.tistory.com/217>

<br/>

*Writer: Jae-Hwan Lee*          