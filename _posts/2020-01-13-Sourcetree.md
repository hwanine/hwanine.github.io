---
layout: post
title: Sourcetree
feature-img: "assets/img/pexels/computer.jpeg"
thumbnail: "https://user-images.githubusercontent.com/57826388/72244818-680a6d80-3632-11ea-8049-1d71a4e031f5.png"
tags: [Git, Sourcetree, SSH]
author: Jae-Hwan Lee
excerpt_separator: <!--more-->
---
# 소스트리를 이용한 깃 사용
<!--more-->
* **연구주제** : 소스트리를 이용한 깃 사용
* **연구목적** : 소스트리를 이용한 깃 형상관리
* **연구일시** : 2020년 01월 13일 09:00~17:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR, Window Terminal
* **관련연구** : Git, Sourcetree, OS, Ubuntu

<br/>

## 서론

### **Sourcetree**

![image](https://user-images.githubusercontent.com/57826388/72244818-680a6d80-3632-11ea-8049-1d71a4e031f5.png)


개발자들에겐 개발한 소스코드에 대한 관리가 큰 부담이 된다. 그래서 대다수의 기업들이나 개인은 이것을 관리하조가 다양한 형상관리툴을 사용한다. 대표적으로 SVN, CVS가 있고 우리나라에서는 Git을 많이 쓰고 있다. 

하지만 터미널(git bash)에서 작업하는 것이 불편할 수도 있기 때문에 이를 도와주는 프로그램이 소스트리이다. 소스트리를 이용하면 GUI 환경에서 쉽게 git을 사용할 수 있다.
우선 간단한 설치방법은 다음과 같다.

<https://www.sourcetreeapp.com/>

해당 링크로 접속한 후 인스톨러를 다운로드하고 안내하는 과정을 따라 설치를 진행하면된다.

이렇듯 소스트리는 **GIT을 GUI로 사용자가 더 쉽게 사용할 수 있도록 하는 프로그램**이다.  
Windows와 Mac OS를 지원하고 라이센스가 무료다. 그럼 소스트리에 대해 자세히 알아보도록 하자.

<br/>

## 본론

### **소스트리 사용법**

### 1. 환경 구축

우선 소스트리를 사용하기위한 환경을 구축해야한다.   
Windows Terminal을 설치하고 ssh key를 생성한다. 
````
ssh-keygen -t rsa  
````
````
cat ~/.ssh/id.rsa.pub
````
Windows Terminal을 열고 다음과 같이 입력하게되면 터미널에 키 값이 나오게 되고 키가 생성된다.  
이 때, Clone할 git 저장소에 RSA Public key를 등록한다. 

![image](https://user-images.githubusercontent.com/57826388/72252396-88dabf00-3642-11ea-980b-ea8b0d48fa6e.png)

접속 경로는 Setting - SSH Keys이다.

<br/>

### 2. 깃 서버 접속

상단의 **도구 - 옵션**으로 들어가면 다음과 같은 화면이 나온다.  
여기서 방금전에 생성한 키를 불러오고 통신을 OpenSSH로 바꾼다.  

![image](https://user-images.githubusercontent.com/57826388/72251487-51b7de00-3641-11ea-90af-bac36884817e.png)

<br/>

그리고 **도구 - SSH 키 추가**를 선택하여 키를 불러온다. 

![image](https://user-images.githubusercontent.com/57826388/72252652-0a325180-3643-11ea-9779-2394331da9aa.png)

<br/>

이제 세팅은 완료된 것이다. 이제 소스트리를 실행시킨다.  
우선 로컬 디렉토리를 생성하여 깃 서버 디렉토리에서 파일을 복사해온다.

- 깃 디렉토리 주소
- 로컬 디렉토리 경로
- 디렉토리 이름

이렇게 기입해넣는다.

![image](https://user-images.githubusercontent.com/57826388/72245600-490cdb00-3634-11ea-93fd-f5a92a204ac7.png)


![image](https://user-images.githubusercontent.com/57826388/72250409-e4a34900-363e-11ea-948a-83fcaceb94a9.png)

<br/>

위의 그림과 같이 정상적으로 연결이 되어 Clone이 된 것을 확인할 수 있다.  
이제 본격적으로 소스트리를 이용하여 Git을 사용할 수 있게 된 것이다!

<br/>

### 3. 소스트리 활용

이제 우리는 소스트리를 활용할 수 있다. 우선 poll로 깃 저장소에 있는 파일을 불러와보자. 

![image](https://user-images.githubusercontent.com/57826388/72253891-f3d9c500-3645-11ea-9173-d1781a0af4b3.png)

<br/>

다음과 같이 pool을 누르고 옵션을 지정한 후 pull을 누르게되면 성공적으로 원격 디렉토리의 파일 구성과 로컬디렉토리의 파일구성간의 동기가 이루어진다. 만약 충돌이 일어날 경우, 그에 대한 해소또한 가능하다.
  
그리고 파일을 깃 서버로 push 시키는 과정을 살펴보자. 우선 다음 그림의 커밋을 누른다.

![image](https://user-images.githubusercontent.com/57826388/72254054-559a2f00-3646-11ea-80d9-9d6463801554.png)

<br/>

그 후 다음과 같은 화면이 나온다. 아래는 변경할 파일의 목록이고 이것을 스테이지로 올려야 커밋 목록에 포함된다. 따라서 스테이지 올리기를 누른 후, 하단에 커밋 메시지를 기입하고 우측 하단에 커밋 버튼을 누른다. 그렇게 되면

![image](https://user-images.githubusercontent.com/57826388/72254211-bb86b680-3646-11ea-90a0-868a99aa0168.png)

<br/>

위의 그림처럼 push 버튼에 표시가 나오게 된다. 그리고 클릭하면 push할 파일 정보가 나오게 된다.

이 외에도 브랜치를 생성하고 변경하거나 삭제한다던지, 머지를 한다던지 등 깃에서 할 수 있는 대부분의 기능을 소스트리를 이용하여 GUI환경으로 쉽게 다룰 수 있다. 

<br/>

## 결론

개발자의 위치에서 협업은 굉장히 중요하다. 결국 개발은 혼자서 하는 것이 아닌 다 함께 하는 것이고, 거기서 최상의 효율을 내려면 그런 협업을 하는 기술역량이 가장 중요한 부분이 아닐 수 없다고 생각한다.

평소 GUI환경이 아닌, 낯선 git bash환경이어서 다가가기 힘들었던 주니어 개발자나 학생들은 꼭 한번 소스트리를 써보기를 권장한다.

<br/>

## 향후과제

간단하게 소규모 프로젝트에 소스트리를 활용해보았는데 다음에는 소스트리를 이용하여 실제 대규모 협업 프로젝트를 경험해 볼 것이다.

<br/>

## 참고자료

<https://devjjo.tistory.com/24>  
<https://donbada.tistory.com/73>  
<https://voidfunction-e.tistory.com/entry/%EC%86%8C%EC%8A%A4%ED%8A%B8%EB%A6%AC-%EA%B9%83%ED%97%88%EB%B8%8C-%EC%82%AC%EC%9A%A9%EB%B0%A9%EB%B2%95-%EC%97%B0%EA%B2%B0ssh%ED%82%A4-%EC%83%9D%EC%84%B1%EC%9D%B8%EC%A6%9D>

<br/>

*Writer: Jae-Hwan Lee*


