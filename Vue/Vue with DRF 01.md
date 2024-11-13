# 메인 페이지 구현

## 게시글 목록 출력

### 개요

- ArticleView 컴포넌트에 ArticleList 컴포넌트와 ArticleListItem 컴포넌트 등록 및 출력하기
- ArticleList와 ArticleListItem은 각각 게시글 출력 담당

1. ArticleView의 route 관련 코드 작성

```jsx
// router/index.js

import ArticleView from '@/views/ArticleView.vue'

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
    {
      path: '/',
      name: 'ArticleView',
      component: ArticleView
    },
  ]
})
```

1. App 컴포넌트에 ArticleView 컴포넌트로 이동하는 RouterLink 작성

```jsx
<!-- App.vue -->

<template>
  <header>
    <nav>
      <RouterLink :to="{ name: 'ArticleView' }">Articles</RouterLink>
    </nav>
  </header>
  <RouterView />
</template>

<script setup>
import { RouterView, RouterLink } from 'vue-router'
</script>
```

1. ArticleView 컴포넌트에 ArticleList 컴포넌트 등록

```jsx
<!-- views/ArticleView.vue -->

<template>
  <div>
    <h1>Article Page</h1>
    <ArticleList />
  </div>
</template>

<script setup>
import ArticleList from '@/components/ArticleList.vue'
</script>

```

1. store에 임시 데이터 articles 배열 작성하기

```jsx
// store/counter.js

export const useCounterStore = defineStore('counter', () => {
  const articles = ref([
	  { id: 1, title: 'Article 1', content: 'Content of article 1' },
	  { id: 2, title: 'Article 2', content: 'Content of article 2' },
  ])
  return { articles }
}, { persist: true })
```

1. ArticleList 컴포넌트에서 게시글 목록 출력
    - store의 articles 데이터 참조
    - v-for 를 활용하여 하위 컴포넌트에서 사용할 article 단일 객체 정보를 props로 전달
    
    ```jsx
    <!-- components/ArticleList.vue -->
    
    <template>
      <div>
        <h3>Article List</h3>
        <ArticleListItem 
          v-for="article in store.articles"
          :key="article.id"
          :article="article"
        />
      </div>
    </template>
    
    <script setup>
    import ArticleListItem from '@/components/ArticleListItem.vue'
    import { useCounterStore } from '@/stores/counter'
    
    const store = useCounterStore()
    </script>
    ```
    
2. ArticleListItem 컴포넌트는 내려 받은 props를 정의 후 출력

```jsx
<!-- components/ArticleListItem.vue -->

<template>
  <div>
    <h5>{{ article.id }}</h5>
    <p>{{ article.title }}</p>
    <p>{{ article.content }}</p>
    <hr>
  </div>
</template>

<script setup>
defineProps({
  article: Object
})
</script>
```

## DRF와의 요청과 응답

💡이제는 임시 데이터가 아닌 DRF 서버에 요청하여 데이터를 응답 받아 store에 저장 후 출력하기

1. DRF 서버로의 AJAX 요청을 위한 axios 설치 및 관련 코드 작성

```bash
#  Vue 서버 종료 -> 설치 -> 서버 재실행
$ npm install axios
```

```jsx
// store/counter.js

import { ref, computed } from 'vue'
import { defineStore } from 'pinia'
import axios from 'axios'

export const useCounterStore = defineStore('counter', () => {
  const articles = ref([])
  const API_URL = 'http://127.0.0.1:8000'
}, { persist: true })
```

1. DRF 서버로 요청을 보내고 응답 데이터를 처리하는 getArticles 함수 작성

```jsx
// store/counter.js

export const useCounterStore = defineStore('counter', () => {
  const articles = ref([])
  const API_URL = 'http://127.0.0.1:8000'

  // DRF로 전체 게시글 요청을 보내고 응답을 받아 articles에 저장하는 함수
  const getArticles = function () {
    axios({
      method: 'get',
      url: `${API_URL}/api/v1/articles/`
    })
      .then((res) => {
	      console.log(res)
        console.log(res.data)
      })
      .catch((err) => {
        console.log(err)
      })
  }
  
  return { articles, API_URL, getArticles }
}, { persist: true })
```

1. ArticleView 컴포넌트가 마운트 될 때 getArticles 함수가 실행되도록 함
    - 해당 컴포넌트가 렌더링 될 때 항상 최신 게시글 목록을 불러오기 위함
    - onMounted 이용해서 DOM을 구성하기 전에 axios로 django 측에 요청 보내서 데이터를 세팅해두기. 이제 DOM이 받은 데이터를 이용해서 화면을 채울 수 있음
    - onMounted 안 쓰면 ref(null)에서 null 값이 채워지기 전에 화면을 그려서 데이터가 없는 빈 페이지가 나오는 상황 발생할 수 있음

```jsx
<!-- views/ArticleView.vue -->

<script setup>
import ArticleList from '@/components/ArticleList.vue'
import { onMounted } from 'vue'
import { useCounterStore } from '@/stores/counter'

const store = useCounterStore()

onMounted(() => {
  // mount 되기전에 store에 있는 전체 게시글 요청 함수를 호출
  store.getArticles()
})
</script>

```

1. Vue와 DRF 서버를 모두 실행한 후 응답 데이터 확인
    
    ⇒ 에러 발생!
    
    DRF 서버 측에서는 문제 없이 응답(200 OK) ⇒ 서버는 응답했으나 브라우저 측에서 거절한 것
    
    브라우저가 거절한 이유
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1a5b2bca-9c7c-4a9d-b553-9a8142d9c9ac/f5662f6c-d3e5-4054-9b20-0c94c0242978/image.png)
    

# CORS Policy

### SOP (Same-origin policy)

- 동일 출처 정책
- 어떤 출처(Origin)에서 불러온 문서나 스크립트가 다른 출처에서 가져온 리소스와 상호 작용하는 것을 제한하는 보안 방식
- 다른 곳에서 가져온 자료는 일단 막기
- 웹 애플리케이션의 도메인이 다른 도메인의 리소스에 접근하는 것을 제어하여 사용자의 개인 정보와 데이터의 보안을 보호하고, 잠재적인 보안 위협을 방지
- 잠재적으로 해로울 수 있는 문서를 분리함으로써 공격받을 수 있는 경로를 줄임

### Origin(출처)

- URL의 Protocol, Host, Port를 모두 포함하여 “출처”라고 부름
- Same Origin 예시
    - 아래 세 영역이 일치하는 경우에만 동일 출처 (same-origin)로 인정
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1a5b2bca-9c7c-4a9d-b553-9a8142d9c9ac/bb313dd1-774d-4382-b84f-37f17ab13c8f/image.png)
    

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1a5b2bca-9c7c-4a9d-b553-9a8142d9c9ac/b35757a5-73dc-4658-9d1d-bb2b267413d3/image.png)

### CORS policy의 등장

- 기본적으로 웹 브라우저는 같은 출처에서만 요청하는 것을 허용하며, 다른 출처로의 요청은 보안상의 이유로 차단됨
    - SOP에 의해 다른 출처의 리소스와 상호작용 하는 것이 기본적으로 제한되기 때문
- 하지만 현대 웹 애플리케이션은 다양한 출처로부터 리소스를 요청하는 경우가 많기 때문에 CORS 정책이 필요하게 되었음
- CORS는 웹 서버가 리소스에 대한 서로 다른 출처 간 접근을 허용하도록 선택할 수 있는 기능 제공

## CORS Policy

- Cross-Origin Resource Sharing
- 교차 출처 리소스 공유

### CORS

- 특정 출처에서 실행 중인 웹 애플리케이션이 다른 출처의 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 체제
- 만약 다른 출처의 리소스를 가져오기 위해서는 이를 제공하는 서버가 브라우저에게 다른 출처지만 접근해도 된다는 사실을 알려야 함

### CORS Policy

- 교차 출처 리소스 공유 정책
- **서버에서 결정**되며, 브라우저가 해당 정책을 확인하여 요청이 허용되는지 여부를 결정
- 다른 출처의 리소스를 불러오려면 그 다른 출처에서 올바른 **CORS header를 포함한 응답을 반환**해야 함
- 서버가 약속된 CORS Header를 포함하여 응답한다면 브라우저는 해당 요청을 허용

✅ **서버에서** CORS Header를 만들어야 한다.

## CORS Headers 설정

- Django 에서는 django-cors-headers 라이브러리 활용
- 손쉽게 응답 객체에 CORS header를 추가해주는 라이브러리

1. 설치(공식문서 보고 [settings.py](http://settings.py) 추가)

```bash
$ pip install django-cors-headers
```

1. CORS를 허용할 Vue 프로젝트의 Domain 등록

```python
# settings.py

CORS_ALLOWED_ORIGINS = [
    'http://127.0.0.1:5173',
    'http://localhost:5173',
]
```

# Article CR 구현

## 전체 게시글 조회

- store에 게시글 목록 데이터 저장

```jsx
// store/counter.js

import { ref, computed } from 'vue'
import { defineStore } from 'pinia'
import axios from 'axios'

export const useCounterStore = defineStore('counter', () => {
  const articles = ref([])
  const API_URL = 'http://127.0.0.1:8000'

  // DRF로 전체 게시글 요청을 보내고 응답을 받아 articles에 저장하는 함수
  const getArticles = function () {
    axios({
      method: 'get',
      url: `${API_URL}/api/v1/articles/`
    })
      .then((res) => {
        // console.log(res.data)
        **articles.value = res.data**
      })
      .catch((err) => {
        console.log(err)
      })
  }
  
  return { articles, API_URL, getArticles }
}, { persist: true })
```

- store에 저장된 게시글 목록 출력 확인
    - pinia - plugin - persistedstate에 의해 브라우저 Local Storage에 저장됨

## 단일 게시글 조회

1. DetailVue 관련 route 작성

```jsx
// router/index.js

import DetailView from '@/views/DetailView.vue'

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
    {
      path: '/',
      name: 'ArticleView',
      component: ArticleView
    },
    {
      path: '/articles/:id',
      name: 'DetailView',
      component: DetailView
    },
   ...
```

1. ArticleListItem에 DetailView 컴포넌트로 가기 위한 RouterLink 작성

```jsx
<!-- components/ArticleListItem.vue -->

<template>
  <div>
    <h5>{{ article.id }}</h5>
    <p>{{ article.title }}</p>
    <p>{{ article.content }}</p>
    <RouterLink 
      :to="{ name: 'DetailView', params: { id: article.id } }"  
    >
      Detail
    </RouterLink>
    <hr>
  </div>
</template>

<script setup>
import { RouterLink } from 'vue-router'

defineProps({
  article: Object
})
</script>
```

1. DetailView가 마운트 될 때 특정 게시글을 조회하는 AJAX 요청 진행, 응답 데이터 저장 후 출력
    - onMounted 이용해서 DOM을 구성하기 전에 axios로 django 측에 요청 보내서 데이터를 세팅해두기. 이제 DOM이 받은 데이터를 이용해서 화면을 채울 수 있음
    - onMounted 안 쓰면 ref(null)에서 null 값이 채워지기 전에 화면을 그려서 데이터가 없는 빈 페이지가 나오는 상황 발생할 수 있음

```jsx
// views/DetailView.vue

import axios from 'axios'
import { onMounted, **ref** } from 'vue'
import { useCounterStore } from '@/stores/counter'
import { useRoute } from 'vue-router'

const store = useCounterStore()
const route = useRoute()
**const article = ref(null)**

// DetailView가 마운트되기전에 DRF로 단일 게시글 조회를 요청 후 응답데이터를 저장
onMounted(() => {
  axios({
    method: 'get',
    url: `${store.API_URL}/api/v1/articles/${route.params.id}/`
  })
    .then((res) => {
      // console.log(res.data)
      **article.value = res.data**
    })
    .catch((err) => {
      console.log(err)
    })
})
```

```jsx
// views/DetailView.vue

<template>
  <div>
    <h1>Detail</h1>
    <div v-if="article">
      <p>게시글 번호 : {{ article.id }}</p>
      <p>제목 : {{ article.title }}</p>
      <p>내용 : {{ article.content }}</p>
      <p>작성일 : {{ article.created_at }}</p>
      <p>수정일 : {{ article.updated_at }}</p>
    </div>
  </div>
</template>
```

## 게시글 작성

1. CreateView 관련 route 작성

```jsx
// router/index.js

import CreateView from '@/views/CreateView.vue'

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
   ...,
    {
      path: '/create',
      name: 'CreateView',
      component: CreateView
    },
```

1. ArticleView에 CreateView 컴포넌트로 가기 위한 RouterLink 작성

```jsx
<!-- views/ArticleView.vue -->

<script setup>
import ArticleList from '@/components/ArticleList.vue'
import { onMounted } from 'vue'
import { useCounterStore } from '@/stores/counter'
**import { RouterLink } from 'vue-router'**
</script>
```

```jsx
<!-- views/ArticleView.vue -->

<template>
  <div>
    <h1>Article Page</h1>
    <RouterLink :to="{ name: 'CreateView' }">Create</RouterLink>
    <ArticleList />
  </div>
</template>
```

1. v-model을 사용해 사용자 입력 데이터를 양방향 바인딩
    - v-model의 trim 수식어를 사용해 사용자 입력 데이터의 공백 제거

```jsx
<!-- views/CreateView.vue -->

<template>
  <div>
    <h1>게시글 작성</h1>
    <form>
      <div>
        <label for="title">제목 : </label>
        <input type="text" id="title" v-model.trim="title">
      </div>
      <div>
        <label for="content">내용 : </label>
        <textarea id="content" v-model.trim="content"></textarea>
      </div>
      <input type="submit">
    </form>
  </div>
</template>

```

```jsx
<!-- views/CreateView.vue -->

<script setup>
import { ref } from 'vue'

const title = ref(null)
const content = ref(null)
</script>
```

1. 게시글 생성 요청을 담당하는 createArticle 함수 작성
    - 게시글 생성이 성공한다면 ArticleView 컴포넌트로 이동

```jsx
// views/CreateView.vue

import { ref } from 'vue'
import { useCounterStore } from '@/stores/counter'
import axios from 'axios'
import { useRouter } from 'vue-router'

const title = ref(null)
const content = ref(null)
const store = useCounterStore()
const router = useRouter()
```

```jsx
// views/CreateView.vue

const createArticle = function () {
  axios({
    method: 'post',
    url: `${store.API_URL}/api/v1/articles/`,
    data: {
      title: title.value,
      content: content.value
    }
  })
    .then((res) => {
      // console.log('게시글 작성 성공!')
      router.push({ name: 'ArticleView' })
    })
    .catch((err) => {
      console.log(err)
    })
}
```

1. submit 이벤트가 발생하면 createArticle 함수 호출
    - v-on의 prevent 수식어를 사용해 submit 이벤트의 기본 동작 취소

```jsx
<template>
  <div>
    <h1>게시글 작성</h1>
    <form @submit.prevent="createArticle">
      <div>
        <label for="title">제목 : </label>
        <input type="text" id="title" v-model.trim="title">
      </div>
      <div>
        <label for="content">내용 : </label>
        <textarea id="content" v-model.trim="content"></textarea>
      </div>
      <input type="submit">
    </form>
  </div>
</template>
```