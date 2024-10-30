---
title: "[ReactNative] Called object type 'facebook::flipper... 에러"
---

### 문제

react-native 빌드 시 아래 에러가 발생한다.

<span style="color:red">Called object type 'facebook::flipper::SocketCertificateProvider' (aka 'int') is not a function or function pointer</span>

### 해결

임시 방편으로 **FlipperTransportTypes.h** 파일을 찾아 아래 include 추가해 주면 된다.

```objective-c
#include <string>
#include <functional> <--- 추가
```

### 참고 링크

https://github.com/facebook/react-native/issues/43335
