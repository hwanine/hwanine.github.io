---
toc: true
toc_sticky: true
categories:
  - AI
title: 딥러닝 입문 가이드 (NHN Forward 2019) (3)
tags: [Machine Learing, Deep Learning, Pytorch, CNN]
excerpt_separator: <!--more-->
---

# 딥러닝 입문 가이드 (4)
<!--more-->
* **연구주제** : 딥러닝
* **연구목적** : 딥러닝 활용
* **연구일시** : 2020년 2월 5일 09:00~18:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR
* **관련연구** : Machine Learing, Deep Learning, Pytorch

<br/>

## 서론

저번 포스팅에서 딥러닝 모델에서 입력 데이터로 결과 값이 어떻게 출력되는지, 그리고 여러 층을 겹겹이 쌓아서 어떻게 하면 효율적인 연산을 할 수 있는지 알아보았다. 
이번에는 모델에 대한 나머지를 설명하고 실전 모델을 경험해보자!

<br/>

## 본론

지난 시간에 이어서 딥러닝 이미지 처리의 대한 설명을 계속 한다.

- Convolutional Neural Network

Neural Network는 이미지 처리에 효과적인 구조를 포함한다.  
이는 MLP의 일부 Layer를 이미지 처리에 적합한 Layer로 변경한다.

![image](https://user-images.githubusercontent.com/57826388/73751597-bf6bab80-47a2-11ea-9592-d05061bdcfc7.png)

이렇듯 CNN에서는 다음과 같이 일부를 연결한 Convolution Layer, Pooling Layer, Fully-Connected Layer을 이용한다.

- Convolutional Layer 연산

<br/>

이 연산은 입력 데이터 일부 영역에 대한 Welghted Sum + Activation Function 이다.   
입력데이터에 weight matrix를 이동해가며 결과를 생성한다.

![image](https://user-images.githubusercontent.com/57826388/73751781-1c676180-47a3-11ea-9870-a08bff5aa6b0.png)

만약 이미지에 동일한 weight matrix를 반복해서 곱하면 어떻게 될까?  
weight matrix의 구성 방식에 따라 입력 이미지와 convolution 연산을 하면 다양한 이미지의 특징을 추출할 수 있다.

![image](https://user-images.githubusercontent.com/57826388/73752113-b0d1c400-47a3-11ea-810f-320de8dcfa69.png)

<br/>

-Pooling Layer 연산

간단한 규칙으로 feature map의 크기를 줄일 수 있다.  
이미지 영역을 나눠 대표 값을 선정한다. 대표 값은 max, average 함수로 선정한다.

![image](https://user-images.githubusercontent.com/57826388/73752510-70bf1100-47a4-11ea-9427-c14b756ac724.png)

<br/>

이에따라 코드를 작성하면 다음과 같다.

![image](https://user-images.githubusercontent.com/57826388/73752642-a5cb6380-47a4-11ea-812f-cd40b24eafd4.png)

<br/>

### **실전**

그럼 이제 음식 종류를 맞추는 모델을 만들어보자!  
데이터는에 해당하는건 음식 사진 모음, 예측 결과는 음식의 종류이다.

![image](https://user-images.githubusercontent.com/57826388/73754407-b7fad100-47a7-11ea-9b24-298fba36392d.png)

<br/>

우리는 데이터를 잘 표현할 수 있는 모델을 찾고 데이터를 예측하면 된다.

![image](https://user-images.githubusercontent.com/57826388/73754595-f2fd0480-47a7-11ea-9e32-482fd559da0e.png)

<br/>

#### 데이터 준비

우리는 학습시킬 데이터가 필요하다. 준비할 데이터는 다음과 같다.

1. 카테고리
- 분류하고자 하는 카테고리를 정의하여 카테고리 마다 id를 부여한다.

2. 입력 데이터 수집
- public dataset 활용 (실습 데이터는 iFood 데이터 세트를 활용한다.)
- 또는 크라울링(Crawling) 하는 방법도 있다.

<br/>

#### 모델 선정

잘 알려진 유명 모델들이 존재한다.  
이미지 처리에 효과적인 구조를 포함하는 대표 모델로는 Neural Network Model이 있다.

모델은 검증된 모델을 활용하는 것이 좋다.  
좋은 성능을 보이는 검증된 모델을 활용하면 쉽고 빠르게 기본 모델을 만들 수 있다.  
우리는 ResNet 모델을 사용하겠다.

> Transfer Learning: Pre-trained weight로 network 초기화

- 잘 알려진 네트워크들은 imagenet dateset으로 train한 weight 파일을 함께 공개한다.
- 다른 데이터 세트로 훈련시킨 pre-trained weight 값을 초깃값으로 사용하면 빠르고 안정적으로 Training 가능하다.

<br/>

## 결론

딥러닝을 활용하는 실전 모델을 선정하는 방법을 알았다.  
다음 포스팅에서는 직접적으로 PyTorch로 이미지를 학습하도록 개발자가 직접 코드로 구현하여 학습하고 활용할 수 있는 내용에 대하여 알아보도록 하자.

<br/>

## 향후과제

직접 데이터를 학습 시키고 활용하는 구현까지..

<br/>

## 참고자료

<https://forward.nhn.com/2019/seoul/presentation-session>  
<https://www.slideshare.net/NHNFORWARD/feat-pytorch>

<br/>

*Writer: Jae-Hwan Lee*