---
toc: true
toc_sticky: true
categories:
  - git
title: Git 여러개 커밋 합치기
tags: [git]
excerpt: "Git 저장소에 올린 여러 커밋을 합치는 방법에 대하여"
---

# Git 여러개 커밋 합치기

## 문제 상황

작업을 마무리하고 나의 브랜치에 Git push까지 완료하였으나, 미처 수정못한 테스트 코드나 오류 코드가 존재할 수 있다.

이럴 경우 대부분 그 1줄분량의 코드를 수정하여 다시 commit 한다.

팀원들과 함께 작업할 경우, 나중에 PR하였을때 민망한 경우가 생길 수 있고 정리안된 커밋로그를보면 괜히 신경쓰이는 경우가 많다.

이런 상황에서 Remote 저장소의 커밋들을 합칠 수 있다.

<br>

## rebase

바로 rabase git 명령어를 사용하면 된다.

예를들어, 3가지 커밋을 통합하고 싶다면 다음과 같이 이전 세번째 커밋까지 rebase 해준다.

```
git rebase -i HEAD~3
```

<br>

이제 이전 세번째 커밋으로 돌아간 상태다.

정상적으로 명령어를 입력했으면

다음 vi 편집기 화면이 나온다.

![image](https://user-images.githubusercontent.com/57826388/223461691-3ded3223-9f9a-4cba-a0af-1f560099058c.png)

<br>

여기서 2, 3번 pick을 각각 s로 변경한다.

이것은 첫번쨰 commit으로 2, 3번 commit을 합치겠다는 의미이다.

그렇게 :wq 하여 저장하면 다음과 같은 화면으로 바뀐다.

![image](https://user-images.githubusercontent.com/57826388/223464890-867ab6e6-ad36-482b-a746-d5ffaff12b51.png)

<br>

여기서 맨위 **`# This is the 1st commit message:`** 아래 커밋메시지를 원하는 문구로 변경하고, 아래 2번 3번 메시지를 제거한다.

그 후, 다시 :wq로 빠져온다.

<br>

이제 모두 완료가 되었다.

원격 저장소에 반영하면된다.

우리는 rebase를 했기 때문에 강제로 -f 옵션을 주어야한다.

다음과 같이 원격 저장소로 푸시한 후 마무리한다.
