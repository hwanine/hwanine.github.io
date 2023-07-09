---
toc: true
toc_sticky: true
categories:
  - c++
title: \[C++\] User/System 권한으로 레지스트리(Registry) 값 읽어오기
tags: [c++, registry]
excerpt: "User권한으로 레지스트리 값 읽어오는 방법과, 시스템 권한으로 실행시 레지스트리 값 읽어오는 방법에 대하여"
---

# Registry 값 읽어오기

## User 권한 방식

아래 코드는 일반적으로 알려져있는 CString으로 레지스트리 읽는 방법이다.

```c++
HKEY hKey = NULL;
DWORD dwDisposition;
	
if (RegCreateKeyEx(HKEY_CURRENT_USER,
    _T("SOFTWARE\\Dev"), 0,
    NULL, REG_OPTION_NON_VOLATILE,
    KEY_WRITE, NULL,
    &hKey, &dwDisposition) == ERROR_SUCCESS)
{
    // CString 값 저장
    CString sValue1 = _T("Value1");
    CString sData = _T("data"); 
    if (RegSetValueEx(hKey, sValue1, 0, REG_SZ, 
        (CONST BYTE*)(LPCTSTR)sData, (DWORD)(_tcslen(sData) + 1) * sizeof(TCHAR)) == ERROR_SUCCESS)
    {
        // 성공
    }
}

RegCloseKey(hKey);
```

위의 방법으로 레지스트리 값을 읽으면  CString으로 잘 읽어진다. 물론 다른 자료형으로 읽는 방법도 가능하다.

<br>

## 문제점

근데 이 방법으로 윈도우 DLL으로 사용하기엔 한가지 문제가 있었다.

만약 DLL 로드를 윈도우 서비스에서 할 경우, 서비스 권한을 유저 권한으로 변경해주지 않으면 System 계정으로 레지스트리에 접근을하여 정상적으로 읽어오지 못한다.

위 코드를 보면 해당 키를 활용해서 값을 읽어온다.

```c++
HKEY_CURRENT_USER
```

이 권한 키를 다음의 키로 변경하여 접근해야한다.

```C++
HKEY_USERS
```

<br>

## System 권한 방식

아래 코드는 윈도우 서비스에 Local System 계정으로 등록되어있어도 정상적으로 레지스트리를 읽어올 수 있다.

방식은 다음과 같다.

1. 실행된 프로세스 PID를 찾는다.
2. 프로세스 ID로 실행자 SID를 찾는다.
3. 실행자 SID로 레지스트리를 조회한다. 

```c++
 // Process ID를 Process Handle로 변경한다.
 DWORD dwPid = GetPIDFromShortFileName("explorer.exe");
 HANDLE hProcess = OpenProcess(PROCESS_QUERY_INFORMATION | PROCESS_VM_READ  , FALSE, dwPid);

 // Process Handle이 NULL이면, Open Failed 에러
 if (hProcess == NULL)
 {
  return;
 }

 // 프로세서의 실행자 SID를 찾는다.
 // 서비스 프로그램은 Local System 계정으로 로그인하였으므로, HKEY_CURRENT_USER로 레지스트리를
 // Open할 경우 App와 다른 레지스트리 키로 접근이 된다.
 // 그러므로, Process Token에서, SID를 확보해서 레지스트리를 직접 찾아 가야 한다.

 HANDLE hTProcess;
 DWORD dwSize;

 // Process 핸들로 토큰을 얻는다. (Vista이므로, 될수 있으면 권한은 낮게... QUERY 권한만으로 접근)
 if (OpenProcessToken(hProcess, TOKEN_QUERY, &hTProcess) == FALSE)
 {
  CloseHandle(hProcess);
  return;
 }

 // NULL 값으로 토큰 정보를 요청하여,  토큰 정보가 저장될 버퍼의 크기를 받아온다.
 GetTokenInformation(hTProcess, TokenUser, NULL, 0, &dwSize);

 TOKEN_USER *sidUser = (TOKEN_USER *)malloc(dwSize);
 if (GetTokenInformation(hTProcess, TokenUser, sidUser, dwSize, &dwSize) == 0)
 {

  free(sidUser);
  CloseHandle(hTProcess);
  CloseHandle(hProcess);

  return;
 }

 // SID는 문자열이 아니고 구조체형이다.
 // 문자형 SID로 변경한다.
 LPTSTR pszSid;
 ConvertSidToStringSid(sidUser[0].User.Sid, &pszSid);
 free(sidUser);
 CloseHandle(hTProcess);

 
 CString strUserSID = pszSid;
 LocalFree(pszSid);

 // 레지스트리 오픈
 strUserSID += L"\\Software\\microsoft\\windows\\currentversion\\run";
 if( RegOpenKeyEx( HKEY_USERS, strUserSID, 0, KEY_READ, &hTestKey) == ERROR_SUCCESS )
 {
        //여기서 부턴 그냥 원래 하던데로 레지값 읽으면 됨
 }
 ```

해당 샘플코드를 커스터마이징하여 사용하면 DLL 로드하는 프로세스가 시스템 계정이어도 정상적으로 현재 유저 계정의 레지스트리를 읽어올 수 있다.

<br>

- 참고자료
    - https://dev-drive.tistory.com/2
    - https://jethro.tistory.com/entry/MFC-%ED%8E%8C-System%EA%B6%8C%ED%95%9C%EC%9C%BC%EB%A1%9C-%ED%98%84%EC%9E%AC-%EC%82%AC%EC%9A%A9%EC%9E%90%EC%9D%98-%EB%A0%88%EC%A7%80%EC%8A%A4%ED%8A%B8%EB%A6%AC-%EA%B2%BD%EB%A1%9C-%EC%A0%91%EA%B7%BC%ED%95%98%EA%B8%B0