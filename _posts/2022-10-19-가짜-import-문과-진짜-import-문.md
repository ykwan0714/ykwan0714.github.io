---
date: 2022-10-19 10:30:32
layout: post
title: 가짜 import 문과 진짜 import 문
subtitle: ""
description: CommonJS와 ESM
image: /assets/img/uploads/logo.png
category: dev
tags:
  - til
  - dev
author: jake
paginate: false
---
>

우리의 import 문은 왜 이상할까? 올바른 Import 문은 무엇인가?

```html
<!-- 전역 jQuery 객체를 정의 -->
<script src="https://cdn.com/jquery.js"></script>
<!-- 전역 lodash 객체를 정의 -->
<script src="https://cdn.com/lodash.js"></script>
<!-- 전역 객체를 참조하여 사용 -->
<script>
  jQuery(document).ready(function() {
	  lodash.get(obj, 'foo')
  })
</script>
```

모듈 개념이 없던 Javascript에선 라이브러리를 사용하려면 스크립트 태그를 사용하였고, 해당 라이브러리가 전역 객체에 할당되어 사용할 수 있었다.



# CommonJS 모듈 시스템

```jsx
const jQuery = require('jQuery')
const lodash = require('lodash')

jQuery(document).ready(function() {
	lodash.get(obj, 'foo')
})
```

CommonJS 모듈 시스템이 도입되면서, `require`함수 이용하여 함수나 변수들을 손쉽게 외부에 노출하거나 가져올 수 있게 되었다.

```jsx
// add.js
export.add = function(a, b) {
	return a + b
}

const { add } = require('./add.js')
console.log(add(1, 2))
```

CommonJS의 모듈 시스템은 파일 단위의 개발과 수백, 수천 개 js 파일로 분리 가능케 했다. 라이브러리 함수들도 손쉽게 재사용이 가능하다. 이러한 장점 덕분에 node.js는 대부분 CommonJS 모듈 시스템을 사용한다.

단점으론 언어 표준이 아니기 때문에 CommonJS 환경이 아닌 곳(웹 브라우저)에선 동작하지 않는다.

```jsx
// add.js
export.add = function(a, b) {
	return a + b
}

const { add } = require('./add.js')
console.log(add(1, 2))
```

또한 정적 분석이 어렵다.`require`는 함수이기에 조건 적으로 호출하거나, 동적으로 호출하는 것이 가능하다. 이런 동작들은 어떤 코드가 어떤 코드를 참조하는지 컴파일타임에서 분석하는 것을 어렵게 만든다. 브라우저의 성능을 위해 실제 사용하는 코드만 사용하는 트리 쉐이킹등은 CommonJS에서 어렵다.

```jsx
const defaultRequire = global.require
const myRequire = (request) => {
	...
}

global.require = myRequire
```

require를 재정의 할 수 있기 때문에 문제점을 야기할 수 있다.

```jsx
let isInitialized = false

export.initialize = async function initialize() {
	if (isInitialized) {
		throw new Error('이미 initialize 되었습니다.')
	}

	await connectToDB()
	isInitialized = true
}

export.readFromDB = async function readFromDB(...) {
	if (!isInitialized) {
		throw new Error('먼저 initialize를 호출하세요..')
	}
	...
}
```

비동기 모듈을 만들 수 없다. 초기화를 예로 들자면 해당 모듈을 사용하려고 할 경우 초기화 여부를 매번 확인해야한다. (top-level await not support)

여기까지 CommonJS에 대해 간략히 알아봤다. 하지만 우리는 대부분 `require`대신 `import/export`를 사용한다. 이걸 가능케 하는 것은 babel과 같은 **transpiler** 덕분이다.

## 가짜 import의 비밀 - babel

```jsx
import React from 'react'
// babel을 거치면...
const React = require('react')
```

우리가 `import`키워드를 사용하게 되도 babel을 거치면 `require`문으로 변환이 된다. typescript 컴파일러에서도 비슷한 형태로 구동이 된다. 이유는 Node.js가 CommonJS 기반이기 때문이다.

# ECMAScript Modules (ESM)

```jsx
// add.js
export function add(a, b) {
	return a + b
}

import { add } from './add.js'
console.log(add(1, 2))
```

CommonJS의 단점을 극복하고자 표준인 ECMAScript Modules(ESM)이 등장하게 되었다. 진짜 import문의 정체이다.

```jsx
// 틀린 코드
if (SOME_CONDITION) {
	import React from 'react'
}

// 틀린 코드
import Something from SOME_CONDITION ? 'foo' : 'bar'

// 틀린 코드, keyword이기 때문에 저장할 수 없다.
const myImport = import
myImport React from 'react
```

쉬운 정적분석이 가능하다. 조건이나 동적으로 import를 할 수 없기에 해당 javascript 파일을 어떤 파일을 참조하고 있는지 쉽게 확인이 가능

```jsx
const db = await connectDB()

export async function readFromDB() {
	await db.read()
}

export async function writeToDB() {
	await db.write()
}
```

쉽게 비동기 모듈을 만들 수 있다. [Top-level await](https://v8.dev/features/top-level-await)

ESM은 언어표준이기 때문에 Node.js 뿐만 아니라 브라우저, deno등에서도 쉽게 사용할 수 있다.

![](/assets/img/uploads/cjs-esm.png)

commonJS 모듈은 import 문으로 가져올수 있다. ESM 모듈은 require를 통해 가져올 수 없다.

![](/assets/img/uploads/cjs-esm2.png)

![](/assets/img/uploads/cjs-esm3.png)

## Node.js에서의 ESM 규칙

### pakage.json - tpye: module

해당 패키지는 ESM 방식으로 동작한다는 것을 의미한다.

```jsx
// package.json
{
	...
	"type": "module", // default 값은 commonjs
	...
}
```

.js의 파일은 가장 가까운 package.json 설정을 따른다.

### 확장자

`.cjs`는 항상 CommonJS, `.mjs`는 항상 ESM이다.

### Node.js의 require 동작

```jsx
const { Component } = require('./MyComponent')
/*
./MyComponent
./MyComponent.js
./MyComponent.node
./MyComponent/index.js
...
*/
```

node.js의 require는 확장자를 생략 하더라도, 다양한 파일 시스템의 파일을 순회하며 찾아준다.

```jsx
// (x)
import { Component } from './MyComponent'
// (o) 확장자 명시
import { Component } from './MyComponent.js' 
```

이러한 순회 동작은 비용이 비쌀 수 밖에 없다. 번들링 속도나 성능이 나빠지는 원인 중 하나이다. node.js 공식 문서에서는 import하는 파일은 반드시 확장자가 명시되어야 한다고 쓰여있다.