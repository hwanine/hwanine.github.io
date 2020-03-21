---
toc: true
toc_sticky: true
categories:
  - Git
title: Git error:failed to push some refs to 'XXX.git'
tags: [VS Code, Remote Development]
excerpt:  "Git error:failed to push some refs to 'XXX.git' 에러 해결법"
---

* **연구주제** : Git error:failed to push some refs to 'XXX.git'
* **연구목적** : 깃허브 푸시 문제 해결
* **연구일시** : 2020년 1월 10일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Windows terminal, Github
* **관련연구** : AWS, Linux, Unix, Ubuntu, OS, Blog

&nbsp;

## 서론

git push 명령어를 사용하여 push를 하려고 할 때  
다음과 같은 에러가 날 수 있다.

&nbsp;

![image](https://user-images.githubusercontent.com/57826388/72117429-02e41d00-3391-11ea-9bcd-90972e1ea5b4.png)

&nbsp;

>### 원인: Conflict

사실 대부분의 경우 remote 버전과 로컬의 버전이 달라서 충돌되어 에러가 나는 것이다.

필자는 깃 홈페이지를 통해 파일들을 조작하곤 했는데 그런 상태에서 로컬 파일을 코드로 푸쉬할 경우 이런 에러가 나게 되었다.   
대부분의 초심자들이 GUI 환경을 더 선호하므로 이러한 에러발생률이 크다고 생각한다.

&nbsp;

## 본론
>### 해결: Merge -> push

 #### 1. pull

 remote 버전에서 pull을 받아올 경우, 자동적으로 로컬 버전과 Merge가 이루어진다.

 ![image](https://user-images.githubusercontent.com/57826388/72117774-48551a00-3392-11ea-80e9-354d8391251d.png)

&nbsp;

  하지만 일부 경우에선 충돌이 발생하므로 직접 수정해주어야 한다.

![image](https://user-images.githubusercontent.com/57826388/72117912-be598100-3392-11ea-9948-8de0f03e9984.png)

&nbsp;

이 경우에서는 CONFLICT로 표시된 부분이 충돌이 난 파일을 의미한다.  
Merge에 실패했으므로 직접 수정하는 과정을 거친다.  

충돌된 파일을 열면 다음과 같은 부분을 찾을 수 있다.

![image](https://user-images.githubusercontent.com/57826388/72118054-3627ab80-3393-11ea-9ef8-32edd26e1e1a.png)

&nbsp;

두개 파일 중, 어느 코드를 쓸지, 혹은 다 쓸지, 새로운 코드를 짤지는 본인이 자유롭게 선택하면 된다.

예를들어, 파일 두개를 둘 다 쓰겠다고 하면 다음과 같이 저장하면 된다.

![image](https://user-images.githubusercontent.com/57826388/72118127-856ddc00-3393-11ea-8f7e-87b27c1b8563.png)

&nbsp;

## 결론

이제 다시 push를 해보자.

![image](https://user-images.githubusercontent.com/57826388/72118263-e4cbec00-3393-11ea-9a4b-1d55788c8041.png)

정상적으로 변경사항이 깃에 push됨을 확인할 수 있다!

&nbsp;

## 향후과제

다른 여러가지 에러에 대해서도 직접 테스팅해보고 해결해보자!

&nbsp;

## 참고자료

<https://gamtoggi.tistory.com/83>

&nbsp;

*Writer: Jae-Hwan Lee*
