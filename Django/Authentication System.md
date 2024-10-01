# Cookie & Session

- 우리가 웹 페이지를 둘러볼 때 우리는 서버와 서로 연결되어 있는 상태가 아니다.

## HTTP

- HTML 문서와 같은 리소스들을 가져올 수 있도록 해주는 규약
- 웹(WWW)에서 이뤄지는 모든 데이터 교환의 기초

### HTTP 특징

1. 비 연결 지향
    - 서버는 요청에 대한 응답을 보낸 후 연결을 끊음
2. 무상태
    - 연결을 끊는 순간 클라이언트와 서버 간의 통신이 끝나며 **상태 정보가 유지되지 않음**
        - 장바구니에 담은 상품을 유지할 수 없음
        - 로그인 상태를 유지할 수 없음

## 쿠키

- 서버가 사용자의 웹 브라우저에 전송하는 작은 데이터 조각
- **서버가 제공**하여 **클라이언트 측**에서 저장되는 **작은 데이터 파일**
- 사용자 인증, 추적, 상태 유지 등에 사용되는 데이터 저장 방식

### 쿠키 동작 예시

1. 브라우저가 웹 서버에 웹 페이지를 요청
2. 웹 서버는 요청된 페이지와 함께 쿠키를 포함한 응답을 브라우저에게 전송
3. 브라우저는 받은 쿠키를 저장소에 저장. 쿠키의 속성(만료 시간, 도메인, 주소 등)도 함께 저장됨
4. 이후 브라우저가 **같은 웹 서버**에 웹 페이지를 요청할 때, 저장된 쿠키 중 해당 요청에 적용 가능한 쿠키를 포함하여 함께 전송
5. 웹 서버는 받은 쿠키 정보를 확인하고 필요에 따라 사용자 식별, 세션 관리 등을 수행
6. 웹 서버는 요청에 대한 응답을 보내며, 필요한 경우 새로운 쿠키를 설정하거나 기존 쿠키를 수정할 수 있음

### 쿠키를 이용한 장바구니 예시

1. 장바구니에 상품 담기
2. 개발자 도구 - network 탭 - carView.pang 확인
    - 서버는 응답과 함께 Set-Cookie 응답 헤더를 브라우저에게 전송
    - 이 헤더는 클라이언트에게 쿠키를 저장하라고 전달하는 것
3. Cookie 데이터 자세히 확인
4. 메인 페이지 이동 - 장바구니 유지 상태 확인
5. 개발자 도구 - Application 탭 - Cookies
    1. 마우스 우측 버튼 - Clear(브라우저에 있는 쿠키를 지움) - 새로 고침 - 장바구니가 빈 것을 확인

### 쿠키의 작동 원리와 활용

1. 쿠키 저장 방식
    - 브라우저(클라이언트)는 쿠키를 key-value 의 데이터 형식으로 저장
    - 쿠키에는 이름, 값 외에도 만료 시간, 도메인, 경로 등의 추가 속성이 포함됨
2. 쿠키 전송 과정
    - 서버는 HTTP 응답 헤더의 Set-Cookie 필드를 통해 클라이언트에게 쿠키를 전송
    - 브라우저는 받은 쿠키를 저장해 두었다가, **동일한 서버**에 재 요청 시 HTTP 요청 Header의 Cookie 필드에 저장된 쿠키를 함께 전송
3. 쿠키의 주요 용도
    - 두 요청이 동일한 브라우저에서 들어왔는지 아닌지를 판단할 때 주로 사용됨
    - 이를 이용해 사용자의 로그인 상태를 유지할 수 있음
    - 상태가 없는(stateless) HTTP 프로토콜에서 **상태 정보를 기억시켜 주는 역할**

```
서버에게 “ 나 로그인된 사용자야!” 라는 인증 정보가 담긴 쿠키를 매 요청마다 계속 보내는 것
매 요청마다 장바구니에 이 상품이 있다는 사실을 계속해서 보냄
```

### 쿠키 사용 목적

1. 세션 관리(상태 관리)
    - 로그인, 아이디 자동 완성, 공지 하루 안 보기, 팝업 체크, 장바구니 등의 정보 관리
2. 개인화
    - 사용자 선호 설정(언어 설정, 테마 등) 저장
3. 트래킹
    - 사용자 행동을 기록 및 분석

## 세션

- 서버 측에서 생성되어 클라이언트와 서버 간의 **상태를 유지**하는 것
- 상태 정보를 저장하는 데이터 저장 방식
- 쿠키에 세션 데이터를 저장하여 매 요청 시마다 세션 데이터를 함께 보냄

### 세션 작동 원리

1. 클라이언트가 로그인 요청 후 인증에 성공하면 서버가 세션 데이터를 생성 후 암호화 하여 DB에 저장
2. 생성된 세션 데이터에 인증할 수 있는( 암호화된 데이터에 접근할 수 있는) `session id(키)` 발급
3. 발급한 `session id`를 클라이언트에게 응답(데이터는 서버에 저장, 열쇠만 주는 것)
4. 클라이언트는 응답 받은 `session id`를 쿠키에 저장
5. 클라이언트가 다시 동일한 서버에 접속하면 요청과 함께 쿠키(`session id`가 저장된)를 서버에 전달
6. 쿠키는 요청 때마다 서버에 함께 전송되므로 서버에서 `session id`를 확인해 로그인 되어있다는 것을 계속해서 확인하도록 함(로그인 상태 유지)

❗서버 측에서는 **세션 데이터를 생성** 후 **저장**하고 이 데이터에 접근할 수 있는 **`session id`를 생성**.

이 **ID를 클라이언트 측으로 전달**하고, **클라이언트는 쿠키에 이 ID를 저장.** 

이후 클라이언트가 같은 서버에 재 요청 시마다 저장해 두었던 쿠키도 요청과 함께 전송

(예를 들어 로그인 상태 유지를 위해 로그인 되어있다는 사실을 입증하는 데이터를 매 요청마다 계속해서 보내는 것)

### 쿠키와 세션의 목적

- 클라이언트와 서버 간의 상태 정보를 유지하고 사용자를 식별하기 위해 사용

# Django Authentication System

- `Authentication` : 사용자가 자신이 누구인지 확인하는 것

### 사전 준비

- 두 번째 app `accounts` 생성 및 등록
- `auth`와 관련한 경로나 키워드들을 django 내부적으로 `accounts`라는 이름으로 사용하고 있기 때문에 되도록 `accounts` 로 지정하는 것을 권장

```python
# crud/urls.py
urlpatterns = [
		...,
    path('accounts/', include('accounts.urls')),
]
```

```bash
# accounts/urls.py
app_name = 'accounts'
urlpatterns = [

]
# 비어 있더라도 일단 만들어둬야 에러가 안 남
```

# Custom User model

### 기존 User Model의 한계

- 우리는 지금까지 별도의 `User` 클래스정의 없이 내장된 `auth` 앱에 작성된 `Use`r 클래스를 사용했음
- Django의 기본 `User` 모델은 `username`, `password` 등 제공되는 필드가 매우 제한적
- 추가적인 사용자 정보(예: 생년월일, 주소, 나이 등)가 필요하다면 이를 위해 User Model을 변경하기 어려움
    - 별도의 설정 없이 사용할 수 있어 간편하지만, 개발자가 직접 수정하기 어려움

### User Model 대체의 필요성

- 프로젝트의 특정 요구 사항에 맞춰 사용자 모델을 확장할 수 있음
- 예를 들어 이메일을 `username`으로 사용하거나, 다른 추가 필드를 포함시킬 수 있음

## User model 대체하기

1. `AbstractUser` 클래스를 상속 받는 커스텀 User 클래스 작성
    - `기존 User` 클래스도 `AbstractUser`를 상속 받기 때문에 `커스텀 User` 클래스도 `기존 User` 클래스와 완전히 같은 모습을 가지게 됨
    
    ```python
    # accounts/models.py
    
    from django.contrib.auth.models import AbstractUser
    from django.db import models
    
    # Create your models here.
    # 커스텀 User 모델로 대체하기 위한 class 작성(일단은 커스텀할 게 없으니까 pass)
    class User(AbstractUser):
        pass
    ```
    
2. django 프로젝트에서 사용하는 기본 User 모델을 우리가 작성한 User 모델로 사용할 수 있도록 `AUTH_USER_MODEL` 값을 변경
    - 수정 전 기본 값은 `auth.User`
    
    ```python
    # settings.py
    
    AUTH_USER_MODEL = 'accounts.User'
    ```
    
3. admin site에 대체한 User 모델 등록(외우는 것 아님)
    - `기본 User` 모델이 아니기 때문에 등록하지 않으면 admin 페이지에 출력 되지 않기 때문
    
    ```python
    # accounts/admin.py
    
    from django.contrib import admin
    from django.contrib.auth.admin import UserAdmin
    from .models import User
    
    # Register your models here.
    admin.site.register(User, UserAdmin)
    ```
    

### AUTH_USER_MODEL

- django 프로젝트의 `User`를 나타내는 데 사용하는 모델을 지정하는 속성
    
    ※ 주의
    
    - 프로젝트 중간에 `AUTH_USER_MODEL`을 변경할 수 없음
    - 이미 프로젝트가 진행되고 있을 경우 데이터베이스 초기화 후 진행
    

### 프로젝트를 시작하면 반드시 User 모델을 대체해야 한다

- Django는 새 프로젝트를 시작하는 경우 비록 기본 User 모델이 충분하더라도, 커스텀 User 모델을 설정하는 것을 강력하게 권장하고 있음
- 커스텀 User 모델은 **기본 User 모델과 동일하게 작동(1)** 하면서도 **필요한 경우 나중에 맞춤 설정(2)**할 수 있기 때문
    
    ※단, User 모델 대체 작업은 프로젝트의 모든 migrations 혹은 첫 migrate를 실행하기 전에 이 작업을 마쳐야 함
    

# Login

- Session을 create하는 과정

### `AuthenticationForm()`

- 로그인 인증에 사용할 데이터를 입력 받는 built-in form (db에 바로 저장되는 것이 아니라 `ModelForm`이 아니라 그냥 `Form` 사용, 회원 가입 시에는 `ModelForm` 사용)

### 로그인 페이지 작성

```python
# accounts/urls.py

app_name = 'accounts'
urlpatterns = [
    path('login/', views.login, name='login'),
]
```

```python
# accounts/views.py

from django.shortcuts import render, redirect
from django.contrib.auth import login as auth_login

def login(request):
    if request.method == 'POST':
        form = AuthenticationForm(request, request.POST)
        # ModelForm 과 달리 Form 은 첫 번째 인자로 request를 받고, 두 번째로 데이터를 받음
        if form.is_valid():
            # 만약 인증된 사용자라면 로그인 진행(세션데이터 생성)
            # auth_login(request, 인증된 유저 객체)
            auth_login(request, form.get_user())
            return redirect('articles:index')
    else:
        form = AuthenticationForm()
    context = {
        'form': form,
    }
    return render(request, 'accounts/login.html', context)
```

```python
# accounts/login.html
 
 <h1>로그인</h1>
  <form action="{% url "accounts:login" %}" method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <input type="submit">
  </form>
```

### `login(request, user)`

: `AuthenticationForm` 을 통해 인증된 사용자를 로그인 하는(세션 데이터 생성) 함수

### `get_user()`

: `AuthenticationForm` 의 인스턴스 메서드

- **유효성 검사를 통과했을 경우** 로그인 한 사용자 객체를 반환

### 세션 데이터 확인하기

1. 로그인 후 발급 받은 세션 확인 (db에 연결 후 진행)
    - `django_session` 테이블에서 확인
2. 브라우저에서 확인
    - 개발자도구 - `Application` - `Cookies`
        - `sessionid` 있으면 성공

### 로그인 링크 작성

- 메인 페이지에 로그인 페이지로 갈 수 있는 링크 작성
    
    ```html
    # articles/index.html
    
    <h1>Articles</h1>
    <a href="{% url "accounts:login" %}">LOGIN</a>
    <a href="{% url "articles:create" %}">CREATE</a>
    <hr>  
    ```
    

# Logout

- Session을 Delete하는 과정

### logout(request)

1. DB에서 현재 요청에 대한 `Session Data`를 삭제
2. 클라이언트의 쿠키에서도 `Session Id`를 삭제

### logout 로직 작성

```python
# accounts/urls.py

urlpatterns = [
    path('login/', views.login, name='login'),
    path('logout/', views.logout, name='logout'),
]
```

```python
# accounts.views.py

from django.contrib.auth import logout as auth_logout

def logout(request):
    # 세션 데이터 삭제
    auth_logout(request)
    return redirect('articles:index')
```

```html
# articles/index.html

<h1>Articles</h1>
  <a href="{% url "accounts:login" %}">LOGIN</a>

  <form action="{% url "accounts:logout" %}" method="POST">
    {% csrf_token %}
    <input type="submit" value="LOGOUT">
  </form>
```

💡 왜 login은 `a tag`고 logout은 `form` ?

- `a tag`는 `get` 요청을 보내서 로그인 페이지를 달라고 하는 요청
- logout은 페이지를 요청하는 게 아니라 ‘로그아웃 시켜줘’ 라고 요청하고 db에 있는 세션 데이터를 지우는 거라 `post` 요청

# Template with Authentication data

: 템플릿에서 인증 관련 데이터를 출력하는 방법

### 현재 로그인 되어 있는 유저 정보 출력하기

- `user`라는 `context` 데이터를 사용할 수 있는 이유는?
    - django가 미리 준비한 context 데이터가 존재하기 때문(`context processors`)
    - `settings.py`에서 확인 가능

### context processors

- 템플릿이 렌더링 될 때 호출 가능한 컨텍스트 데이터 목록
- 작성된 컨텍스트 데이터는 기본적으로 템플릿에서 사용 가능한 변수로 포함됨
    - django에서 자주 사용하는 데이터 목록을 미리 템플릿에 로드해 둔 것

# 참고

## 쿠키의 수명

### 쿠키 종류별 Lifetime(수명)

1. Session cookie
    - 현재 세션(current session)이 종료되면 삭제됨
    - 브라우저 종료와 함께 세션이 삭제 됨
2. Persistent cookies
    - Expires 속성에 지정된 날짜 혹은 Max-Age 속성에 지정된 기간이 지나면 삭제됨

## 쿠키와 보안

### 쿠키의 보안 장치

- 제한된 정보
    - 쿠키에는 보통 중요하지 않은 정보만 저장(사용자 ID나 세션 번호 같은 것)
- 암호화
    - 중요한 정보는 서버에서 암호화해서 쿠키에 저장
- 만료 시간
    - 쿠키에는 만료 시간을 설정 시간이 지나면 자동으로 삭제
- 도메인 제한
    - 쿠키는 특정 웹사이트에서만 사용할 수 있도록 설정할 수 있음

### 쿠키와 개인정보 보호

- 많은 국가에서 쿠키 사용에 대한 사용자 동의를 요구하는 법규를 시행
- 웹사이트는 쿠키 정책을 명시하고, 필요한 경우 사용자의 동의를 얻어야 함

## Django에서의 세션 관리

### 세션 in Django

- Django는 ‘database-backed sessions’ 저장 방식을 기본 값으로 사용
- session 정보는 DB의 django_session 테이블에 저장
- Django는 요청 안에 특정 session id를 포함하는 쿠키를 사용해서 각각의 브라우저와 사이트가 연결된 session 데이터를 알아냄
- Django는 우리가 session 메커니즘(복잡한 동작 원리)에 대부분을 생각하지 않게끔 많은 도움을 줌

## ‘AbstractUser’ class

: 관리자 권한과 함께 완전한 기능을 가지고 있는 User model을 구현하는 추상 기본 클래스

### Abstract base classes(추상 기본 클래스)

- 몇 가지 공통 정보를 여러 다른 모델에 넣을 때 사용하는 클래스
- 데이터 베이스 테이블을 만드는 데 사용되지 않으며, 대신 다른 모델의 기본 클래스로 사용되는 경우 해당 필드가 하위 클래스의 필드에 추가 됨