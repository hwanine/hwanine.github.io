---
title: "OpenCV를 활용한콘크리트-에폭시 파편 영상 분석"
excerpt: "콘크리트 절단면의 에폭시 검출을 위한 OpenCV를 활용한 영상처리 프로그램"
header:
  teaser: assets/images/epoxy.jpg
toc: true
toc_sticky: true
---

# 프로젝트 개요

![epoxy](https://user-images.githubusercontent.com/57826388/78421998-29b3a600-7697-11ea-9e9e-dac2793b344b.jpg)

<br>

콘크리트-에폭시 파편 영상 분석은 콘크리트를 둘로 쪼갰을 때, 양쪽 콘크리트 단면에서의 에폭시라는 불순물을 검출하는 것을 목적으로 한다.  
여기서 콘크리트와 에폭시의 관계를 보자면 다음 그림과 같다.

![image](https://user-images.githubusercontent.com/57826388/78422012-49e36500-7697-11ea-8467-950a550cb398.png)

![image](https://user-images.githubusercontent.com/57826388/78422020-536ccd00-7697-11ea-9cd4-e76e476372cc.png)

<br>

 콘크리트 표면 내부에는 위의 그림에서의 설명과 같이 다양한 층으로 구성되어 있다.  
 여기서 구체적으로 콘크리트 표면을 단면으로 자른 사진의 예제를 보이면 다음과 같다.

![image](https://user-images.githubusercontent.com/57826388/78422021-5667bd80-7697-11ea-84d8-e0cc28528f18.png)

위의 사진을 보면 콘크리트 단면에 파란색 물질들이 분포되어있는 것을 확인할 수 있는데 이것이 에폭시고 우리가 검출해야하는 물질이다.

 그렇다면 이러한 에폭시를 검출해야하는 이유는 무엇인가? 현재 대부분 지질관련학과의 교수들과 학생들은 연구를 함에 있어서 이러한 콘크리트의 에폭시를 검출해내어 분포도를 도출해야 하는데 대부분의 경우 일일이 수동으로 검출한다는 점을 알게 되었다.  

 더군다나 이렇게 한 쌍의 콘크리트를 분석하는데에 걸리는 소요시간이 약 3시간이 걸린다는 점을 발견하였고, 따라서 그들에게 유용한 소프트웨어를 제공하고자 콘크리트-에폭시 파편 영상 분석 프로젝트를 수립하였다.

<br>

## 기대효과

본 실습을 통하여 얻을 수 있는 기대효과는 다음과 같다.

Programmer
● OpenCV 함수 이해도 증가
● C++ 언어 이해도 증가
● 영상처리 이론 학습
● 문제 해결 능력 증진
● 프로그래밍 실력 증진
● 새로운 분야로의 접근 가능성

Customer
● 지질관련학과 연구업무효율 상승
● 영상 검출에 대한 정확성 상승(수동 -> 자동)

<br>

- 서비스: 콘크리트-에폭시 파편 영상 분석
 - 실습환경: Visual Studio 2019
 - 개발언어: C++
 - 라이브러리: OpenCV
 - 참고 자료: OpenCV로 배우는 영상 처리 및 응용 (생능출판, 정성환, 배종욱)

<br>

# 개발 단계

## 개발 단계

개발은 다음과 같이 세 가지의 큰 단계로 구분된다.

● 영상 잘라내기(관심영역)

![image](https://user-images.githubusercontent.com/57826388/78422112-df7ef480-7697-11ea-8ddd-85deb19baf24.png)

<br>

● 에폭시 검출

![image](https://user-images.githubusercontent.com/57826388/78422115-e1e14e80-7697-11ea-9c2b-ab608b3cec36.png)

![image](https://user-images.githubusercontent.com/57826388/78422117-e4dc3f00-7697-11ea-96f6-9cf950fad9c5.png)

<br>

● 검출 결과 분포도 출력

![image](https://user-images.githubusercontent.com/57826388/78422118-e73e9900-7697-11ea-9938-803a74d5b4ef.png)
![image](https://user-images.githubusercontent.com/57826388/78422119-e9a0f300-7697-11ea-966c-61b808d55961.png)

<br>

## 세부 단계

이제 큰 단계들을 세부 단계로 나누면 다음과 같다.  

● 영상 잘라내기(관심영역)  
(1) 영상을 그레이채널로 변환  
(2) 캐니 엣지 적용  
(3) 모폴로지를 통하여 잡티 제거  
(4) 영상에서 사각형 객체 검출  
(5) 검출된 사각형 객체의 외곽선을 초록색으로 칠함  
(6) 화소를 열/행 단위로 읽음   
(7) 초록색 화솟값이 영상 크기대비 일정 화소비율 검출되면 좌표 변수 등록  
(8) 좌표 변수로 영상 자르기  
(9) 영상을 다시 둘로 나눠야하므로 다시 (1)~(8) 연산 진행 (사각형 내부이므로 (4) 과정은 직	선 검출로 대체)  
 
● 에폭시 검출  
(1) BGR 컬러채널로변환  
(2) 검출 객체에 대한 최소화소값 & 최대화소값 색상마스크 적용  
(3) 검출 객체에 대한 최소채도값 & 최대채도값 채도마스크 적용  
(4) 색상마스크와 채도마스크 병합  
(5) RGB 모델로 복사  

● 검출 결과 분포도 출력  
(1) x, y에 대해 화소 검출 간격(그리드) 지정  
(2) 검출간격(관심영역)별로 화소값을 읽어옴  
(3) 검출간격 대비 화소값의 비율에 따라 결과 영상에 그리드를 입힘  
(4) 모든 화소를 거쳤으면 결과 영상 출력   

![image](https://user-images.githubusercontent.com/57826388/78422170-5b793c80-7698-11ea-8b86-15a73e419c2d.png)

<br>

# 개발 과정

## 영상 잘라내기

### 1. 단계 (1) ~ (5)

![image](https://user-images.githubusercontent.com/57826388/78422191-8b284480-7698-11ea-80a3-062d963c2fe9.png)

![image](https://user-images.githubusercontent.com/57826388/78422192-8e233500-7698-11ea-8215-08ba36db1599.png)

● 영상을 불러와서 내부 사각형만 검출하기위하여 캐니 엣지를 적용하고 모폴로지를 적용한다.  
● 사각형 검출 루프를 통해 사각형의 외곽선을 초록색으로 출력한다.

<br>

### 2. 단계 (6) ~ (8)

![image](https://user-images.githubusercontent.com/57826388/78422204-a6934f80-7698-11ea-8670-caeb0c5a6aab.png)

![image](https://user-images.githubusercontent.com/57826388/78422207-a7c47c80-7698-11ea-8bf2-ce894314c402.png)

● 검출된 초록색 외곽선을 가진 사각형을 잘라내기 위하여 화소값을 열/행 별로 읽으면서 일정 이상 초록색이 검출이 되면 좌표값을 변수에 저장한다.  
● 위의 코드는 상단 잘라내기이며 이와 같은 원리로 동작하는 루프가 3개 더 존재한다.

<br>

### 3. 단계 (9)

![image](https://user-images.githubusercontent.com/57826388/78422221-bad74c80-7698-11ea-867b-8a3c6982e58b.png)

![image](https://user-images.githubusercontent.com/57826388/78422223-bdd23d00-7698-11ea-9a2e-4739384dc602.png)

● 아직 영상이 두 개로 나눠지지않았고 아래 흰 부분이 존재하기 떄문에 1번더 영상 잘라내기를 한다.  
● 아직 외곽 사각형이 남아있기 떄문에 초록색으로 둘러져있어서내부 사각형은 직선 검출 알고리즘을 이용하여 분포도가 많은 부분을 탐색하여 좌표값을 저장한다.

<br>

![image](https://user-images.githubusercontent.com/57826388/78422234-cb87c280-7698-11ea-988b-3f9ba5ecde49.png)
![image](https://user-images.githubusercontent.com/57826388/78422239-cf1b4980-7698-11ea-8c93-7c2097d05867.png)

● 잘려진 두 영상의 해상도를 조절하고 출력하면 다음과 같다.

<br>

## 에폭시 검출

### 1. 단계 (1)

![image](https://user-images.githubusercontent.com/57826388/78422264-fa059d80-7698-11ea-90f4-ccae0fd354d0.png)

● 영상의 에폭시를 검출하기위해 BGR 컬러채널로 변환한다.

<br>

### 2. 단계 (2) ~ (4)

![image](https://user-images.githubusercontent.com/57826388/78422274-10135e00-7699-11ea-8257-6cd86388f719.png)

![image](https://user-images.githubusercontent.com/57826388/78422275-11dd2180-7699-11ea-9ca9-dbe42a976778.png)

● 에폭시가 검출된 영상은 위 영상과 같다.

<br>

### 3. 단계 (5)

![image](https://user-images.githubusercontent.com/57826388/78422288-3638fe00-7699-11ea-8e9d-44924c59cf5c.png)

● RGB 모델로 검출된 영상을 복사하면 다음과 같다.  
● 왼쪽 오른쪽 영상 모두 검출 연산을 진행한다.

<br>

## 검출 결과 분포도 출력

### 1. 단계 (1)

![image](https://user-images.githubusercontent.com/57826388/78422311-5963ad80-7699-11ea-8950-f9239c8a9686.png)

● 그리드를 나누기 위해 매개변수로 간격값을 받는다.

<br>

### 2. 단계: (2) ~ (3)

![image](https://user-images.githubusercontent.com/57826388/78422328-77311280-7699-11ea-9781-86e2b398e797.png)

![image](https://user-images.githubusercontent.com/57826388/78422331-7ac49980-7699-11ea-8f9c-de97f1fe2b7d.png)

● 그리드 내에서 화소값을 계속 탐색하며 그리드 크기와 검출된 객체에 해당하는 화소값의 비를 비교하여 그리드 영상을 그린다.

<br>

### 3. 단계: (4)

![image](https://user-images.githubusercontent.com/57826388/78422345-8dd76980-7699-11ea-85ad-4446a92ba4ba.png)

![image](https://user-images.githubusercontent.com/57826388/78422348-8fa12d00-7699-11ea-8f04-89fb9cb50b73.png)

● 결과 영상을 출력하여보면 다음과 같다. 비교 사진을 보아도 잘 출력 되었음을 한 눈에 알 수 있다.

<br>

# 결과

위의 모든 과정이 메인함수를 통하여 연결되고 출력이 이루어진다.  
이러한 과정을 통하여 결과가 잘 출력되는 것을 확인할 수 있다. 

프로그램에서 사용되는 구조체와 함수를 정리하면 다음과 같다.

>구조체

● resultimg – leftimg: 잘라냈을 때의 왼쪽 이미지 저장 변수  
● resultimg – leftimg: 잘라냈을 때의 오른쪽 이미지 저장 변수  

<br>

>함수

● resultimg cut(Mat): 1차 잘라내기 함수  
● resultimg cut2(Mat): 2차 잘라내기 함수  
● Mat process(Mat): 에폭시 검출 함수  
● Mat resultGrid2(Mat, float, float): 검출 결과 분포도 출력 함수  
● Mat gridWrite(Mat, float,float): 이미지에 그리드 레이아웃 오버렙 함수  