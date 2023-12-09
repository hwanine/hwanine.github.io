---
toc: true
toc_sticky: true
categories:
  - c++
title: \[C++\] DLL 내부에서 boost library를 활용하여 STL 컨테이너 공유 메모리 셋팅 방법 (IPC)
tags: [c++, registry]
excerpt: "DLL을 접근할 때, boost library를 활용하여 STL 컨테이너 공유 메모리 셋팅 방법 "
---

# STL 컨테이너 공유메모리 사용법

## 개요

지난포스트에서 DLL으로 여러프로세스가 접근할 때, 공유 메모리 사용 방법에 대해 다뤘었다.
https://hwanine.github.io/c++/shared-memory/

하지만 int, double, char.. 등 일반적인 자료형에선 공유 메모리 셋팅이 되지만, STL 컨테이터는 공유가 되지 않았다.

STL(Standard Template Libary) 컨테이너는 vector, deque, list, map 등의 자료구조를 가진다.

프로세스 내 STL 컨테이너 형태에서 공유메모리에 접근하기 위해서는 boost::interprocess 라이브러리를 사용해야한다.

<br>

## 설치
 
우선 boost 라이브러리를 사용하기위해선 설치과정이 필요하다.

1. 설치는 아래 링크에서 최신 버전을 찾아 다운로드 받으면 된다.
    
    https://www.boost.org/

2. 이후 다운로드된 압축파일을 압축을 풀어주고 ``boost/bootstrap.bat`` 파일을 실행한다.

3. 이후 같은 경로에 생성된 ``boost/b2.exe`` 파일을 실행시켜주면 꽤 긴 시간 빌드를 진행한다.
    - 윈도우에서 사용하기위해선 비주얼스튜디오 컴파일러 버전을 명시해야할 필요가 있을수 있다. 이럴 경우 콘솔로 실행하여 다음 명령어 인수를 추가하여 실행한다.
    - ``./b2.exe --toolset=msvc-14.2``

<br>

## 셋팅

이후 빌드가 완료되면 빌드된 파일들이 나온다.
비주얼 스튜디오에서 라이브러리와 헤더파일을 세팅하자.

해당 경로를 라이브러리 디렉터리로 셋팅한다.

> boost/stage/lib

해당 경로를 헤더 디렉터리로 셋팅한다.

> boost/

<br>

## 구현

이제 실제로 라이브러리를 사용해보자.
일단 상단에 다음의 헤더를 포함시켜준다.

본 예제로 사용해볼 컨테이너는 map으로, 헤더를 포함시켜준다. 
allocator는 IPC에서 메모리 할당과 해제를 담당한다.

```c++
#include <boost/interprocess/managed_shared_memory.hpp>
#include <boost/interprocess/containers/map.hpp>
#include <boost/interprocess/allocators/allocator.hpp>
```

<br>

- 참고자료
    - https://kukuta.tistory.com/289
    - https://coding-chobo.tistory.com/115
