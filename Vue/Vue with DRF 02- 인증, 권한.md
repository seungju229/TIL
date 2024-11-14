# 인증 with DRF

## 인증

- 수신된 요청을 해당 요청의 사용자 또는 자격 증명과 연결하는 메커니즘
- 누구인지를 확인하는 과정

### 권한

- 요청에 대한 접근 허용 또는 거부 여부를 결정

### 인증과 권한

- 순서상 인증이 먼저 진행되며 수신 요청을 해당 요청의 사용자 또는 해당 요청이 서명된 토큰과 같은 자격 증명 자료와 연결
- 그런 다음 권한 및 제한 정책은 인증이 완료된 해당 자격 증명을 사용하여 요청을 허용해야 하는 지를 결정

### DRF에서의 인증

- 인증은 항상 view 함수 시작 시, 권한 및 제한 확인이 발생하기 전, 다른 코드의 진행이 허용되기 전에 실행됨
- 인증 자체로는 들어오는 요청을 허용하거나 거부할 수 없으며, 단순히 요청에 사용된 자격증명만 식별한다는 점에 유의

### 승인되지 않은 응답 및 금지된 응답

- 인증되지 않은 요청이 권한을 거부하는 경우 해당되는 두 가지 오류 코드를 응답
1. HTTP 401 Unauthorized
    - 요청된 리소스에 대한 유효한 인증 자격 증명이 없기 때문에 클라이언트 요청이 완료되지 않았음을 나타냄(누구인지를 증명할 자료가 없음)
2. HTTP 403 Forbidden (Permission Denied)
    - 서버에 요청이 전달되었지만, 권한 때문에 거절되었다는 것을 의미
    - 401과 다른 점은 서버는 클라이언트가 누구인지는 알고 있음

## 인증 정책 설정

### 방법(1) - 전역 설정

- 프로젝트 전체에 적용되는 기본 인증 방식을 정의
- DEFAULT_AUTHENTICATION_CLASSES를 사용
- 기본 값: SessionAuthentication과 BasicAuthentication
- 사용 예시(django 공식 문서 참고)

```python
# settings.py

REST_FRAMEWORK = {
    # Authentication
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
    ],
}
```

### 방법(2) - View 함수 별 설정

- authentication_classes 데코레이터를 사용
- 개별 view에 지정하여 재정의
- 사용 예시

```python
from rest_framework.decorators import authentication_classes
from rest_framework.authentication import TokenAuthentication, BasicAuthentication

@api_view(['GET', 'POST'])
@permission_classes([IsAuthenticated])
def article_list(request):
	pass
```

### DRF가 제공하는 인증 체계

1. BasicAuthentication
2. **TokenAuthentication**
3. SessionAuthentication
4. RemoteUserAuthentication

### **TokenAuthentication**

- token 기반 HTTP 인증 체계
- 기본 데스크톱 및 모바일 클라이언트와 같은 클라이언트-서버 설정에 적합
- 서버가 인증된 사용자에게 토큰을 발급하고 사용자는 매 요청마다 발급 받은 토큰을 요청과 함께 보내 인증 과정을 거침

## Token 인증 설정

1. 인증 클래스 설정
    - TokenAuthentication 활성화 코드 작성 (drf 공식 문서 참고)
    - 전역 인증 정책을 Token 방식으로 설정
    
    ```python
    # settings.py
    
    REST_FRAMEWORK = {
        # Authentication
        'DEFAULT_AUTHENTICATION_CLASSES': [
            'rest_framework.authentication.TokenAuthentication',
        ],
    }
    ```
    
2. INSTALLED_APPS 추가
3. Migrate 진행
4. 토큰 생성 코드 작성
    - accounts/signals.py 작성
    - 인증된 사용자에게 자동으로 토큰을 생성해주는 역할
    
    ```python
    # accounts/signals.py
    
    from django.db.models.signals import post_save
    from django.dispatch import receiver
    from rest_framework.authtoken.models import Token
    from django.conf import settings
    
    @receiver(post_save, sender=settings.AUTH_USER_MODEL)
    def create_auth_token(sender, instance=None, created=False, **kwargs):
        if created:
            Token.objects.create(user=instance)
    ```
    

### 토큰 인증 방식 과정

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1a5b2bca-9c7c-4a9d-b553-9a8142d9c9ac/d395f4bd-096b-4fd3-9d17-e3674245b5a6/image.png)

## Dj-Rest-Auth 라이브러리

### Dj-Rest-Auth

- 회원가입, 인증(소셜미디어 인증 등), 비밀번호 재설정, 사용자 세부 정보 검색, 회원 정보 수정 등 다양한 인증 관련 기능을 제공하는 라이브러리

### Dj-Rest-Auth 설치 및 적용

- 설치

```bash
pip install dj-rest-auth
```

- INSTALLED_APPS 작성
- 추가 URL 작성

```python
# my_api/urls.py

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/v1/', include('articles.urls')),
    path('accounts/', include('dj_rest_auth.urls')),
    # path('accounts/signup/', include('dj_rest_auth.registration.urls')),
]
```

### Dj-Rest-Auth Registration(등록) 기능 추가 설정

1. 패키지 추가 설치
    
    ```bash
    pip install 'dj-rest-auth[with-social]'
    ```
    
2. 추가 App 등록, 관련 설정 코드 작성(settings.py)
    
    https://dj-rest-auth.readthedocs.io/en/latest/installation.html#registration-optional
    
3. 추가 URL 등록
4. Migrate

## Token 발급 및 활용

<aside>
💡

회원 가입 및 로그인을 진행하여 토큰 발급 테스트하기

</aside>

- 라이브러리 설치로 인해 추가된 URL 목록 확인
- 회원 가입, 로그인 진행(DRF 페이지 하단 회원가입, 로그인 폼 이용)
- 로그인 성공 후 DRF로부터 발급 받은 Token 확인
    
    ✅ 이제 이 Token을 Vue에서 별도로 저장하여 매 요청마다 함께 보내야 함
    

### Token 활용

- Postman을 활용해 게시글 작성 요청
    - **Body**에 게시글 제목과 내용 입력
- **Headers**에 발급 받은 Token 작성 후 요청 성공 확인
    - Key: “Authorization”
    - Value: “Token 토큰 값”

### 클라이언트가 Token으로 인증 받는 방법

1. “Authorization” HTTP Header에 포함
2. 키 앞에는 문자열 “Token”이 와야 하며, “공백”으로 두 문자열을 구분해야 함

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1a5b2bca-9c7c-4a9d-b553-9a8142d9c9ac/eba5f7be-51a4-4800-85a2-adca48ca64d9/image.png)

※ 발급 받은 Token을 인증이 필요한 요청마다 함께 보내야 함

# 권한 with DRF

## 권한 정책 설정

### 방법(1) - 전역 설정

- 프로젝트 전체에 적용되는 기본 권한 방식을 정의
- DEFAULT_PERMISSION_CLASSES를 사용
- 기본 값: `rest_framework.permissions.AllowAny`
- 사용 예시

```python
# settings.py

REST_FRAMEWORK = {
    # permission
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.AllowAny',
    ],
}
```

### 방법(2) - View 함수 별 설정

- permission_classes 데코레이터를 사용
- 개별 view에 지정하여 재정의
- 사용 예시

```python
# permission Decorators
from rest_framework.decorators import permission_classes
from rest_framework.permissions import IsAuthenticated

@api_view(['GET', 'POST'])
@permission_classes([IsAuthenticated])
def article_list(request):
	pass
```

### DRF가 제공하는 권한 정책

1. **IsAuthenticated**
2. IsAdminUser
3. IsAuthenticatedOrReadOnly
4. …

### **IsAuthenticated**

- 인증되지 않은 사용자에 대한 권한을 거부하고 그렇지 않은 경우 권한을 허용
- 등록된 사용자만 API에 액세스 할 수 있도록 하려는 경우에 적합

## IsAuthenticated 설정

- DEFAULT_PERMISSION_CLASSES 작성
    - 기본적으로 모든 View 함수에 대한 접근 허용

```python
# settings.py

REST_FRAMEWORK = {
    # Authentication
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
    ],
    # permission
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.AllowAny',
    ],
}
```

- permission_classes 관련 코드 작성
    - 전체 게시글 조회 및 생성시에만 인증된 사용자만 진행할 수 있도록 권한 설정
    
    ```python
    # articles/views.py
    from rest_framework.decorators import permission_classes
    from rest_framework.permissions import IsAuthenticated
    
    @api_view(['GET', 'POST'])
    @permission_classes([IsAuthenticated])
    def article_list(request):
    	pass
    ```
    

### 권한 활용

- 만약 관리자만 전체 게시글 조회가 가능한 권한이 설정되었을 때, 인증된 일반 사용자가 조회 요청을 할 경우 어떻게 되는지 응답 확인하기
- 테스트를 위해 임시로 관리자 관련 권한 클래스 IsAdminUser로 변경

```python
# articles/views.py

from rest_framework.permissions import IsAuthenticated, IsAdminUser

@api_view(['GET', 'POST'])
@permission_classes([IsAdminUser])
def article_list(request):
	pass
```

- 전체 게시글 조회 요청
- 403 Forbidden 응답 확인
- IsAdminUser 삭제 후 IsAuthenticated 권한으로 복구

# 인증 with Vue

## 회원가입

- SignUpView route 관련 코드 작성

```jsx
// router/index.js

import SignUpView from '@/views/SignUpView.vue'

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
    {
      path: '/signup',
      name: 'SignUpView',
      component: SignUpView
    }
  ]
})
```

- App 컴포넌트에 SignUpView 컴포넌트로 이동하는 RouterLink 작성

```jsx
// App.vue

<template>
  <header>
    <nav>
      <RouterLink :to="{ name: 'ArticleView' }">Articles</RouterLink> |
      <RouterLink :to="{ name: 'SignUpView' }">SignUpView</RouterLink> |
    </nav>
  </header>
  <RouterView />
</template>
```

- 회원가입 form 작성

```jsx
<!-- views/SignUpView.vue -->

<template>
  <div>
    <h1>Sign Up Page</h1>
    <form @submit.prevent="signUp">
      <label for="username">username : </label>
      <input type="text" id="username" v-model.trim="username"><br>

      <label for="password1">password : </label>
      <input type="password" id="password1" v-model.trim="password1"><br>

      <label for="password2">password confirmation : </label>
      <input type="password" id="password2" v-model.trim="password2">
      
      <input type="submit" value="SignUp">
    </form>
  </div>
</template>
```

- 사용자 입력 데이터와 바인딩 될 반응형 변수 작성

```jsx
<!-- views/SignUpView.vue -->

<script setup>
import { ref } from 'vue'

const username = ref(null)
const password1 = ref(null)
const password2 = ref(null)
</script>
```

- SignupView 컴포넌트 출력 확인
- 회원가입 요청을 보내기 위한 signUp 함수가 해야 할 일
    1. 사용자 입력 데이터를 받아
    2. 서버로 회원가입 요청을 보냄
    
    ```jsx
    // stores/counter.js
    
    export const useCounterStore = defineStore('counter', () => {
      // 회원가입 요청 액션
      const signUp = function (payload) {
        ...
      }
      return { articles, API_URL, getArticles, signUp }
    }, { persist: true })
    ```
    
- 컴포넌트에 사용자 입력 데이터를 저장 후 store의 signUp 함수를 호출하는 함수 작성

```jsx
// views/SignUpView.vue

import { useCounterStore } from '@/stores/counter'

const store = useCounterStore()

const signUp = function () {
  const payload = {
    username: username.value,
    password1: password1.value,
    password2: password2.value
  }
  store.signUp(payload)
}
```

```jsx
<!-- views/SignUpView.vue -->

<form @submit.prevent="signUp">
  ...
</form>
```

- 실제 회원가입 요청을 보내는 store의 signUp 함수 작성

```jsx
// stores/counter.js

// 회원가입 요청 액션
  const signUp = function (payload) {
    // const username = payload.username
    // const password1 = payload.password1
    // const password2 = payload.password2
    const { username, password1, password2 } = payload

    axios({
      method: 'post',
      url: `${API_URL}/accounts/signup/`,
      data: {
        username, password1, password2
      }
    })
      .then((res) => {
        // console.log(res)
        // console.log('회원가입 성공')
      })
      .catch((err) => {
        console.log(err)
      })
  }
```

## 로그인

- LogInView route 코드 작성

```jsx
// router/index.js

import LogInView from '@/views/LogInView.vue'

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
   ...,
    {
      path: '/login',
      name: 'LogInView',
      component: LogInView
    }
  ]
})
```

- App 컴포넌트에 LogInView 컴포넌트로 이동하는 RouterLink 작성

```jsx
// App.vue

<template>
  <header>
    <nav>
      <RouterLink :to="{ name: 'ArticleView' }">Articles</RouterLink> |
      <RouterLink :to="{ name: 'SignUpView' }">SignUpView</RouterLink> |
      **<RouterLink :to="{ name: 'LogInView' }">LogInView</RouterLink> |** 
    </nav>
  </header>
  <RouterView />
</template>
```

- 로그인 form 작성

```jsx
<!-- views/LoginView.vue -->

<template>
  <div>
    <h1>LogIn Page</h1>
    <form @submit.prevent="logIn">
      <label for="username">username : </label>
      <input type="text" id="username" v-model.trim="username"><br>

      <label for="password">password : </label>
      <input type="password" id="password" v-model.trim="password"><br>

      <input type="submit" value="logIn">
    </form>
  </div>
</template>
```

- 사용자 입력 데이터와 바인딩 될 반응형 변수 작성

```jsx
<!-- views/LoginView.vue -->

<script setup>
import { ref } from 'vue'

const username = ref(null)
const password = ref(null)
</script>
```

- LogInView 컴포넌트 출력 확인
- 로그인 요청을 보내기 위한 LogIn 함수가 해야할 일
    1. 사용자 입력 데이터를 받아
    2. 서버로 로그인 요청 및 응답 받은 토큰 저장
    
    ```jsx
    // stores/counter.js
    
    export const useCounterStore = defineStore('counter', () => {
    
      // 로그인 요청 액션
      const logIn = function (payload) {
        ...
      }
      return { articles, API_URL, getArticles, signUp, logIn }
    }, { persist: true })
    ```
    
- 컴포넌트에 사용자 입력 데이터 저장 후 store의 LogIn 함수를 호출하는 함수 작성

```jsx
// views/LoginView.vue

import { useCounterStore } from '@/stores/counter'

const store = useCounterStore()

const logIn = function () {
  const payload = {
    username: username.value,
    password: password.value
  }
  store.logIn(payload)
}
```

- 실제 로그인 요청을 보내는 store의 LogIn 함수 작성

```jsx
// stores/counter.js

// 로그인 요청 액션
  const logIn = function (payload) {
    // const username = payload.username
    // const password1 = payload.password
    const { username, password } = payload

    axios({
      method: 'post',
      url: `${API_URL}/accounts/login/`,
      data: {
        username, password
      }
    })
      .then((res) => {
        // console.log(res.data)
        // console.log('로그인 성공')
      })
      .catch((err) => {
        console.log(err)
      })
  }

```

- 로그인 테스트
    - 응답 객체 안에 Django 가 발급한 Token이 함께 온 것 확인

## 요청과 토큰

Token을 store에 저장하여 인증이 필요한 요청마다 함께 보낸다.

### 토큰 저장 로직 구현

- 반응형 변수 token 선언 및 토큰 저장

```jsx
// stores/counter.js

export const useCounterStore = defineStore('counter', () => {
 
  const token = ref(null)
  // 로그인 요청 액션
  const logIn = function (payload) {
    ...
      .then((res) => {
        **token.value = res.data.key**
        router.push({ name: 'ArticleView' })
        // console.log(res.data)
        // console.log('로그인 성공')
      })
      .catch((err) => {
        console.log(err)
      })
  }
  return { articles, API_URL, getArticles, signUp, logIn, token }
}, { persist: true })
```

- 다시 로그인 요청 후 store에 저장된 토큰 확인

### 토큰이 필요한 요청 (1) - 게시글 전체 목록 조회 시

- 게시글 전체 목록 조회 요청 함수 getArticles에 token 추가

```jsx
// stores/counter.js

// DRF로 전체 게시글 요청을 보내고 응답을 받아 articles에 저장하는 함수
const getArticles = function () {
  axios({
    method: 'get',
    url: `${API_URL}/api/v1/articles/`,
    headers: {
      Authorization: `Token ${token.value}`
    }
  })
    .then((res) => {
      articles.value = res.data
    })
    .catch((err) => {
      console.log(err)
    })
}
```

### 토큰이 필요한 요청 (2) - 게시글 작성 시

- 게시글 생성 요청 함수 createArticle에 token 추가

```jsx
<!-- views/CreateView.vue -->

// DRF로 게시글 생성 요청을 보내는 함수
const createArticle = function () {
  axios({
    method: 'post',
    url: `${store.API_URL}/api/v1/articles/`,
    data: {
      title: title.value,
      content: content.value
    },
    headers: {
      Authorization: `Token ${store.token}`
    }
  })
...
}
```

## 인증 여부 확인

1. 인증되지 않은 사용자 메인 페이지 접근 제한
2. 인증된 사용자 회원가입 및 로그인 페이지 접근 제한

### 인증 상태 여부를 나타낼 속성 값 지정

- token 소유 여부에 따라 로그인 상태를 나타낼 isLogin 변수 작성
- 그리고 computed를 활용해 token 값이 변할 때만 상태를 계산하도록 함

```jsx
// stores/counter.js

const isLogin = computed(() => {
  if (token.value === null) {
    return false
  } else {
    return true
  }
})
```

### 인증되지 않은 사용자 메인 페이지 접근 제한

- 전역 네비게이션 가드 beforeEach를 활용해 다른 주소에서 메인 페이지로 이동 시 인증 되지 않은 사용자라면 로그인 페이지로 이동시키기

```jsx
// router/index.js

import { useCounterStore } from '@/stores/counter'

const router = createRouter({...})
router.beforeEach((to, from) => {
  const store = useCounterStore()
  // 만약 이동하는 목적지가 메인 페이지이면서
  // 현재 로그인 상태가 아니라면 로그인 페이지로 보냄
  if (to.name === 'ArticleView' && !store.isLogin) {
    window.alert('로그인이 필요합니다.')
    return { name: 'LogInView' }
  }
```

- 브라우저 local storage에서 token을 삭제 후 메인 페이지 접속 시도

### 인증된 사용자 회원가입 및 로그인 페이지 접근 제한

- 다른 주소에서 회원 가입 또는 로그인 페이지로 이동 시 이미 인증된 사용자라면 메인 페이지로 이동시키기

```jsx
// router/index.js
router.beforeEach((to, from) => {
  const store = useCounterStore()
  // 만약 이동하는 목적지가 메인 페이지이면서
  // 현재 로그인 상태가 아니라면 로그인 페이지로 보냄
  if (to.name === 'ArticleView' && !store.isLogin) {
    window.alert('로그인이 필요합니다.')
    return { name: 'LogInView' }
  }

  **// 만약 로그인 사용자가 회원가입 또는 로그인 페이지로 이동하려고 하면
  // 메인 페이지로 보냄
  if ((to.name === 'SignUpView' || to.name === 'LogInView') && (store.isLogin)) {
    window.alert('이미 로그인 되어있습니다.')
    return { name: 'ArticleView' }
  }**
})
```

# 참고

## 기타 기능 구현

### 1. 로그인 성공 후 자동으로 메인 페이지로 이동하기

```jsx
// stores/counter.js

import { useRouter } from 'vue-router'

export const useCounterStore = defineStore('counter', () => {
  const router = useRouter()

  // 로그인 요청 액션
  const logIn = function (payload) {
   ...
      .then((res) => {
        token.value = res.data.key
        **router.push({ name: 'ArticleView' })**
        // console.log(res.data)
        // console.log('로그인 성공')
      })
      .catch((err) => {
        console.log(err)
      })
  }
```

### 2. 회원가입 성공 후 자동으로 로그인까지 진행하기

```jsx
// stores/counter.js

export const useCounterStore = defineStore('counter', () => {
  // 회원가입 요청 액션
  const signUp = function (payload) {
	 ...
      .then((res) => {
        **const password = password1
        logIn({ username, password })**
      })
      .catch((err) => {
        console.log(err)
      })
  }
```

### 3. 로그아웃

```jsx
// stores/counter.js
  
  // [추가기능] 로그아웃
  const logOut = function () {
    axios({
      method: 'post',
      url: `${API_URL}/accounts/logout/`,
    })
      .then((res) => {
        console.log(res.data)
        token.value = null
        router.push({ name: 'ArticleView' })
      })
      .catch((err) => {
        console.log(err)
      })
  }
  
// App.vue
<form @submit.prevent="logOut">
<input type="submit" value="Logout">
</form>
```

## Django Signals

- 이벤트 알림 시스템
- 애플리케이션 내에서 특정 이벤트가 발생할 때, 다른 부분에게 신호를 보내어 이벤트가 발생했음을 알릴 수 있음
- 주로 모델의 데이터 변경 또는 저장, 삭제와 같은 작업에 반응하여 추가적인 로직을 실행하고자 할 때 사용
    - 예를 들어, 사용자가 새로운 게시글을 작성할 때마다 특정 작업(예: 이메일 알림 보내기)을 수행하려는 경우

## 환경 변수

- 애플리케이션의 설정이나 동작을 제어하기 위해 사용되는 변수

### 환경 변수의 목적

- 개발, 테스트 및 프로덕션 환경에서 다르게 설정되어야 하는 설정 값이나 민감한 정보(ex. API key)를 포함
- 환경 변수를 사용하여 애플리케이션의 설정을 관리하면, 다양한 환경에서 일관된 동작을 유지하면서 필요에 따라 변수를 쉽게 변경할 수 있음
- 보안적인 이슈를 피하고, 애플리케이션을 다양한 환경에 대응하기 쉽게 만들어줌

### Vite에서 환경 변수를 사용하는 법

- .env.local 파일 생성 및 API 변수 작성
- 주의사항
    - 변수명은 반드시 VITE_접두어를 작성해야 함
    - 변수명과 값 사이에 공백이 없어야 함

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/1a5b2bca-9c7c-4a9d-b553-9a8142d9c9ac/3fdb7950-cf97-4b27-b44a-99dbc4f3750a/image.png)

## Vue 참고 자료

- Awesome Vue.js
    - Vue와 관련하여 선별된 유용한 자료를 아카이빙 및 관리하는 프로젝트
- Vuetify
    - Vue를 위한 UI 라이브러리