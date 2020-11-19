---
toc: true
toc_sticky: true
categories:
  - C#
title: C# - TextBox 바인딩을 했는데 값이 바로 안바뀐다구요?
tags: [C#, WPF, MVVM]
excerpt: "TextBox 바인딩을 하고 UpdateSourceTrigger를 활용하여 업데이트 타이밍을 지정해주어야 합니다."
---

# UpdateSourceTrigger를 활용한 바인딩 업데이트 타이밍 결정 방법

## UpdateSourceTrigger

Binding 클래스의 UpdateSourceTrigger 속성은 바인딩 소스 업데이트 타이밍을 결정하는 값을 가져오거나 설정할때 사용되는 속성입니다.

이것을 xaml에서 사용 하려면 다음과 같이 사용합니다.

```xml
Text="{Binding Text,UpdateSourceTrigger=PropertyChanged}" 
```

<br>

UpdateSourceTrigger의 열거 형식은 다음과 같습니다.

- Default
    - UpdateSourceTrigger의 기본값입니다.  
    - **대부분의 속성의 기본값은 PropertyChanged 이지만, TextBox의 기본값은 LostFocus입니다.** 그래서 바로 업데이트가 안되는 것 입니다..
    - 이 값은 프로그래밍 언어에서 확인하는 방법으로는 `GetMetadata`를 이용하여 메타데이터를 확보한 다음 `DefaultUpdateSourceTrigger` 값을 확인하는 것 입니다.

- PropertyChanged
    - 이 친구는 대부분의 속성의 기본값이며 변경될 때 마다 즉시 업데이트 합니다.

- LostFocus
    - 바인딩 대상 요소가 포커스를 잃을 때마다 바인딩 소스를 업데이트 합니다.

- Explicit
    - UpdateSource 메서드를 호출할 때에만 업데이트 합니다.

```c#
BindingExpression be = textBox1.GetBindingExpression(TextBox.TextProperty);
 be.UpdateSource();
```

<br>

## 결론

위의 내용으로 보았을 때, TextBox의 기본값이 포커스를 잃을 때만 소스가 업데이트 되기 때문에 문제가 발생합니다.

따라서  TextBox Text 속성에 바인딩 할때에 PropertyChanged 속성을 사용하면 문제가 해결됩니다.

(단, 성능의 희생이 약간 필요합니다.)

- 참고자료: <https://ehdrn.tistory.com/289>
