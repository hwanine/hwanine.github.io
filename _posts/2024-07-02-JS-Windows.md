---
toc: true
toc_sticky: true
categories:
  - Web
title: 웹 브라우저 환경에서의 Javascript로 로컬 Windows .EXE 파일을 실행시키는 방법
tags: [Web, JS, Windows]
excerpt: "웹 브라우저 환경에서의 Javascript로 레지스트리를 통한 로컬 Windows .EXE 파일을 실행시키는 방법을 소개합니다."
---

# Web JS <-> Windows .EXE

클라이언트 PC에서 브라우저를 통해 Web 서비스를 이용할 수 있다.  
필요한 모든 동작들이 서버를 통해서 처리되면 문제가 없겠으나, 실제적으로 제한되는 작업들이 있다.  

서버 부하를 많이 필요로 하는 동작이나 클라이언트 PC의 Windows 시스템을 제어하는 동작이라던지, Native한 여러 기능동작이 필요한 경우 등이 있다.  
과거에는 **`ActiveX`** 를 설치하여 해당 시스템과 연동하며 사용을 많이 했었다. 하지만 이 경우, 불편성과 보안 문제가 크게 발생하였다. 특히 현재는 **`Internet Explorer`**가 공식 지원을 종료하고 Edge로 전환되면서 권장 되고있지 않다.

그래서 우리는 또 다른 방법을 고려해야만 한다.  
그 방법중 하나가 Windows 레지스트리를 활용한 방법이다. 

<br>

## 레지스트리와 프로토콜

JavaScript는 웹 브라우저 내에서 실행되기 때문에, 보안상의 이유로 로컬 시스템의 레지스트리와 상호작용하거나 .EXE 파일을 실행하는 것과 같은 작업을 직접 수행할 수 없다.  
하지만 특정 프로토콜을 통해 로컬 시스템에서 응용 프로그램을 실행할 수 있다.

해당 작업에 대한 예시를 본문에서는 진행하며, .EXE를 실행하는 과정을 다룰 것이다.    
동작은 다음과 같다.

**1. 프로토콜 핸들러 등록**

    - 로컬 시스템의 레지스트리를 수정하여 특정 프로토콜을 등록.
    - 프로토콜이 호출될 때 특정 .EXE 파일이 실행되도록 설정.

**2. 웹 페이지에서 프로토콜 호출**

    - 웹 페이지에서 해당 프로토콜을 사용하여 등록된 .EXE 파일을 실행

<br>

## 기본

### ***프로토콜 핸들러 등록***

먼저 레지스트리에 프로토콜을 등록한다.  
아래와 같이 실행할 .exe 경로를 명시하여 .reg 파일을 작성하여 실행한다.

***registry 파일***
```
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\myapp]
@="My Custom Protocol"

[HKEY_CLASSES_ROOT\myapp\shell]
@=""

[HKEY_CLASSES_ROOT\myapp\shell\open]
@=""

[HKEY_CLASSES_ROOT\myapp\shell\open\command]
@="\"C:\\Path\\To\\YourApp.exe\" \"%1\""
```

<br>

레지스트리 파일을 실행하면 레지스트리에 ***myApp*** 이라는 프로토콜이 등록된다.

![Alt text](/assets/images/240702_1.JPG)

<br>

### ***웹 페이지에서 프로토콜 호출***

이후 웹 브라우저의 JS를 통해 프로토콜을 통한 EXE 실행을 할 수 있다.

***html 파일***
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Custom Protocol Example</title>
</head>
<body>
    <a href="myapp://example">Run My App</a>
</body>
</html>
```

<br>

링크 버튼을 클릭하면 .exe 실행 팝업이 발생하고, 열기를 클릭하여 .exe 파일을 실행한다.

![Alt text](/assets/images/240702_2.JPG)

<br>

## 고급

### ***파라미터 입력***

.exe 파일을 단순 실행하는 것이 아닌, 파라미터를 전달할 수도 있다.  
처음 프로토콜을 등록할때 .exe 경로와 ```%1``` 문자를 명시해두었다. 이 심볼 문자를 통해 파라미터를 수신한다.

***registry 파일***
```
[HKEY_CLASSES_ROOT\myapp\shell\open\command]
@="\"C:\\Path\\To\\YourApp.exe\" \"%1\""
```

<br>

따라서 JS에서 호출할 때의 주소가 파라미터가 된다.

***html 파일***
```html
<a href="myapp://{param}">Run My App</a>
```

<br>

### ***다중 파라미터 입력***

파라미터가 1개가 아니랄 여러 파라미터를 입력할 수도 있다.  
이는 모든 파라미터를 담아 .exe를 실행하여 .exe 코드에서 파라미터를 추출할 수도 있으나 .EXE 파일을 조작하기 어려운 경우, 배치파일을 통해 .EXE를 간접적으로 실행하도록 한다.

이렇게 되면, 최초 프로토콜을 통해 .bat 파일이 실행되게된다.

***registry 파일***
```
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\myapp]
@="My Custom Protocol"

[HKEY_CLASSES_ROOT\myapp\shell]
@=""

[HKEY_CLASSES_ROOT\myapp\shell\open]
@=""

[HKEY_CLASSES_ROOT\myapp\shell\open\command]
@="\"C:\\Path\\To\\YourBat.bat\" \"%1\""
```

<br>

***batch 파일***
```bat
@echo off
setlocal

rem Remove the protocol prefix
set "url=%~1"
set "url=%url:myapp://=%"

rem Split the command and arguments
for /f "tokens=1,2 delims=/" %%a in ("%url%") do (
    set "args1=%%a"
    set "args2=%%b"
)

rem Debug: display the parsed command and arguments
echo Argument1: %args1%
echo Argument2: %args2%

rem Execute the appropriate command
start "" "C:\Program Files (x86)\test\testApp.exe" "%args1%" "%args2%"
endlocal
```

<br>

***html 파일***
```html
<a href="myapp://{param1}/{param2}">Run My App</a>
```

<br>

### ***다중 .EXE 실행***

마찬가지로 배치파일을 통해 1개의 프로토콜로 다중 EXE를 실행시킬 수 있다.  
다중 파라미터와 유사하게 파라미터를 구분하여 Command 명령어로 사용한다.

***batch 파일***
```bat
@echo off
setlocal

rem Remove the protocol prefix
set "url=%~1"
set "url=%url:myapp://=%"

rem Split the command and arguments
for /f "tokens=1,2,3 delims=/" %%a in ("%url%") do (
    set "command=%%a"
    set "args1=%%b"
    set "args2=%%c"
)

rem Debug: display the parsed command and arguments
echo Command: %command%
echo Argument1: %args1%
echo Argument2: %args2%

rem Execute the appropriate command
if "%command%" == "start" (
    start "" "C:\Program Files (x86)\test\testApp.exe" "%args1%" "%args2%"
) else if "%command%" == "stop" (
    start "" "C:\Program Files (x86)\test\testAppStop.exe" "%args1%"
) else if "%command%" == "exit" (
    start "" "C:\Program Files (x86)\test\testAppExit.exe"
) 

endlocal
```

<br>

***html 파일***
```html
<a href="myapp://{start}/{param1}/{param2}">Run My App1</a>
<a href="myapp://{stop}/{param2}">Run My App2</a>
<a href="myapp://{exit}">Run My App3</a>
```

<br>

## 정리

위 내용처럼 웹 브라우저 JS에서 프로토콜을 통하여 로컬 Windows .EXE 파일을 실행할 수 있다.  
더 나아가 Bat 파일을 통해 컨트롤러 역할을 수행하여 API 형태로 개발하여 활용할 수도 있다. 

하지만 해당 방법이 보안상, 그리고 서비스 제공 플로우상 적절한 방법이 아닐 수도 있다.  
또한 더 나은 방법이 존재할 수도 있고, 해당 프로세스를 상용 서비스에 적용하려면 많은 검토가 필요할 수 있다.  

따라서 웹 환경에서 Windows 로컬환경에 접근하기위한 여러가지 방법 중 한 가지 방법으로 참고해주기를 바란다. 

<br>

## 관련 자료


