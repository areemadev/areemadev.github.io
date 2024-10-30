---
title: "[React] Cookie 에 한글 저장시 깨질 경우 해결방법"
tags:
  - react
  - cookie
  - 한글깨짐
layout: post
---

# 해결방법

```javascript
import { Cookies } from 'react-cookie';

const cookies = new Cookies();
// 깨진 한글
const value = "ëë©ì¸"; 
try {
	const decodedValue = decodeURIComponent(escape(value));
	cookies.set(key, decodedValue);
} catch(e) {
	// decoding 에 실패했을 경우에 대한 처리
	cookies.set(key, value);
}

```

