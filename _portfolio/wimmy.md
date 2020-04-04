---
title: "Ginger Gulp Identity"
excerpt: "Ginger Gulp design system including logo mark, website design, and branding applications."
header:
  image: /assets/images/unsplash-gallery-image-3.jpg
  teaser: assets/images/unsplash-gallery-image-3-th.jpg
sidebar:
  - title: "Role"
    image: http://placehold.it/350x250
    image_alt: "logo"
    text: "Designer, Front-End Developer"
  - title: "Responsibilities"
    text: "Reuters try PR stupid commenters should isn't a business model"
gallery:
  - url: /assets/images/unsplash-gallery-image-1.jpg
    image_path: assets/images/unsplash-gallery-image-1-th.jpg
    alt: "placeholder image 1"
  - url: /assets/images/unsplash-gallery-image-2.jpg
    image_path: assets/images/unsplash-gallery-image-2-th.jpg
    alt: "placeholder image 2"
  - url: /assets/images/unsplash-gallery-image-3.jpg
    image_path: assets/images/unsplash-gallery-image-3-th.jpg
    alt: "placeholder image 3"
---

# 프로젝트 개요

![1](https://user-images.githubusercontent.com/57826388/77233868-c4b77500-6bed-11ea-8926-f8753cbbca0d.png)


기존의 사진 관리 프로그램은 보통 폴더별로만 관리할 수 있다. 본 프로그램은 폴더, 특징, 날짜, 위치 별 관리가 가능하다​.  
본 프로그램의 핵심인 태그는 이미지를 분석하여 자동으로 추출하거나, 사용자가 직접 추가, 수정, 삭제가 가능하다.​  
이를 활용하여 캘린더에서 월별로 특징들을 확인하거나, 지도를 직접 돌아보며 특징을 확인할 수 있도록 분류된 사진에 중첩하여 태그에 대한 연관성을 극대화 하였다.  
이를 통해 사용자의 사진들에 대한 접근성을 극대화 하는 것이 우리의 최종 목표이다.

- 서비스: 이미지 태그 및 특징/위치/날짜 분류를 통한 안드로이드 사진 관리 애플리케이션
- 개발 환경: Android Studio 3.5/3.6
- 개발 언어: Kotlin, XML
- 기타 환경: Git 
- 외부 자원: Firebase ML Kit - Translate API, Image labeling API, Google Map API
- 제약 사항: 안드로이드 OS 4.0 이상, 기기 접근 권한

<br>

# Diagram

![Untitled Diagram (1)](https://user-images.githubusercontent.com/57826388/77235305-f5041100-6bf7-11ea-998d-5426f7a27c8c.png)

<br>

# 프로젝트 모델링

 안드로이드 내부 DB를 통해 로컬 저장소에 있는 이미지들과 핵심 메타데이터들을 관리한다.  
 추가적으로 관리할 DB는 다음과 같다.

 안드로이드 RoomDB, LiveData, ViewModel 을 이용하여 `MVVM모델`을 구축.  
 `Observer` 패턴을 통해 이미지의 변경이 있을 시 자동으로 업데이트를 하도록 한다.

- ExtraPhotoData
    - 사진 id
    - 위치
    - 즐겨찾기 여부

- TagData
    - 사진 id
    - 태그

- thumnailData
    - 사진 id
    - 데이터

- LatLngData
    - 위경도 번호
    - 사진 아이디
    - 위경도

<br>

# 주요 일정

## 완료

- ~ 2020\. 02. 07: 시나리오, 기능 도출, 프로토타입 UI 개발
- 2020\. 02. 08 ~ 2020\. 02. 14: XML UI 구조 개발, DB 모델링  
- 2020\. 02. 14 ~ 03. 21: 주요 개발 (Android Studio)
    - 사진을 폴더, 태그, 위치, 날짜별로 보여주기​
    - 안드로이드 내부 DB에서 사진 목록 가져오기​
    - 날짜별로 보여주기 위한 캘린더 만들기​
    - Room을 사용해 추가 정보 저장(좌표 -> 위치 정보, 즐겨찾기, 태그 정보)​
    - Firebase ML Kit - 이미지 분석 API를 활용하여 이미지 특징정보 추출​
    - 사진 출력을 위한 UI 생성
    - 구글 맵 API와 클러스터링 라이브러리를 활용하여 위치 좌표 생성 및 출력​
    - 프로그램 시작 시 추가 삭제된 사진 처리​
    - ThreadPoolExcutor를 사용한 추가 쓰레드 관리
    - 사용자가 직접 태그를 입력하거나 수정, 삭제 가능​
    - 사진 공유기능​
    - 백그라운드에서 특징정보 자동 분석 및 삽입​

## 예정

- 추가 기능 도출
- 테스팅
- 버그 수정

## 문제점

- 좌표를 지역으로 변환하는 GeoCoder 사용시 일부 사진에서 에러 발생
- ​추가된 사진에 대해서는 사진 추가 일시를 확인해서 그 이후 사진으로 구분하지만, 삭제된 사진의 경우 모든 사진을 확인해야하는 문제
- 애니메이션 이미지 지원
- 실시간으로 추가 삭제되는 사진을 반영하지 못함
- 날짜별로 사진 검색

​<br>

# Application 

## Folder List

- 유형 별로 분류한 폴더를 출력한다. 사용자는 클릭이벤트를 통해 해당 폴더 내부로 들어갈 수 있다.

### **로컬 폴더 별 분류**

- 로컬 저장소에 있는 사진들을 폴더 별로 분류하여 이미지를 출력한다.  
- 가장 기본적인 폴더 별 분류를 이용하여 사용자는 본인의 사진에 대해 조작을 함에 있어서 손쉽게 다가갈 수 있다.

![KakaoTalk_20200321_221134563](https://user-images.githubusercontent.com/57826388/77233890-f29cb980-6bed-11ea-84c7-4034a457b52f.jpg)

<br>

### **태그 별 분류**

 - 이미지를 태그별로 분류한다.
 - 앱이 실행된 후 백그라운드로 실행되면서 딥러닝을 통한 이미지에 대한 분석을 하고 특징을 자동으로 도출하여 DB에 입력된다.
 - 특징은 Firebase ML kit image labeling API를 통해 추출해낸다.
 - 추가적으로 추출된 특징이 영어이기 때문에 Firebase ML kit translate API를 통해 서비스 지역 언어로 번역하여 삽입된다.

![KakaoTalk_20200321_222826856](https://user-images.githubusercontent.com/57826388/77233905-f92b3100-6bed-11ea-9481-01735dfa924b.jpg)

<br>

### **날짜 별 분류**

- 달력을 년/월 단위로 볼수있게 출력한다.
- 달력의 각각의 일자를 클릭하면 해당 날짜의 이미지를 볼 수 있다.
- 달력에는 해당 날짜에서 가장 많은 비중을 차지하는 태그들을 표기한다. 이를 통해 사용자는 해당 날짜에 어떠한 사진을 찍었는지, 사진은 어떤 특징을 지녔는지 직접 확인하지 않고도 한 눈에 알 수 있다.

![KakaoTalk_20200321_222815162](https://user-images.githubusercontent.com/57826388/77233894-f4ff1380-6bed-11ea-8940-6a1af19a7592.jpg)

<br>

### **위치 별 분류**

- 이미지를 위치별로 분류한다.
- 위치는 앱이 실행된 후 백그라운드로 실행되면서 자동으로 로컬 저장소의 사진들의 위치를 추출한다. 그 후, 변환작업을 거쳐 추가 데이터베이스에 삽입되며 사용자의 화면에 출력된다.
- 위치는 시/군/구로 구분되며 시/군/구로 분류가 불가능할 경우, 국가로 구분한다.

![KakaoTalk_20200321_222817182](https://user-images.githubusercontent.com/57826388/77233895-f4ff1380-6bed-11ea-9720-27394f09f461.jpg)

<br>

## Photo List

- 분류를 통한 UI에서 사용자 클릭 이벤트를 통해 이미지 리스트로 이동한다.
- 본격적으로 분류된 이미지를 출력한다.

<br>

### **이미지 그리드 리스트**

- 분류된 이미지들을 출력한다.
- 출력 과정은 비동기 프로그래밍으로 진행되기 때문에 성능에는 문제가 없다.

![KakaoTalk_20200321_222823868](https://user-images.githubusercontent.com/57826388/77233903-f7fa0400-6bed-11ea-9c2a-1f7106e04359.jpg)

<br>

### **이미지 맵 리스트**

- 구글 맵을 이용하여 이미지의 대한 위치를 마커로 표시한다.
- 마커를 누르게되면 이미지의 태그정보를 말풍선으로 출력하고 하단에 카드뷰 형식으로 이미지에 대한 정보를 출력한다.
- 클러스터링 라이브러리를 통해 마커가 근접하게 여러 개 존재할 경우, 이를 모아준다.
- 하단의 이미지를 클릭하게되면 이미지 출력 뷰로 이동하게 된다.

![KakaoTalk_20200321_222735467](https://user-images.githubusercontent.com/57826388/77233891-f3cde680-6bed-11ea-9ec5-9243a64bc946.jpg)

<br>

## 이미지 출력 뷰

- 이미지 리스트에서 사용자가 특정 이미지를 클릭을 할 경우, 해당 이미지에 해당하는 이미지를 출력한다.
- 이미지를 확대하거나 축소할 수도 있다.
- 좌/우로 스와이프시 이전 또는 이후의 이미지를 출력하는 뷰 페이저 방식으로 구현되었다.
- 이미지를 터치하게되면 상단 이미지 정보와 하단 옵션이 감춰지게되고, 다시 터치하면 나타나게 된다.
- 상단의 별 이미지 클릭을 통해 즐겨찾기 이미지를 지정할 수 있다.

![KakaoTalk_20200321_222822495](https://user-images.githubusercontent.com/57826388/77233901-f7616d80-6bed-11ea-9081-d147e6eb4c38.jpg)

![KakaoTalk_20200321_233007949](https://user-images.githubusercontent.com/57826388/77233907-f9c3c780-6bed-11ea-8843-c9c243ad03d6.jpg)

<br>

## 기타 기능

### **이미지 맵 리스트**

- 메인 화면에서 우측 상단 옵션을 통해 즐겨찾기 뷰로 이동할 수 있다.
- 즐겨찾기가 되어있는 이미지를 출력해준다.

![KakaoTalk_20200321_222818098](https://user-images.githubusercontent.com/57826388/77233896-f597aa00-6bed-11ea-87ac-d540761454a6.jpg)

<br>

### **이미지 삭제**

- 뷰페이저의 하단 옵션에서 삭제를 클릭하면 이미지를 삭제할 수 있다.
- 삭제는 로컬 저장소의 해당 이미지, 해당 이미지의 ID가 포함된 DB의 모든 데이터가 지워지게 된다.

![KakaoTalk_20200321_222818982](https://user-images.githubusercontent.com/57826388/77233897-f6304080-6bed-11ea-945f-8642708782b0.jpg)

<br>

### **태그 입력, 수정, 삭제**

- 사용자는 태그를 최대 5개까지 관리 가능하다.
- 태그 입력 버튼을 누르면 자동으로 이미지의 태그들로 다이얼로그를 채운다.
- 거기서 태그를 추가하거나 지우는 행위를 통해 태그를 직접 조작할 수 있다.

![KakaoTalk_20200321_222820128](https://user-images.githubusercontent.com/57826388/77233899-f6c8d700-6bed-11ea-88e3-b7b5c711e879.jpg)

<br>

### **SNS 공유**

- 다음 공유 버튼을 통하여 SNS로 해당 이미지를 공유할 수 있다.

![KakaoTalk_20200321_222821436](https://user-images.githubusercontent.com/57826388/77233900-f6c8d700-6bed-11ea-8b19-9828f2384f7e.jpg)

<br>

### **이미지 검색**

- 사용자는 원하는 이미지를 직접 검색할 수 있다.
- 유형은 이름, 위치, 태그, 날짜로 이루어지며 검색을 하면 키워드가 포함된 모든 이미지를 고유한 이름으로 출력한다.
- 검색화면은 메인화면 상단의 돋보기 모양 버튼을 통해 이동할 수 있다.

![KakaoTalk_20200321_222825229](https://user-images.githubusercontent.com/57826388/77233904-f8929a80-6bed-11ea-9eca-d947871daefd.jpg)

<br>

### **카메라**

- 사용자는 메인화면 상단 툴바에 카메라 버튼을 통해 카메라 캡처를 할 수 있다.
- 캡처한 이미지는 `Pictures/Wimmy` 폴더에 저장된다.

![image](https://user-images.githubusercontent.com/57826388/77234692-cedc7200-6bf3-11ea-8532-b9f7eb745299.png)

<br>

### **로딩 화면**

- 본 서비스를 이용하기 위해서는 일부 권한에 허가를 해야하고 특정 API 모델을 사용자의 기기에 설치해야 한다. 이 과정을 로딩 화면에서 제어한다.

![KakaoTalk_20200321_222912042](https://user-images.githubusercontent.com/57826388/77233906-f92b3100-6bed-11ea-8995-130f9561a484.jpg)

<br>

## Others

### Custom Date picker
### 최상단, 최하단 버튼
### 상단 툴바 숨기기
### 프래그먼트 생애주기 백 스택 관리
### GestureDetector
### 위치별 보기 옵션

