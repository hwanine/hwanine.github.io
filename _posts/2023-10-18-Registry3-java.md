---
toc: true
toc_sticky: true
categories:
  - java
title: \[Java\] User/System 권한으로 레지스트리(Registry) 값 읽어오기
tags: [Java, registry]
excerpt: "Java에서의 User권한으로 레지스트리 값 읽어오는 방법과, 시스템 권한으로 실행시 레지스트리 값 읽어오는 방법에 대하여"
---

# Registry 값 읽어오기

## Java에서 레지스트리 값 읽기

이전 포스팅에서 C++과 C#에서 레지스트리 값을 읽어오는 과정에 대해 다루었다.  
사실 자바가 대부분 Spring 구조의 웹 백엔드 언어로 사용되다보니 윈도우 서버나 윈도우 애플리케이션이 아니면 해당 방법에 대한 수요는 거의 없을 것이다.

그래서 실제로 구글 서치시 윈도우 레지스트리값을 조작하는 방법에 대한 글들은 거의 찾아볼 수 없었다.

이번 포스팅에는 해당 JAVA 레지스트리를 읽는 방법과 기존 C++/C#과 마찬가지로 관리자 권한으로 레지스트리 값을 읽는 방법도 다뤄보겠다.

아래 C++과 C#의 레지스트리 참조 글은 참고하면 된다.

- https://hwanine.github.io/c++/Registry-c++/
- https://hwanine.github.io/c#/Registry-c#/

<br>

## User 권한 방식

우선 레지스트리를 읽고 쓰는 방법중 대다수는 관련 라이브러리로 제공되고있다.  
해당 포스팅에서는 라이브러리를 사용하는 방법이 아닌, 직접 윈도우 명령어를 활용하여 동작시킬 것이다.

자바에서 `getRuntime` 메소드를 통해 레지스트리 값을 읽고 쓰는 Windows 명령을 사용하는 방법이다.

아래 코드를 통해 레지스트리 값을 읽고 저장할 수 있다.  

```java
// 레지스트리 값 가져오기
private String getRegistryValue(String path, String key) throws Exception {
	String result = null;
	BufferedInputStream in = null;
	
	String command = "reg query " + "\"" + path + "\" /v " + key;
	
	try { 
		Process process = Runtime.getRuntime().exec(command);
		in = new BufferedInputStream(process.getInputStream());
		
		// Data가 들어오는 동안 0.2초정도 대기
		try {
			Thread.sleep(200);
		} catch (InterruptedException e) {
			throw e;
		}
		
		byte[] buff = new byte[in.available()];
		in.read(buff);
		
		result = new String(buff);
	} catch(IOException e) {
		throw e;
	} finally {
		try { in.close(); } catch(IOException e) { throw e; }
	}
	
	int index = result.indexOf("REG_SZ");
	if (index < 0) return null;

	return result.substring(index + "REG_SZ".length()).trim();
}


// 레지스트리 값 저장하기
private void setRegistryValue(String path, String key, String value) throws Exception {
	
	String command = "reg add " + path + " /t REG_SZ /v \"" + key + "\" /d " + key + " /f";

	try { 
		Process process = Runtime.getRuntime().exec(command);
        process.waitFor();	
	} catch(Exception e) {
		throw e;
	} 
}

// 값 불러오기
String value = getRegistryValue("HKEY_CURRENT_USER\\Software\\TestCompany\\TestProgram", "Version");
String input = "testInput"

// 값 저장하기
setRegistryValue("HKEY_CURRENT_USER\\Software\\TestCompany\\TestProgram", "Version", testInput);
```

위의 방법으로 레지스트리 값을 읽고 쓸 수 있다. 물론 다른 자료형으로 읽는 방법도 가능하다.

<br>

## 문제점

근데 이 방법으로 사용하면 마찬가지로 시스템 권한일 경우 레지스트리를 읽어오지못한다.
즉, JAVA 프로그램이 관리자 권한으로 실행될 경우, 레지스트리가 등록된 계정에 대한 값을 찾지 못한다.  
마찬가지로 윈도우 서비스로 자바 프로그램이 실행될 경우, 서비스 권한을 유저 권한으로 변경해주지 않으면 System 계정으로 레지스트리에 접근을하여 정상적으로 읽어오지 못한다.

<br>

## System 권한 방식

아래 코드는 윈도우 서비스에 Local System 계정으로 등록되어있어도 정상적으로 레지스트리를 읽어올 수 있다.

여기 소개하는 방식은 C++/C# 방식과 유사하지만 약간 다르다

1. 실행된 프로세스 PID와 그 프로세스의 OWNER를 찾는다.
2. 계정명 OWNER로 SID를 찾는다.
3. 실행자 SID로 레지스트리를 조회한다. 

```java
	// 프로세스 ID, OWNER 조회
    private static String[] getPid(String processName) throws IOException, InterruptedException {
      String command = "tasklist /FI \"IMAGENAME eq " + processName + "\" /FO LIST /V";
  	  ProcessBuilder pb2 = new ProcessBuilder("cmd.exe","/C", command);
  	  Process myproc = pb2.start();
  	  
  	  InputStream errorOutput = new BufferedInputStream(myproc.getErrorStream(), 1000000);
  	  BufferedReader reader =	new BufferedReader(new InputStreamReader(myproc.getInputStream(), "MS949"));
  	  int ch;
  	  String[] keyNames = {"PID:", "사용자 이름:"};
  	  String[] result = {"", ""};
  	  String processlist = "";
  	  while ((ch = reader.read()) != -1) {
  	      processlist = processlist + (char) ch;
  	  }
  	  
  	  String []processarr = processlist.split("\n");
  	  String processtemp = "";
  	  for( int j = 0; j< processarr.length;j++){ 	   
  	   processtemp = processarr[j];
  	   if(processtemp.startsWith(keyNames[0])) {
  		 result[0] = processtemp.substring(keyNames[0].length(), processtemp.length()-1).trim();
  	   }
  	   
  	   if(processtemp.startsWith(keyNames[1])) {
  		 result[1] = processtemp.substring(keyNames[1].length(), processtemp.length()-1).trim();
  	   }
  	   
  	   if(result[0] != "" && result[1] != "") {
  		   break;
  	   }
  	  }
  	  int exitCode = myproc.waitFor(); 
  	  return result;
  }

 // 사용자 SID 조회
  private static String getSid(String userName) throws IOException, InterruptedException {
	  userName = userName.replace("\\", "\\\\");
	  String command = "wmic useraccount where Caption='" + userName + "' get sid";
  	  ProcessBuilder pb2 = new ProcessBuilder("cmd.exe","/C", command);
 	  Process myproc = pb2.start();
 	  
 	  InputStream errorOutput = new BufferedInputStream(myproc.getErrorStream(), 1000000);
 	  BufferedReader reader =	new BufferedReader(new InputStreamReader(myproc.getInputStream(), "MS949"));
 	  int ch;
 	  String key = "SID";
 	  String result = "";
 	  String processlist = "";
 	  while ((ch = reader.read()) != -1) {
 	      processlist = processlist + (char) ch;
 	  }
 	  
 	  String []processarr = processlist.split("\n");
 	  String processtemp = "";
 	  for( int j = 0; j< processarr.length;j++){
 	   
 	   processtemp = processarr[j];
 	   if(processtemp.contains(key)) {
 		  result = processarr[j+1].trim();
 		  break;
 	   }
 	  }
 	  int exitCode = myproc.waitFor(); 
 	  return result;
  }

  // 레지스트리 값 가져오기
private String getRegistryValue(String path, String key) throws Exception {
	String result = null;
	BufferedInputStream in = null;
	
	String command = "reg query " + "\"" + path + "\" /v " + key;
	
	try { 
		Process process = Runtime.getRuntime().exec(command);
		in = new BufferedInputStream(process.getInputStream());
		
		// Data가 들어오는 동안 0.2초정도 대기
		try {
			Thread.sleep(200);
		} catch (InterruptedException e) {
			throw e;
		}
		
		byte[] buff = new byte[in.available()];
		in.read(buff);
		
		result = new String(buff);
	} catch(IOException e) {
		throw e;
	} finally {
		try { in.close(); } catch(IOException e) { throw e; }
	}
	
	int index = result.indexOf("REG_SZ");
	if (index < 0) return null;

	return result.substring(index + "REG_SZ".length()).trim();
}


// 레지스트리 값 저장하기
private void setRegistryValue(String path, String key, String value) throws Exception {
	
	String command = "reg add " + path + " /t REG_SZ /v \"" + key + "\" /d " + key + " /f";

	try { 
		Process process = Runtime.getRuntime().exec(command);
        process.waitFor();	
	} catch(Exception e) {
		throw e;
	} 
}

// 값 불러오기
String[] result = getPid("explorer.exe");
String sid = getSid(result[1]);
String version = getRegistryValue("HKU\\" + sid + "\\SOFTWARE\\TestCompany\\TestProgram", "version");

// 값 저장하기
setRegistryValue("HKEY_CURRENT_USER\\Software\\TestCompany\\TestProgram", "Version", testInput);
```

java에서는 이렇듯 windows의 명령프롬포트를 실행시켜 명령어를 통해 정보를 조합하여 각 사용자 레지스트리에 접근할 수 있다.  
관리자 프로그램에서 사용자 레지스트리를 가져오는 다른 오픈 라이브러리의 기능도 있고 위에서 설명한 로직과는 다른 방법이 또 많이 있을 것이다. 

하지만 사실 윈도우즈기반 자바 프로그램에서 관리자로 레지스트리에 접근할 일이 잘 없다보니 서치를 해도 잘 나오지 않아서 직접 구현 해보았다.

해당 샘플코드를 커스터마이징하여 사용하면 JAVA 프로젝트에서 레지스트리 값을 조작할 경우나 다른 특별한 경우 프로세스가 시스템 계정이어도 정상적으로 현재 유저 계정의 레지스트리를 읽어올 수 있다.

파싱 데이터 처리를 위한 코드들도 있고 단순히 보기만해선 이해가 안될 수도 있으니 직접 디버깅해서 활용하면 좋을듯 하다.  

<br>
