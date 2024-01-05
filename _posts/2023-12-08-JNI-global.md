---
toc: true
toc_sticky: true
categories:
  - C++
title: \[C++\] JNI 연동시 jvm 인스턴스 전역으로 관리하기 (멀티쓰레드 문제해결)
tags: [jni]
excerpt: "JNI 연동시 멀티쓰레드에서 jvm 인스턴스에 접근이 불가능하기 때문에 전역으로 관리하는 방안"
---

# JNI jvm 전역 인스턴스 관리

## 이슈 분석

JNI 연동할 때 멀티쓰레드는 동작이 불가능했다.  
java에서 넘어온 jvm 파라미터 변수는 호출 쓰레드에서 사용 후 소멸되기 때문이었다.  
따라서 jvm 변수를 전역으로 관리해주어야 했는데 그러기 위해선 Onload 함수를 사용한다.

```c++
JavaVM* g_jvm;
JNIEXPORT jint JNICALL JNI_OnLoad(JavaVM* vm, void* reserved) {
	g_jvm = vm;
	JNIEnv* env;
	JavaVMAttachArgs args;
	args.version = JNI_VERSION_1_8; // choose your JNI version
	args.name = NULL; // you might want to give the java thread a name
	args.group = NULL; // you might want to assign the java thread to a ThreadGroup
	
	jint res = g_jvm->AttachCurrentThread((void**)&env, &args);
	if (res != JNI_OK) {
		//g_engine_controller->WriteLogText(LOG_LV_0, "JNI Load Failed!!");
		return 0;
	}

	g_jvm->DetachCurrentThread();
	return JNI_VERSION_1_8;
}
```

<br>

이후 쓰레드 함수 호출시 다음과 같이 처리해준다.
```c++
unsigned __stdcall Thread_RunTest(void* pParam)
{
    // 사용시
    JNIEnv* jniEnv;
    JavaVMAttachArgs args;
    args.version = JNI_VERSION_1_8; // choose your JNI version
    args.name = NULL; // you might want to give the java thread a name
    args.group = NULL; // you might want to assign the java thread to a ThreadGroup
    g_jvm->AttachCurrentThread((void**)&jniEnv, &args);

    // 종료시
    g_jvm->DetachCurrentThread();
}
```

<br>

이렇게 구성하면 멀티쓰레드로 C++ 내 코드가 동작하여도 전역 JVM을 통해 리소스 접근이 가능하여 다양한 처리가 가능해진다.

## 참고자료

- [https://planek.tistory.com/44](https://planek.tistory.com/44)
