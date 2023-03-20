---
toc: true
toc_sticky: true
categories:
  - git
title: Git 커밋을 되돌리는 방법들
tags: [git]
excerpt: "Git 커밋을 되돌리는 방법들에대해 알아보자."
---

# Git 커밋 되돌리기

## revert

revert는 특정 커밋만 되돌려서 다시 작업 후 커밋하는 방식이다.

즉, 커밋 내역이 지워지지는 않고 이전 커밋내용을 불러와서 새로운 커밋으로 반영하는 것이다.

사용은 다음과 같다.

```
git revert [되돌리고 싶은 커밋 이름]
```

![image](https://user-images.githubusercontent.com/57826388/223475268-dba77957-e7e5-4f0e-a2c4-30fb27ede5e2.png)

<br>

위 이미지와 같이 신규 커밋이 생성되는 것을 알 수 있다.

<br>

## reset

reset 명령어는 옵션이 3가지가 있다.

기본값은 `--mixed` 로 실행된다.

간단하게 살펴보면 다음과 같다.

``` 
공통적으로 HEAD는 변경될 커밋을 가리킨다.
--soft : Staging Area와 Working Directory는 기존의 커밋의 상태로 유지된다.
--mixed: Working Directory만 기존의 커밋의 상태로 유지된다.
--hard: 유지되는것 없이 모든게 변경될 커밋 기준으로 변경된다.
```

<br>

이제 자세히 살펴보자.

### ***--soft***

--soft 옵션은 모든 변경사항이 그대로 남고 Head만 변경된다.

```
git reset --soft HEAD~1
```

![image](https://user-images.githubusercontent.com/57826388/223479244-a5aa7f6d-d85c-4230-93c4-a5e52755dd73.png)

<br>

### ***--mixex***

--mixed 옵션은 작업 디록토리가 남고, 스테이징 상태와 Head가 변경된다.

```
git reset --mixex HEAD~1
```

![image](https://user-images.githubusercontent.com/57826388/223480668-1b3693c3-b139-4bd1-bbd9-01fab4fb8a86.png)

<br>

### ***--hard***

--hard 옵션은 남는것 없이 모든게 변견된다.

```
git reset --hard HEAD~1
```

![image](https://user-images.githubusercontent.com/57826388/223480372-f71480b3-bbab-4868-aeb9-77842fa75f7a.png)

<br>

## revert vs rebase

그럼 어떨때 revert를 쓰고 어떨때 rebase를 쓸까?

커밋을 수정하느냐, 새로 만드느냐의 차이다.


협업 관점에서 보자면,

로컬에서의 커밋만을 변경할 경우, reset를 사용하는게 좋다.

반대로 원격 저장소의 커밋 변경을 할 경우, revert를 하여 이력을 확실히 남기는쪽으로 관리하는게 좋다.

<br>

- 참고자료: https://www.freecodecamp.org/news/content/images/2021/08/image-117.png