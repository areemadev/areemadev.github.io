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
const decodedValue = decodeURIComponent(escape(value));
cookies.set(key, decodedValue);
```

