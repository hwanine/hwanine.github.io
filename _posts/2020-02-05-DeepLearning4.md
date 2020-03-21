---
toc: true
toc_sticky: true
categories:
  - AI
title: 딥러닝 입문 가이드 (NHN Forward 2019) (4)
tags: [Machine Learing, Deep Learning, Pytorch, CNN]
excerpt: "초보자용 딥러닝 입문 가이드"
---

* **연구주제** : 딥러닝
* **연구목적** : 딥러닝 활용
* **연구일시** : 2020년 2월 5일 09:00~18:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR
* **관련연구** : Machine Learing, Deep Learning, Pytorch

<br/>

## 서론

딥러닝을 활용하는 실전 모델을 선정하는 방법을 알았다.  
이번 포스팅에서는 직접적으로 PyTorch로 이미지를 학습하도록 개발자가 직접 코드로 구현하여 학습하고 활용할 수 있는 내용에 대하여 알아보도록 하자.

<br/>

## 본론

이제 PyTorch로 직접 학습을 해본다.  
PyTorch의 학습 코드가 동작할 수 있도록 필요한 부분을 구현한다.

- torch.utils.data.Dataset을 상속받아 `Dataset Class`를 구현한다.
- torch.nn.Module을 상속받아 `Model Class`를 구현한다.

![image](https://user-images.githubusercontent.com/57826388/73760135-4bd09b00-47b0-11ea-969a-a794f91f73f1.png)

<br/>

1. Dataset Class 구현

우리가 가지고 있는ㄷ 데이터를 list 파일을 생성한다.

![image](https://user-images.githubusercontent.com/57826388/73760281-76baef00-47b0-11ea-9a59-48ab613bcacc.png)

그리고 json 파일 또한 생성해준다.

![image](https://user-images.githubusercontent.com/57826388/73760367-905c3680-47b0-11ea-99d8-55691e7f06b3.png)

이제 코드에서 torch.utils.data.DataLoader로 차례로 데이터를 읽어온다.

![image](https://user-images.githubusercontent.com/57826388/73760483-b8e43080-47b0-11ea-9af0-032f9b604f58.png)

#### _init_ 함수

- data를 읽어올 때, 필요한 정보를 저장한다.
- data.list 파일을 읽어서 리스트 만들기

#### _getitem_ 함수

- 특정 index가 요청되었을 떄, 리턴
- 리스트의 index에 접근  
  - 이미지 읽기, resize, tensor로 변환
  - 라벨은 float 형태로 변환

#### _len_ 함수

- 전체 data sample의 수를 리턴
- 리스트의 크기를 

<br/>

2. DataLoader Class 활용

데이터로더 클래스를 이용하여 학습 데이터들을 불러올 수 있다. 

![image](https://user-images.githubusercontent.com/57826388/73760997-8850c680-47b1-11ea-8a82-adeda39a53d6.png)

<br/>

3. Module Class 구현

Torchvision.models에 사전에 정의된 모델을 활용한다.

- 잘 알려진 네트워크들의 경우 아키텍쳐와 pretrained-weights를 제공한다.
- 예를들어, alexnet, vgg, resent, inception
- 기존 모델을 불러와 카테고리 수로 output 변경한다.
  
  ![image](https://user-images.githubusercontent.com/57826388/73761548-5c821080-47b2-11ea-9e30-d72558b8fde9.png)

### **실행**

이제 우리가 만든 모델을 실행시키고 동작해보자.  
우선 우리가 구현한 학습 모델과 데이터, 학습 코드는 다음과 같다.

![image](https://user-images.githubusercontent.com/57826388/73761741-981cda80-47b2-11ea-88f5-8c74f5482430.png)

우측 실행창을 통해 정상적으로 학습이 되게있다는 것을 확인할 수 있다.  

<br/>

다음 학습된 모델을 이용하여 데이터를 주었을 때, 정상적으로 오차율을 출력하며 분류가 되는 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/57826388/73762481-9b649600-47b3-11ea-9400-a34357c8ec61.png)

<br/>

## 결론

이렇게 우리는 딥러닝의 기초를 배우고 직접 간단히 구현하야 사용할 수 있다.  
컨퍼런스 참가 당시만 해도 딥러닝이 낯설고 나에겐 어렵기만한 기술인 줄 알았다. 하지만 이번 세션을 들으면서 이미 인공지능분야가 널리 알려진 기술이고 그로인한 프레임워크도 많이 생겨났기 때문에 일반 개발자 입장에서는 그저 사용하기만 하면 되어서 굉장히 간단하고 흥미를 많이 가지게 되었다.  

이러한 세션을 주제로 포스팅을 진행하였는데 직접 하나씩 진행하지는 못했지만 원리와 흐름은 확실히 이해가 되었던 시간이었다. 이제는 실제로 딥러닝을 구현하여 써보도록 하겠다!

<br/>

## 향후 과제

딥러닝을 이용한 졸업작품 제작

<br/>

## 참고자료

<https://forward.nhn.com/2019/seoul/presentation-session>  
<https://www.slideshare.net/NHNFORWARD/feat-pytorch>

<br/>

*Writer: Jae-Hwan Lee*


