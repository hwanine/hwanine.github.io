---
toc: true
toc_sticky: true
categories:
  - Android
title: Android - 안드로이드 스튜디오 APK 파일 만들기
tags: [Android, Android Studio]
excerpt: "안드로이드 스튜디오에서 개발한 앱의 APK를 생성하는 방법 소개"
---

# APK 생성

앱을 개발해서 플레이스토어에 출시하거나, 중간에 APK파일을 추출해야할 필요가 있을 경우 APK파일을 생성해야합니다.

이번 포스팅에서는 APK파일 생성 과정을 소개하겠습니다.

<br>

> Build - Generate Signed Bundle / APK

상단 메뉴에서 다음의 경로로 선택을 합니다.

![image](https://user-images.githubusercontent.com/57826388/80280990-f1a5fd00-8742-11ea-8d6e-facb21bed0dc.png)

<br>

> APK or Bundle 선택

여기서 APK를 선택해줍니다.  
Bundle의 경우에는 구글 플레이 스토어에서 추천하는 방법으로 사용자로 하여금 안전하고 빠르게 앱을 설치할 수 있는 파일이라고 보시면 됩니다.

Bundle이나 APK나 생성 과정은 같기 때문에 APK를 선택해보겠습니다.

![image](https://user-images.githubusercontent.com/57826388/80281032-4d708600-8743-11ea-82ee-d34675e6d0a9.png)

<br>

> 키 경로 생성

키를 저장할 경로를 선택합니다.  
Create new... 버튼을 선택합니다.

![image](https://user-images.githubusercontent.com/57826388/80281074-9de7e380-8743-11ea-8569-4d1e85e437ff.png)

<br>

여기서 다음과 같은 화면이 나올텐데, 파란색으로 표시가 있는 부분을 채워줍니다.

여기서의 `비밀번호`와 `키 저장 경로`, `Alias` 꼭 기억해두셔야 합니다. 또한 생성되는 `jks 파일`도 잘 보관해주어야 합니다.

그렇지 않을 경우, 앱을 출시하고도 업데이트를 하지 못하여 새로 앱을 출시해야할 수 있습니다.

![image](https://user-images.githubusercontent.com/57826388/80281112-e30c1580-8743-11ea-8fb3-d6f080fe90fb.png)

<br>

> 나머지 정보 기입

키 경로를 다 입력했다면, 키 경로가 생성됩니다.  
이제 키 경로를 만들 때의 정보들을 마저 입력해주고 Next를 누릅니다.

`Remember passwords`는 혹시모르니 체크하시는걸 추천합니다.

![image](https://user-images.githubusercontent.com/57826388/80281188-688fc580-8744-11ea-9167-83149568b9ca.png)

<br>

> 버전 선택

배포를 위한 release를 선택해줍니다.  
또한 버전을 선택해야하는데, 저는 V1, V2 모두 체크하는 것을 추천합니다.  
왜냐하면 V2만 체크한다면 구형 디바이스에서 정상적으로 설치되지 않을 수도 있습니다.

체크를 완료했으면 Finish를 누르고 1~2분 정도 기다려줍니다.

![image](https://user-images.githubusercontent.com/57826388/80281224-ad1b6100-8744-11ea-834e-0d63d25e8ebe.png)

<br>

> 생성 확인

APK 파일 생성이 완료되었다면 다음과 같이 우측 하단에 알림이 표시됩니다.  

![image](https://user-images.githubusercontent.com/57826388/80281321-3d59a600-8745-11ea-9b06-6c8e9b69a8a2.png)

<br>

locate 링크를 클릭하면 APK 파일이 저장된 폴더가 열리게 됩니다.  
aab파일은 번들을 선택했을 경우 나오는 파일입니다. 

이렇게 APK 파일 생성하는 내용을 알아보았습니다.  
초기 생성 시, 키 파일에 대한 백업이 가장 중요하므로 다시 한번 주의하시길 바랍니다.

![image](https://user-images.githubusercontent.com/57826388/80281332-4fd3df80-8745-11ea-923a-bed09a767d7a.png)