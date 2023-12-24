---
toc: true
toc_sticky: true
categories:
  - C++
title: [C++] JNI 환경에서 C++에서 전송한 문자열이 깨지는 문제 컨버팅 방법
tags: [jni]
excerpt: "JNI 환경에서 C++에서 전송한 문자열이 깨지는 문제를 해결한 코드 보관용"
---

# JNI 환경에서 C++에서 전송한 한글 문자열이 깨지는 문제

## 이슈 분석

JNI를 통해 C++에서 문자열을 jstring으로 인스턴스를 생성하여 전송시 Java쪽에서 한글이 깨지는 문제가 있다.  
이 문제의 원인은 Java에서는 UTF-8을 사용하지만 C++에서는 그렇지 못했기 때문이다.

그래서 문자열을 컨버팅 하는 처리가 필요하다.

## 코드 처리

- 선언 함수
```c++
char* JByteArray2CStr(JNIEnv * env, jbyteArray javaBytes);
jbyteArray CStr2JByteArray(JNIEnv * env, const char* nativeStr);
jbyteArray JavaGetBytes(JNIEnv * env, jstring str);
jbyteArray JavaGetBytesEncoding(JNIEnv * env, jstring str, const char* encoding);
jstring JavaNewString(JNIEnv * env, jbyteArray javaBytes);
jstring JavaNewStringEncoding(JNIEnv * env, jbyteArray javaBytes, const char* encoding);
jstring JavaNewStringChar(JNIEnv * env, const char* nativeStr);
```

<br>

- 구현 함수
```c++
static jclass class_String;
static jmethodID mid_getBytes, mid_getBytesEncoding;
static jmethodID mid_newString, mid_newStringEncoding;


/// <summary>
/// JAVA <-> C++ ENCODING
/// </summary>


char* JByteArray2CStr(JNIEnv* env, jbyteArray javaBytes) {
	size_t len = env->GetArrayLength(javaBytes);
	jbyte* nativeBytes = env->GetByteArrayElements(javaBytes, 0);
	char* nativeStr = (char*)malloc(len + 1);
	strncpy(nativeStr, (const char*)nativeBytes, len);
	nativeStr[len] = '\0';
	env->ReleaseByteArrayElements(javaBytes, nativeBytes, JNI_ABORT);

	return nativeStr;
}

jbyteArray CStr2JByteArray(JNIEnv* env, const char* nativeStr) {

	jbyteArray javaBytes;
	int len = strlen(nativeStr);
	javaBytes = env->NewByteArray(len);
	env->SetByteArrayRegion(javaBytes, 0, len, (jbyte*)nativeStr);

	return javaBytes;
}

jbyteArray JavaGetBytes(JNIEnv* env, jstring str) {

	if (mid_getBytes == 0)
	{
		if (class_String == 0)
		{
			jclass cls = env->FindClass("java/lang/String");

			if (cls == 0)
			{
				return 0;
			}
			class_String = (jclass)env->NewGlobalRef(cls);
			env->DeleteLocalRef(cls);

			if (class_String == 0)
			{
				return 0;
			}
		}
		mid_getBytes = env->GetMethodID(class_String, "getBytes", "()[B");

		if (mid_getBytes == 0)
		{
			return 0;
		}
	}

	return (jbyteArray)env->CallObjectMethod(str, mid_getBytes);
}

jbyteArray JavaGetBytesEncoding(
	JNIEnv* env,
	jstring str,
	const char* encoding
) {

	if (mid_getBytesEncoding == 0)
	{
		if (class_String == 0)
		{
			jclass cls = env->FindClass("java/lang/String");
			if (cls == 0)
			{
				return 0;
			}
			class_String = (jclass)env->NewGlobalRef(cls);
			env->DeleteLocalRef(cls);

			if (class_String == 0)
			{
				return 0;
			}
		}
		mid_getBytesEncoding = env->GetMethodID(class_String, "getBytes", "(Ljava/lang/String;)[B");

		if (mid_getBytesEncoding == 0)
		{
			return 0;
		}
	}

	jstring jstr = env->NewStringUTF(encoding);
	jbyteArray retArray = (jbyteArray)env->CallObjectMethod(
		str,
		mid_getBytesEncoding,
		jstr
	);
	env->DeleteLocalRef(jstr);

	return retArray;

}

jstring JavaNewString(JNIEnv* env, jbyteArray javaBytes) {

	if (mid_newString == 0)
	{
		if (class_String == 0)
		{
			jclass cls = env->FindClass("java/lang/String");
			if (cls == 0)
			{
				return 0;
			}

			class_String = (jclass)env->NewGlobalRef(cls);
			env->DeleteLocalRef(cls);
			if (class_String == 0)
			{
				return 0;
			}
		}
		mid_newString = env->GetMethodID(class_String, "<init>", "([B)V");

		if (mid_newString == 0)
		{
			return 0;
		}
	}
	return (jstring)env->NewObject(class_String, mid_newString, javaBytes);
}

jstring JavaNewStringEncoding(
	JNIEnv* env,
	jbyteArray javaBytes,
	const char* encoding
) {
	int len;
	jstring str;
	if (mid_newString == 0)
	{
		if (class_String == 0)
		{
			jclass cls = env->FindClass("java/lang/String");
			if (cls == 0)
			{
				return 0;
			}

			class_String = (jclass)env->NewGlobalRef(cls);
			env->DeleteLocalRef(cls);
			if (class_String == 0)
			{
				return 0;
			}
		}
		mid_newString = env->GetMethodID(class_String, "<init>", "([BLjava/lang/String;)V");

		if (mid_newString == 0)
		{
			return 0;
		}
	}
	jstring jstr = env->NewStringUTF(encoding);
	str = (jstring)env->NewObject(class_String, mid_newString, javaBytes, jstr);
	env->DeleteLocalRef(jstr);
	return str;
}

jstring JavaNewStringChar(JNIEnv* env, const char* nativeStr) {
	jbyteArray byteArray = CStr2JByteArray(env, nativeStr);
	jstring jstr = JavaNewStringEncoding(env, byteArray, "euc-kr");
	env->DeleteLocalRef(byteArray);
	return jstr;
}
```

<br>

- 호출부

이제 코드를 변환하여 문자열 송수신시 결과가 잘 출력된다.

```c++
char* name = "홍길동";
jstring jstr = JavaNewStringChar(jniEnv, name);
return jstr;
```

<br>

## 참고자료

- https://micropilot.tistory.com/1638

