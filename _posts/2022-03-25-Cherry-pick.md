---
toc: true
toc_sticky: true
categories:
  - git
title: Git 다른 브랜치의 특정 commit만 내 브랜치에 반영하기
tags: [git]
excerpt: "Git cherry-pick을 사용하면 다른 브랜치의 특정 commit만 내 브랜치에 반영할 수 있다."
---

# 특정 브랜치의 특정 commit만 내 브랜치에 반영하기

## 문제 상황

브랜치 개수가 늘어나고 사이즈가 커지면서, 각기 다른 형상에 대해 다른 브랜치의 특정 커밋 내용만 반영해야하는 상황.

이럴땐 어떻게 해야할까?


- PR을 통해 머지를 한다?? X

    -> 형상이 다르기 떄문에 형상 자체가 바뀐다 

- 특정 커밋로그를 찾아서 하나씩 코드를 복사한다?? X

    -> 노가다는 방법을 모르기전까진 유효하다.

<br>

## cherry-pick

바로 cherry-pick git 명령어를 사용하면 된다. 
```
cherry-pick
```

<br>

우선 `git logs`나 github 리파지터리를 가서 반영할 커밋해시값을 찾는다.

그 뒤, 아래와 같이 반영할 커밋해시값과 함께 명령어를 입력한다.

```
git cherry-pick 293br39
```

<br>

만약 2개 이상의 커밋을 다 반영하고싶다면..

```
git cherry-pick 293br39 2359rt2
```

<br>

혹은 연속적인 경우..

```
git cherry-pick 293br39..t94j0g3
```

<br>

여기서 충돌이 발생할 수도 있다.

그러면 우선 충돌난 파일을 수정한 후 다음 명령어를 사용하면 다시 진행이 시작 된다.

```
git add [충돌파일경로]
git cherry-pick –continue 
```

<br>

만약 cherry-pick을 중단하고싶으면 다음과 같이 abort명령어를 입력한다.
 
```
git cherry-pick –abort
```

<br>

그리고 내 IDE의 소스코드를 보면 커밋이 반영되어있는 것을 확인할 수 있다.

이제 수정을 하던, 그대로 가던 커밋로그가 반영된 소스코드를 리모트 저장소에 푸시해준다.
