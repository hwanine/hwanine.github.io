---
layout: post
title: 딥러닝 입문 가이드 (NHN Forward 2019) (1)
feature-img: "assets/img/pexels/computer.jpeg"
thumbnail: "https://user-images.githubusercontent.com/57826388/73757402-89332980-47ac-11ea-87f2-e36bd15b5d69.png"
tags: [Machine Learing, Deep Learning, Pytorch, CNN]
author: Jae-Hwan Lee
excerpt_separator: <!--more-->
---

# 딥러닝 입문 가이드 (1)
<!--more-->
* **연구주제** : 딥러닝
* **연구목적** : 딥러닝 활용
* **연구일시** : 2020년 1월 31일 13:00~18:00
* **연구자** : 이재환 <ljh951103@naver.com>
* **연구장비** : HP EliteDesk 800 G4 TWR
* **관련연구** : Machine Learing, Deep Learning, Pytorch

<br/>

## 서론

![image](https://user-images.githubusercontent.com/57826388/73757540-b4b61400-47ac-11ea-9a67-4cf6acd260e1.png)

NHN Forward 2019 컨퍼런스에 참가했을 떄, 기억에 남았던 세션이 있었다.  
입문자를 위한 딥러닝 강의였는데 이번 포스팅에서는 그 때의 자료를 참고하고 느꼇던 기억을 살려, 많은 분들에게 그 때의 학습내용을 전달하고자 글을 쓰게 되었다.

<br/>

## 본론

### **딥러닝? 인공지능? 머신러닝?**

`인공지능`은 인간의 지능을 갖고 있는 기능을 갖춘 컴퓨터 시스템을 의미한다.  
즉, 인간의 지능을 기계 등에 인공적으로 구현한 것이다.
현대사회에서 이러한 인공지능(AI)은 다양한 분야로 활용되고있다.  

그렇다면 우리 개발자의 관점에서 AI는 개발해야할 목표가 된다.
AI를 만드는 방법을 `머신러닝`이라고 표현한다. 다양한 머신러닝 방법 중 하나가 바로 `딥러닝`이다.

> 머신러닝(딥러닝)을 한다는 것은?

데이터를 잘 표현할 수 있는 알고리즘(모델)을 찾고 새로운 데이터를 예측하는 것이라고 할 수 있다.

![image](https://user-images.githubusercontent.com/57826388/73723711-0dfe5300-476d-11ea-91aa-ee36a10ecd22.png)

![image](https://user-images.githubusercontent.com/57826388/73723931-8a913180-476d-11ea-837d-0a775477e5d4.png)

<br/>

### **준비**

딥러닝을 시작하기 위해 필요한 것들이다. (개발 언어, 개발 환경, 라이브러리)

- 개발 언어: 파이썬
- 개발 환경: Jupyter
- 라이브러리: PyTorch, Tensorflow...

딥러닝 프레임워크에는 다양한 모델이 존재한다. 이 중, 가장 많이 쓰이는 모델이 바로 `Pytorch`와 `TensorFlow`다. 여기서 설명할 모델은 PyTorch이다.

![image](https://user-images.githubusercontent.com/57826388/73724102-f378a980-476d-11ea-8d13-80bbbdbd0dd7.png)

>PyTorch

PyTorch는 대표적인 오픈소스 머신러닝 프레임워크로 페이스북에 의해 개발 및 유지보수 되고있다.

- `Numpy + GPU 사용 + 딥러닝`
- 연구 목적의 빠른 프로토타이핑에 적합하다
- 기본 자료구조 "tensor"

![image](https://user-images.githubusercontent.com/57826388/73724307-58cc9a80-476e-11ea-8f67-17e8b8068798.png)

위에서 언급한 NumPy는 C언어로 구현된 파이썬 라이브러리다.  
수치계산을 위한 기능들을 제공한다.

<br/>

### **이론**

이렇듯, 데이터로 훈련을 하여 모델을 생성하는 것이 머신러닝이라고 할 수 있다.  
직접적으로 딥러닝에 들어가기전에 필요한 이론지식을 살펴보자.

#### **훈련: 데이터를 잘 표현하는 모델을 찾는 과정**

데이터를 잘 표현하는 모델에는 수 많은 모델이 존재한다. 우리는 그 중 좋은 것을 사용하려 할 것이다. 그렇다면 어떤 것이 더 좋은가?

![image](https://user-images.githubusercontent.com/57826388/73724611-0b046200-476f-11ea-939d-22d6b770c141.png)

모델의 예측값과 실제값의 **차이가 작을 수록** 좋은 모델이다.  
밑의 예시는 (예측값 - 실제값)^2 의 평균이다. 따라서 Model1이 가장 좋은 모델이라고 볼 수 있다.

**그렇다면 어떻게 이 값을 찾는가?**

1. w 벡터를 찾는다.

이론적으로는 모델의 w벡터를 찾는 문제다.이 때, w는 가중치를 의미한다.

![image](https://user-images.githubusercontent.com/57826388/73725057-fd031100-476f-11ea-86af-60b6c953781c.png)

<br/>

2. w에 대한 최솟값을 찾는다.

Loss 함수를 활용하여 w벡터를 계속 업데이트하며 최적의 지점을 찾는다.

![image](https://user-images.githubusercontent.com/57826388/73725164-2cb21900-4770-11ea-8b90-61b56fbafd0b.png)

<br/>

이론 상으로는 복잡해보이지만 코드를 이용하여 함수를 적용하면 간단하게 찾을 수 있다.

![image](https://user-images.githubusercontent.com/57826388/73725328-83b7ee00-4770-11ea-98a4-a6487216e023.png)

<br/>

## 결론

지금까지 우리는 인공지능이 뭔지, 그리고 인공지능을 개발하는 머신러닝과 딥러닝, 딥러닝을 개발하기위한 라이브러리, 언어, IDE를 알아보았다.  
또한 데이터를 잘 표현하는 모델을 찾는 과정에 대해 알아보았다.
다음에는 이어서 모델에 대한 정보를 포스팅하겠다.

<br/>

## 향후과제

머신러닝 모델, 과정

<br/>

## 참고자료

<https://forward.nhn.com/2019/seoul/presentation-session>  
<https://www.slideshare.net/NHNFORWARD/feat-pytorch>

<br/>

*Writer: Jae-Hwan Lee*