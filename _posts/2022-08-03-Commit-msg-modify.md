---
toc: true
toc_sticky: true
categories:
  - git
title: Git 커밋 메시지 수정하기
tags: [git]
excerpt: "Git 저장소에 올린 커밋메시지나 로컬 저장소에 커밋한 커밋메시지를 수정해보자."
---

# Git 커밋 메시지 수정하기

## 문제 상황

1. 단위 개발을 완료하여 commit을 하였으나 커밋메시지 입력을 잘못하여 수정이 필요하다.

2. commit과 push까지 완료하였으나 뒤늦게 확인해보니 커밋메시지 수정이 필요하다.

<br>

## 최근 commit 메시지 수정하기

최근 커밋메시지는 간단하게 변경할 수 있다.

여기서 설명할 방법은 2가지다.

- 첫번쨰 방법

다음과 같은 명령어를 입력하면 다음 vi 편집기 화면이 나온다.

최상단엔 나의 마지막 커밋이 있다. 

```
git commit --amend
```

![image](https://user-images.githubusercontent.com/57826388/223471994-36235d3f-05f5-4edd-8499-52e871277e06.png)


<br>

이 커밋 메시지를 수정한 후, :wq로 저장하고 빠져나온다.

<br>

- 두번쨰 방법

vi 수정할 필요없이, 명령어에서 바로 처리가능하다. 아래 명령어로 입력하면 커밋메시지가 변경된다.

```
git commit --amend -m "변경 메시지"
```

<br>

이젲 커밋메시지 변경이 안료되었다. 원격 저장소로 푸시한다.

```
git push origin [branch] -f
```

<br>

## 여러 or 특정 commit 메시지 수정하기

여러 커밋 메시지나 특정 커밋 메시지를 변경하는 방법은, 이전 커밋 병합 포스팅했을 때의 방법과 같다.

예를들어, 3가지 커밋 메시지나 세번째 커밋 메시지를 변경하고 싶다면 다음과 같이 이전 세번째 커밋까지 rebase 해준다.

```
git rebase -i HEAD~3
```

<br>

이제 이전 세번째 커밋으로 돌아간 상태다.

정상적으로 명령어를 입력했으면

다음 vi 편집기 화면이 나온다.

![image](https://user-images.githubusercontent.com/57826388/223461691-3ded3223-9f9a-4cba-a0af-1f560099058c.png)

<br>

여기서 2, 3번 pick을 각각 reword로 변경한다.

그렇게 :wq 하여 저장하면 처음 --amend를 했을 떄와 같이, 각각의 커밋에 대해 메시지를 변경할 수 있다.

이제 모두 완료가 되었다.

원격 저장소에 반영하면된다.

```
git push origin [branch] -f
```