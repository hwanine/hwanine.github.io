---
toc: true
toc_sticky: true
categories:
  - Git
title: Git - Fork한 Repository를 원본 저장소와 동기화(업데이트) 하기  
tags: [Git]
excerpt: "Git을 사용할 때, Fork한 본인 저장소와 원본 저장소와 동기화하는 방법을 다루겠습니다."
---

# Fork한 Repository 업데이트

### 1. 개인 저장소 Clone

fork한 개인 저장소를 로컬 PC에 저장해줍니다.

```
$ git clone https://github.com/본인ID/본인저장소.git
```

<br>

### 2. Clone한 저장소 이동

clone한 프로젝트를 찾아서 해당 저장소로 이동합니다

<br>

### 3. 리모트 저장소 확인

내가 가져온 저장소의 원본 저장소를 확인해봅니다.  
현재 동일하게 설정되어 있습니다.

```
$ git remote -v
origin	https://github.com/본인ID/본인저장소.git (fetch)
origin	https://github.com/본인ID/본인저장소.git (push)
```

<br>

### 4. 리모트 저장소 추가

이전에 확인했던 리모트 저장소에 원본 저장소를 추가해줍니다.  
여기서 원본 저장소를 임의로 upstream으로 지정해주었습니다.

```
$ git remote add upstream https://github.com/원본저장소계정ID/원본저장소이름.git
```

<br>

### 5. 리모트 추가 확인

확인해보면 이전과 다르게 리모트 저장소가 생겼음을 알 수 있습니다.

```
$ git remote -v 
```

```
origin	https://github.com/본인ID/본인저장소.git.git (fetch)
origin	https://github.com/본인ID/본인저장소.git.git (push)
upstream	https://github.com/원본저장소계정ID/원본저장소이름.git (fetch)
upstream	https://github.com/원본저장소계정ID/원본저장소이름.git (push)
```

<br>

### 6. 원본 저장소 fetch

원본 저장소를 fetch 하여 가져옵니다.

```
$ git fetch upstream
```

<br>

### 7. 원본 저장소 merge

원본 저장소와 로컬 저장소를 merge 하여 합쳐줍니다.

```
$ git merge upstream/master
```

<br>

### 8. 개인 깃 저장소로 push

마지막으로 push 하여 개인 저장소를 업데이트 할 수 있습니다.

```
$ git push
```

<br>

- 참고자료: <https://velog.io/@k904808/Fork-%ED%95%9C-Repository-%EC%97%85%EB%8D%B0%EC%9D%B4%ED%8A%B8-%ED%95%98%EA%B8%B0>
