---
layout: post
title: Container VS VM
feature-img: "assets/img/pexels/computer.jpeg"
thumbnail: "https://lh3.googleusercontent.com/53UBfkTd__oJLlxXMbYVhHBBXtnRhkQ7fhUplq1eF7LHp-rTPbVADRbQQPugcZtK-TGoymFE1OQwC0lkvCUbWNi8j2MvUUdTIK5rPsfa4AtmA2k2M9s36ZliRCeTkmSbZ7DdHphU"
tags: [Container, Virtual machine, Docker]
author: Jae-Hwan Lee
excerpt_separator: <!--more-->
---
# Container VS Virtual Machine
<!--more-->
* **연구주제** : Container VS Virtual Machine
* **연구목적** : 도커 사용전, 컨테이너와 가상머신의 차이를 알기위함
* **연구일시** : 2019년 12월 31일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, WSL, Lightsail, Docker
* **관련연구** : AWS, Linux, Unix, Ubuntu, OS, SSH

&nbsp;

## 서론

![123](https://lh3.googleusercontent.com/53UBfkTd__oJLlxXMbYVhHBBXtnRhkQ7fhUplq1eF7LHp-rTPbVADRbQQPugcZtK-TGoymFE1OQwC0lkvCUbWNi8j2MvUUdTIK5rPsfa4AtmA2k2M9s36ZliRCeTkmSbZ7DdHphU)

지난 포스트에서 도커에 대해 다루었다. 그럼 컨테이너가 무엇이고 가상머신은 무엇인가?
그럼 그 둘의 차이는?

&nbsp;

## 본론

>**가상머신**  
```
가상머신(VM)은 컴퓨터 환경을 말 그대로 가상화하여 소프트웨어로 구현한 것이다.  
위의 그림에서 볼 수 있듯이 VM은 서버, Host OS, Hypervisor 위에 올라가게 되고,  
각 VM은 OS, 드라이버, 메모리 등 컴퓨터 환경이 구성되기 위해 필요한 요소들을 갖추게 된다.
```
```
VM을 사용하면 서버 한 대 안에서도 여러 개의 OS 환경을 구성할 수 있다는 장점이 있고,  
그만큼 서버의 공간을 효율적으로 사용할 수 있게 된다.
```

&nbsp;

>**컨테이너**
```
VM에 비교했을 때 컨테이너는 한 층 더 애플리케이션 중심으로 설계되었다.  
그림에서 볼 수 있 듯이 컨테이너는 별도의 OS나 드라이버 없이 Host OS를 공유하는 형태로 실행된다.  
VM이 서버를 여러 대로 사용할 수 있게 해주었다면, 컨테이너는 개별 애플리케이션을 위한 가상 공간을 할당해 준다.
```
```  
컨테이너는 VM보다 작은 단위이고 내부에서 처리하는 일도 더 적기 때문에 보다 간단하고,   
빠르고, 효율적으로 애플리케이션을 실행시킬 수 있다. 하지만 컨테이너를 사용하기 위해서는 하나의 OS만   
사용해야 하기 때문에 여러 가지의 OS를 사용할 수 있게 해주는 VM보다는 용도가 제한될 수 있다.
```

&nbsp;
  
### 컨테이너의 장점
 1. 성능상의 이점
-- OS를 새로 설치할 필요가 없으므로 성능 저하가 거의 없다.

 2. 빠른 운영환경 구성
-- 미리 사용자들이 만들어 둔 이미지를 다운받아 실행만 하면 해당 환경을 구축할 수 있다.

 3. 관리의 용이
 -- 이미지 만을 관리 배포하면 되므로 운영환경의 관리가 용이하다.
 
 4. 확장성
-- 한번 만들어낸 이미지를 이용하여 여러 서버들에서 사용가능하다. 

 &nbsp;

## 결론 

정리하자면 다음과 같다.

|가상머신  | 컨테이너 |
|:----------|:--------|
| 하드웨어 수준의 가상화 | 운영체제 가상화 |
| 리소스 사용량이 높음 | 리소스 사용량이 적음 |
| 다양한 어플리케이션 실행가능 | 동일 커널 기반의 서비스만 구성 가능 |
| 제한된 성능 | 성능 제한 x |
| 가상머신간 완전한 격리로 높은 보안성 | 프로세스 수준의 격리로 비교적 낮은 보안성 |

&nbsp;

## 향후과제

도커를 다방면으로 활용하면서 컨테이너의 장점을 실감해보고 이미지를 배포한다.

&nbsp;

## 참고자료

<https://food4ithought.com/2019/10/26/%EA%B0%80%EC%83%81%EB%A8%B8%EC%8B%A0virtual-machine-vs-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88container/>  
<https://post.naver.com/viewer/postView.nhn?volumeNo=21385900&memberNo=2521903>  
<https://dololak.tistory.com/350>

&nbsp;

*Writer: Jae-Hwan Lee*
