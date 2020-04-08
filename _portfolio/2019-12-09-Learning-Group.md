---
title: "Learning Group In Changwon National University"
excerpt: "소프트웨어 공학론을 따른 교내 학술 모임 애플리케이션 개발"
header:
  teaser: assets/images/lg.PNG
toc: true
toc_sticky: true
---

소프트웨어 공학론의 Plan-driven, 폭포수 모델을 따라서 개발을 진행하였다.  
각 요구사항을 도출한 명세서와 소프트웨어 설계서를 작성하고, 이후 개발을 진행하였다.  
마지막으로 테스팅을 통해 테스팅 문서를 작성하였다.

# Software Specification Doc


명세서에서 명세한 각각의 기능을 대략적으로 설명하고,
본 소프트웨어의 기능 흐름과 사용 대상들을 위한 성능 요구사항을 보여준다.

<br>

## 개요 및 목적

Learning Group In Changwon N.Univ.는 창원대학교 학생들을 대상으로 편리하게 소모임 생성 및 참여를 할 수 있도록 하는 기능을 제공하는 어플리케이션이다.  
L&D Soft팀에서는 기존 소프트웨어의 다음과 같은 단점들을 보완하였다.

><기존의 소모임 소프트웨어의 단점> ex) 와글, 에브리타임, etc  

- 비교적 까다로운 사용자 인증 방식  
- 사용자 범위 미지정에 따른 범죄 노출 위험  
- 사용자들 사이의 물리적 거리로 인하여 실제 모임을 형성하기 어려움   
- 카테고리별로 분류 되지 않음  
- 게시글 자체에서 참여의사를 표시하기 어려움

기존 소프트웨어에는 위의 단점들 외에도 많은 단점이 존재한다.  
L&D Soft팀에서는 이와 같은 기존 소프트웨어의 단점을 해소하였고, 장점을 부각시켜 학업 관련 모임에 더욱 비중을 두어 학습과 관련된 카테고리들을 제공하고, 창원대학교 학생들의 커뮤니티 활성화를 통해 학생들 간의 활발한 교류의 발생과 모든 학생들이 학업 역량증진을 성취하는 것을 목표로 한다. 

<br>

## 시스템 컨텍스트

![image](https://user-images.githubusercontent.com/57826388/78468768-d05e7c00-7755-11ea-817b-4f4885f55c10.png)

<br>

## 전체 기능 유스케이스

![image](https://user-images.githubusercontent.com/57826388/78468777-e3714c00-7755-11ea-8696-d207cc9bc631.png)

- 회원 정보 관리에서, 비밀번호 변경 및 이메일 찾기, 로그인 등은 회원가입이 선행되어야 하는 기능

- 일단 카테고리를 분류한 후에야 참여나 모임 개설 버튼 등을 통한 실제 모임 개설이 가능

- 참여 취소나 댓글 기능은 우선 참여하기로 한 모임에서만 가능
 
- 모임시스템에서 참여를 이용할 경우 모임 시간 알림, 알림에서 참석 확인을 통한 실명 확인이 가능

- 내 모임 확인하기 기능을 사용 가능. 이 때 참석 모임이 있을 경우에만 보임

<br>

## 전체 기능 유스케이스

![image](https://user-images.githubusercontent.com/57826388/78468796-03087480-7756-11ea-9e50-e0c5000d3341.png)

- 처음 소프트웨어 사용 시 회원가입이 선행되어야 함, 글을 작성할 경우 로그인이 되어 있어야 함

- 로그인이 선행되어 있을 경우, ‘모임개설’ ‘내 모임 확인하기’ ‘참여’’ ‘ 댓글’ 등의 기능을 사용할 수 있음

- 모임에 참여할 경우 참여 취소 기능이 활성화

- 모임시간이 되면 알림을 받고, 모임 때 모임 내 참여자의 정보 확인 가능

<br>

## 품질 요구사항

![image](https://user-images.githubusercontent.com/57826388/78468812-259a8d80-7756-11ea-8daa-452b93bea377.png)

<br>

# Software Design Doc

설계서에서 작성한 소프트웨어의 클라이언트-서버 아키텍처 환경을 설명하고,
클래스 다이어그램을 통해 본 소프트웨어의 정적 구조를 보여준다.

<br>

## SW/HW 시스템 환경 및 제약사항

> 시스템 환경

- 개발 환경: Windows 10, Android Studio

    - 윈도우 OS상에서 안드로이드 스튜디오를 사용

- 개발 언어: Java, Xml, Php

    - 기본적으로 안드로이드 스튜디오에서 사용하는 자바 및 XML을 사용하고, 데이터베이스에 접근하기 위해 PHP를 사용한다

- 서버: AWS EC2, Windows Server, Apache

    - AWS EC2 윈도우 서버를 설치, Apache를 구동해 기본 웹서버로 설정해 접근

- 데이터베이스: AWS RDS, Mysql, Mysql Workbench

    - AWS RDS Mysql 을 설치해 데이터베이스 구성. GUI기반 관리를 위해 워크벤치 이용

- 하드웨어 환경: Android 5.0 이상

    - 안드로이드 버전 5.0 이상 부터 사용이 가능하다 (API 수준 문제, 차후 업그레이드 예정)

<br>

## 클라이언트-서버 아키텍처

![image](https://user-images.githubusercontent.com/57826388/78468957-6f37a800-7757-11ea-943f-f599d07304d7.png)

- 클라이언트는 인터넷을 통해 서로 통신하게 됨

- 이때, 클라이언트는 자신의 모임 정보나, 참여 정보, 회원 정보 등을 데이터베이스에 저장할 필요가 있다

- 정보를 저장하는 데이터베이스는 AWS에서 할당받은 RDS mysql 을 사용하고, AWS에서 할당받은 EC2 윈도우 서버를 통해 데이터베이스에 접근한다.

- 또, 사용자 인증을 위해 네이버 SMTP 서버를 사용해 인증 메일을 발송한다.

<br>

## 데이터베이스 모델

![image](https://user-images.githubusercontent.com/57826388/78469019-e66d3c00-7757-11ea-8ce6-e52757199244.png)

- 5개의 엔티티가 각각의 정보를 저장

- User_managing(회원 정보)

- Group_managing(모임 정보)

- Log_managing(모임 로그 정보)

- Comment_managing(모임 댓글)

<br>

## 클래스 다이어그램

![image](https://user-images.githubusercontent.com/57826388/78469025-fe44c000-7757-11ea-84e8-5c507ceed527.png)

- 대부분이 필드만 존재하는 클래스 (DB에서 묶어서 가져 와야 하는 값, 또는 DB에 묶어서 저장해야 하는 값)

<br>

![image](https://user-images.githubusercontent.com/57826388/78469038-1b798e80-7758-11ea-8ff2-cf1d83dd6bf8.png)

- 데이터를 가져오는 요청을 처리하는 RequestHandler, 사용자 정보를 인식하게 하는 PrefManager

<br>

![image](https://user-images.githubusercontent.com/57826388/78469054-44018880-7758-11ea-9c30-95b1373b49d0.png)

- 리스트 뷰를 구현하는 베이스 어댑터를 상속한 클래스들

<br>

![image](https://user-images.githubusercontent.com/57826388/78469059-511e7780-7758-11ea-9548-a431f26fd45e.png)

- 서버에 접속해야 하는 기능들을 스레드 대신 Asynctask 를 상속받아 사용

<br>

# Software Testing Doc

## 테스팅 구성

1. 단위 테스팅(기능 테스팅)

2. 성능 테스팅(비기능 테스팅)

3. 시스템 테스팅

<br>

## 시스템 테스팅 시나리오

![image](https://user-images.githubusercontent.com/57826388/78469095-ce49ec80-7758-11ea-9706-7dcb08bca214.png)

- 해당 시나리오는 회원가입 및 이메일 인증(중복 검사 포함) 기능, 관련 성능에 대한 시나리오이고, 모든 기능/ 성능에 대한 시나리오가 이러한 형태로 작성되어 테스팅

<br>

## 시스템 테스팅 결과

기능 | 성능   (또는   부속 기능) | 결과
-- | -- | --
회원가입 | 로그인   대기 시간 | SUCCESS
회원가입 | 비밀번호   안전성 검사 | SUCCESS
회원가입 | 비밀번호   암호화 방식 | SUCCESS
회원가입 | 이메일   인증 | SUCCESS
회원가입 | (부속   기능)   회원가입   시 최초 자동 로그인 | SUCCESS
이메일   중복 검사 |   | SUCCESS

- 해당 기능, 기능에 관련된 성능과 부속 기능에 대한 테스팅 결과

- SUCCESS 또는 FALSE로 기입

<br>

# System UI

## 로딩

![image](https://user-images.githubusercontent.com/57826388/78469124-297bdf00-7759-11ea-92da-e009a1afec3b.png)

<br>

## 로그인

![image](https://user-images.githubusercontent.com/57826388/78469125-2b45a280-7759-11ea-8501-87bb02b380fa.png)

<br>

## 회원가입

![image](https://user-images.githubusercontent.com/57826388/78469136-4a443480-7759-11ea-998d-47de62e90498.png)

<br>

## 이메일 인증

![image](https://user-images.githubusercontent.com/57826388/78469138-4c0df800-7759-11ea-98e0-72ec99dd3267.png)

<br>

## 모임 개설

![image](https://user-images.githubusercontent.com/57826388/78469154-66e06c80-7759-11ea-8fa8-b79039e4b22c.png)

<br>

## 모임 화면(+ 댓글)

![image](https://user-images.githubusercontent.com/57826388/78469155-68aa3000-7759-11ea-967e-d6beaf35aefa.png)

<br>

## 메인 화면

![image](https://user-images.githubusercontent.com/57826388/78469160-76f84c00-7759-11ea-8252-5df729841f4c.png)

<br>

## 모임 로그 확인

![image](https://user-images.githubusercontent.com/57826388/78469161-78c20f80-7759-11ea-85c8-f6299931277d.png)

<br>

## 모임 시간 알림

![image](https://user-images.githubusercontent.com/57826388/78469177-b1fa7f80-7759-11ea-950c-90a15308e200.png)

![image](https://user-images.githubusercontent.com/57826388/78469178-b3c44300-7759-11ea-88c6-6847e2fcc8c2.png)

<br>

## 모임 시간 실명 확인

![image](https://user-images.githubusercontent.com/57826388/78469183-bd4dab00-7759-11ea-9edc-4b318722b214.png)

<br>

## 신고하기

![image](https://user-images.githubusercontent.com/57826388/78469185-bf176e80-7759-11ea-8a9b-d3bd58fe8a87.png)

<br>

## 이메일 찾기

![image](https://user-images.githubusercontent.com/57826388/78469194-d9e9e300-7759-11ea-83c9-a7fbe3a6c940.png)

<br>

## 모임 로그 확인

<br>

## 모임 로그 확인


