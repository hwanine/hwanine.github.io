---
toc: true
toc_sticky: true
categories:
  - AI
title: 딥러닝 입문 가이드 (NHN Forward 2019) (2)
tags: [Machine Learing, Deep Learning, Pytorch, CNN]
excerpt: "초보자용 딥러닝 입문 가이드"
---

* **연구주제** : 딥러닝
* **연구목적** : 딥러닝 활용
* **연구일시** : 2020년 2월 3일 13:00~18:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR
* **관련연구** : Machine Learing, Deep Learning, Pytorch

<br/>

## 서론

저번 포스팅에서 인공지능에 대한 이론과 머신러닝과 딥러닝의 준비와 과정, 그리고 적합한 모델을 찾는 방법에 대하여 알아보았다.  
이번 포스팅에서는 모델과 모델에서 행하는 연산에 대해 알아보고자 한다.

<br/>

## 본론

### **모델**

여러 입력 데이터의 관계를 계산하여 하나의 결과를 출력한다. 즉, 다음 사진과 같이 결과를 출력한다.

![image](https://user-images.githubusercontent.com/57826388/73741353-6b0a0100-478d-11ea-9178-4a2a93a00da5.png)

<br/>

예를 들어보자. 이미지를 입력으로 받아 개인지 아닌지 판단하는 모델의 경우이다.  
입력값은 이미지의 모든 픽셀이고 출력값은 개다.

![image](https://user-images.githubusercontent.com/57826388/73741942-a953f000-478e-11ea-97c2-20a2b3bc0516.png)

<br/>

코드 구현은 PyTorch로 이루어진다.

![image](https://user-images.githubusercontent.com/57826388/73742065-ed46f500-478e-11ea-981d-4b9a539b8725.png)

- torch.nn.module 을 상속받아 모델을 구현한다.
- def_init_(self): 생성자, 필요한 변수들을 선언
- def forward(self, x): 입력 데이터를 처리하는 함수, 입력데이터x에 적용될 연산 구현

<br/>

![image](https://user-images.githubusercontent.com/57826388/73742316-7b22e000-478f-11ea-90fb-3b0dcd0d508e.png)

다음과 같이 Pytorch의 Linear Class를 활용하여 여러개의 출력 값을 가지도록 확장한다.  

<br/>

이제는 이 모델을 여러 층으로 쌓을 수 있다.  

![image](https://user-images.githubusercontent.com/57826388/73742410-b7eed700-478f-11ea-8d3f-0c79fbbce80b.png)

- 다음 모델은 2개 이상의 층으로 구성되어 모든 포인트를 서로 연결한 모델이다.
- 입력, 출력 수를 문제에 맞게 설정한다.
- 따라서 좀 더 복잡한 문제를 모델링 할 수 있다.

입력층은 데이터 입력, 중간층은 여러개의 층이 존재하고 출력 층은 마지막 층으로 최종 결과를 출력한다.

![image](https://user-images.githubusercontent.com/57826388/73742592-10be6f80-4790-11ea-8d37-2015585756b0.png)

<br/>

코드로 보면 다음과 같다.

![image](https://user-images.githubusercontent.com/57826388/73742672-49f6df80-4790-11ea-9629-fb5e616f4baf.png)

<br/>

### **MLP 모델로 이미지 분류기 만들기**
 
우선 이미지를 다음과 같이 1차원 배열로 처리할 수 있다.  
전체 픽셀 정보를 한번에 전달한다.

![image](https://user-images.githubusercontent.com/57826388/73743002-f769f300-4790-11ea-8c86-36a2bdee5be5.png)

<br/>

하지만 입출력이 늘어나게 되면? 연결의 수는?

![image](https://user-images.githubusercontent.com/57826388/73743086-254f3780-4791-11ea-80f6-8e0f6371797c.png)

이미지의 크기를 28x28에서 256x256으로 확대, 분류 클래스를 1000개로 확대하기 되면 다음과 같이 가중치 수가 기하급수적으로 증가하게 된다.

<br/>

![image](https://user-images.githubusercontent.com/57826388/73743283-86770b00-4791-11ea-8c41-1dbef9a299d1.png)

이러한 문제점을 해결하기 위한 아이디어를 살펴보면, 우선 첫 번째로 이미지 전체 정보가 아닌 인접 영역의 정보만 연결할 수 있다.

1. 인접 영역의 정보만 연결

- MLP를 사용하는 경우 이미지의 모든 픽셀간의 연관관계를 찾으려고 한다.
- 여기서 일부 영역만 보고 연관관계를 찾을 수 있도록 모델링한다.

사진을 보면 우측의 경우 기존 MLP는 36\*6개의 weights가 필요하다. 하지만 왼쪽 사진의 경우에는 인접영역만 연결하는 경우, 6\*6개의 weights가 필요하다. 

<br/>

2. 연결 정보를 재활용

weight를 모두 다르게 구성하지 않아서 단 4개의 weights만 사용할 수 있다.

![image](https://user-images.githubusercontent.com/57826388/73744007-39943400-4793-11ea-8bc9-3145459f67c2.png)

<br/>

3. 이미지의 픽셀 위치 정보 보존

2차원 모양 그대로 유지해서 표현한다.

![image](https://user-images.githubusercontent.com/57826388/73744203-ad364100-4793-11ea-9bdd-6b2cd04c464e.png)

<br/>

## 결론

딥러닝 모델에서 입력 데이터로 결과 값이 어떻게 출력되는지, 그리고 여러 층을 겹겹이 쌓아서 어떻게 하면 효율적인 연산을 할 수 있는지 알아보았다.  
다음에는 모델에 대한 나머지 이야기와 실전 모델에 대해 설명하겠다.

<br/>

## 향후과제

실전 모델 설계 및 구현

<br/>

## 참고자료

<https://forward.nhn.com/2019/seoul/presentation-session>  
<https://www.slideshare.net/NHNFORWARD/feat-pytorch>

<br/>

*Writer: Jae-Hwan Lee*








