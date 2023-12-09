---
toc: true
toc_sticky: true
categories:
  - c++
title: \[C++\] Windows DLL 전역변수 공유 메모리 셋팅 방법 (IPC)
tags: [c++, registry]
excerpt: "Windows 환경에서 DLL의 전역변수를 공유 메모리로 만들어서 사용하는 방법"
---

# Windows DLL 전역변수 공유 메모리 셋팅 방법

## 개요

DLL 배포시 여러 프로세스에서 DLL에 접근하는 경우가 생길 수 있다.

이 경우, 단순 API처럼 특정 요청에 대한 결과만 응답해주면 되는 것이 아닌, 전역변수로 어떠한 값을 보존 후, 여러 DLL에서 이를 활용할 때가 있을 수 있다.

DLL은 로드시 DLL안의 메모리는 항상 프로세스별로 새로 초기화가 된다.

그래서 우리는 프로세스별로 DLL을 각각 초기화해서 사용할 수 밖에 없고, 이 전역변수를 공유 메모리로 설정하면 어느 프로세스에서나 동일한 메모리값이 유지가 된다.

<br>

## IPC

IPC란 ``Inter Process Communication`` 의 약자이고 프로세스들끼리 서로 데이터를 주고 받는 방법을 의미한다.

IPC 방법은 다음 두 가지다.

1. 메시지 전달
2. 메모리 공유

여기서 소개할 방법은 Windows에서 제공하는 2번 메모리 공유 방법이다.

<br>

## 전역 변수 공유 메모리

아래는 코드 예시다.

```c++
#pragma data_seg (".shareEx")
   int i = 0; 
   char a[32]n = "hello world";
#pragma data_seg()
#pragma comment(linker, "/section:shareEx,RWS")
```

C++ 상단 헤더아래 전역변수 정의를 ``#pragma data_seg`` 로 감싸준다.

여기서 ``.shareEx`` 값은 세그먼트 이름으로 보면 된다. 입맛에 맞게 변경해주면 된다.

그리고 링크를 걸어주면된다.
``/section:shareEx,RWS`` 에서 RWS는 Read/Write/Shared 이다. 필요한 경우 변경하면 된다.

주의해야할 점은, 반드시 해당 변수 초기화를 저 전처리 구문 내에서 해야한다는 점이다.

선언만 할 경우, 공유가 되지않는다.

<br>

이제 여러 프로세스에서 접근했을 때, 메모리 값이 잘 유지 되는 것을 확인 할 수있다.

하지만 STL 컨테이너는 공유가 안되는 것을 확인할 수 있다.

이 내용은 다음 포스팅에서 다루도록 하겠다.

<br>

- 참고자료
    - https://learn.microsoft.com/ko-kr/previous-versions/visualstudio/visual-studio-2008/h90dkhs0(v=vs.90)?redirectedfrom=MSDN

	