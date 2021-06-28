---
date: 2021-06-28 16:16:32
layout: post
title: Nuxt.js - fetch vs asyncData
subtitle: ""
description: fetch와 asyncData의 다른 점
image: /assets/img/uploads/new-fetch-lifecycle-hooks.png
category: dev
tags:
  - dev
author: jake
paginate: false
---


> 참고: [Data Fetching](https://ko.nuxtjs.org/docs/2.x/features/data-fetching)

참고로 둘다 ssr / csr시에 호출된다. 

# fetch

> nuxt 2.12 이전의 fetch hook과 헷갈리지 말자!

* 컴포넌트 인스턴스가 생성된(created)후 호출된다. (after created hook)
* `this`를 사용할 수 있다.
* 어느 컴포넌트에서나 fetch hook을 사용할 수 있다.
* fetchOnServer, fetchDelay 등 옵션 사용이 가능하다.
* $fetchState 값으로 pending, error을 표시할 수 있다.
* $fetch() 메서드로 수동으로 fetch 함수를 호출할 수 있다.

```javascript
<template>
  <p v-if="$fetchState.pending">Fetching mountains...</p>
  <p v-else-if="$fetchState.error">An error occurred :(</p>
  <div v-else>
    <h1>Nuxt Mountains</h1>
    <ul>
      <li v-for="mountain of mountains">{{ mountain.title }}</li>
    </ul>
    <button @click="$fetch">Refresh</button>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        mountains: []
      }
    },
    async fetch() {
      this.mountains = await fetch(
        'https://api.nuxtjs.dev/mountains'
      ).then(res => res.json())
    }
  }
</script>
```

# asyncData

컴포넌트 인스턴스가 생성되기 전에 호출되며 결과값을 컴포넌트 데이터와 병합한다. (before beforeCreate hook)

* `page` 컴포넌트에서만 사용할 수 있다.
* `this`를 통해 컴포넌트 인스턴스에 접근이 불가능하다.
* 첫번째 인자로 [context](https://ko.nuxtjs.org/docs/2.x/concepts/context-helpers) 객체를 받는다. 
* asyncData의 결과 값은 데이터와 병합된다.
* loading placeholder를 표시할 수 없고, 결과가 반환되기 전까지 route navitaition이 block된다.
* 만약 오류가 발생한다면 에러 페이지를 보여준다.

```javascript
export default {
  data () {
    return { project: 'default' }
  },
  asyncData (context) {
    return { project: 'nuxt' }
  },
  mounted() {
    console.log(this.project) // nuxt
  }
}


```

# 요약

* `asyncData`: 컴포넌트가 생성되기 전에, 컴포넌트 데이터를 구성하는데 목적. Vuex store에 접근하거나 해당 컴포넌트에 접근(this)할 필요가 없는 경우 사용. 
* `fetch`: api를 통해 해당 컴포넌트에 미리 정의된 데이터 속성을 사용해야하는 경우 사용.