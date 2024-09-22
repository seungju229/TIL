# Template System

## Django Template system

### HTML의 콘텐츠를 변수 값에 따라 변경하기

```python
# views.py
def index(request):
    context = {
        'name': 'alice',
    }
    return render(request, 'articles/index.html', context)
```

```bash
# template
<body>
  <h1>안녕하세요! {{ name }}</h1>
</body>
```

## Django Template Language(DTL)

- Template에서 조건, 반복, 변수 등의 프로그래밍적 기능을 제공하는 시스템

### DTL Syntax

1. Variable
    - render 함수의 세번째 인자로 딕셔너리 데이터 사용
    - 딕셔너리 key에 해당하는 문자열이 template에서 사용 가능한 변수명이 됨
    - dot(’.’)을 사용하여 변수 속성에 접근할 수 있음
2. Filters
    - 표시할 변수를 수정할 때 사용(변수 + ‘|’ + 필터)
    - chained(연결)이 가능하며 일부 필터는 인자를 받기도 함
    - 약 60개의 built-in template filters를 제공
3. Tags
    - 반복 또는 논리를 수행하여 제어 흐름을 만듦
    - 일부 태그는 시작과 종료 태그가 필요
    - 약 24개의 built-in template tags를 제공
4. comments (주석)
    
    ```bash
     {% comment %} 
     ...
     {% endcomment %}
    ```
    

### DTL 예시

```bash
# urls.py
urlpatterns = [
    path('index/', views.index),
    path('dinner/', views.dinner),
]
```

```bash
# views.py
import random

def dinner(request):
    foods = ['국밥', '국수', '카레', '탕수육',]
    picked = random.choice(foods)
    context = {
        'foods': foods,
        'picked': picked,
    }
    return render(request, 'articles/dinner.html', context)
```

```bash
# articles/dinner.html

<p>{{ picked }} 메뉴는 {{ picked|length }}글자 입니다.</p>
  <ul>
    {% for food in foods %}
      <li>{{ food }}</li>
    {% endfor %}
  </ul>

  {% if foods|length == 0 %}
    <p>메뉴가 소진 되었습니다.</p>
  {% else %}
    <p>아직 메뉴가 남았습니다.</p>
  {% endif %}
{% endblock content %}
```

# 템플릿 상속

- 페이지의 공통 요소를 포함하고, 하위 템플릿이 재정의 할 수 있는 공간을 정의하는 기본 ‘skeleton’ 템플릿을 작성하여 상속 구조를 구축

## 상속 구조 만들기

- skeleton 역할을 하게 되는 상위 템플릿(base.html) 작성

```bash
# 상위 템플릿

<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Bootstrap demo</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
  </head>
  <body>
    {% block content %}
    {% endblock content %}
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
  </body>
</html>

```

```bash
# 하위 템플릿

{% extends "articles/base.html" %}

{% block content %}
  <h1>Catch</h1>
  <p>당신이 입력한 데이터는 {{ message }}입니다.</p>
{% endblock content %}
```

## 상속 관련 DTL 태그

- 페이지의 공통 요소를 포함하고, 하위 템플릿이 재정의 할 수 있는 공간을 정의하는 기본 ‘skeleton’템플릿을 작성하여 상속 구조를 구축
- ‘extends’ tag
    - 자식(하위) 템플릿이 부모 템플릿을 확장한다는 것을 알림
    - 반드시 자식 템플릿 최상단에 작성되어야 함(2개 이상 사용 불가)
- ‘block’ tag
    - 하위 템플릿에서 재정의 할 수 있는 블록을 정의
    - 상위 템플릿에 작성하며 하위 템플릿이 작성할 수 있는 공간을 지정하는 것

# HTML form

## 요청과 응답

### 데이터를 보내고 가져오기

- HTML ‘form’ element를 통해 사용자와 애플리케이션 간의 상호작용 이해하기
- HTML ‘form’은 HTTP 요청을 서버에 보내는 가장 편리하고 안전한 방법

```bash
{% extends "articles/base.html" %}

{% block content %}
  <h1>Fake Naver</h1>
  <form action="https://search.naver.com/search.naver" method="GET">
    <input type="text" name="query">
    <input type="submit">
  </form>
{% endblock content %}
```

## ‘form’ element

### form의 핵심 속성 2가지

1. action
    - 데이터를 어디로
    - 입력 데이터가 전송될 URL 지정(목적지)
    - 이 속성을 지정하지 않으면 데이터는 현재 form이 있는 페이지의 URL로 보내짐
2. method
    - 어떤 방식으로 요청할지
    - 데이터를 어떤 방식으로 보낼 것인지 정의
    - 데이터의 HTTP request methods(GET, POST)를 지정

## ‘input’ element

- 사용자의 데이터를 입력 받을 수 있는 요소
- type 속성 값에 따라 다양한 유형의 입력 데이터를 받음

### ‘name’ attribute

- input의 핵심 속성
- 사용자가 입력한 데이터에 붙이는 이름(key)
- 데이터를 제출했을 때 서버는 name 속성에 설정된 값을 통해서만 사용자가 입력한 데이터에 접근할 수 있음

### Query String Parameters

- 사용자의 입력 데이터를 URL 주소에 파라미터를 통해 서버로 보내는 방법
- 문자열은 앰퍼샌드(’&’)로 연결된 key = value 쌍으로 구성되며, 기본 URL과는 물음표(’?’)로 구분됨

```bash
https://search.naver.com/search.naver?where=nexearch&sm=top_hty&fbm=0&ie=utf8&query=%EC%8B%B8%ED%94%BC
```

## form 활용

### 1. throw 로직 작성

```bash
# urls.py

urlpatterns = [
    path('throw/', views.throw),
]
```

```bash
# views.py

def throw(request):
    return render(request, 'articles/throw.html')
```

```bash
# throw.html

{% extends "articles/base.html" %}

{% block content %}
  <h1>Throw</h1>
  {% comment %} <form action="http://127.0.0.1:8000/catch/" method="GET"> {% endcomment %}
  <form action="/articles/catch/" method="GET">
    <input type="text" name="message">
    <input type="submit">
  </form>
{% endblock content %}
```

### 2. catch 로직 작성

```bash
# urls.py

urlpatterns = [
    path('catch/', views.catch),
]
```

```bash
# views.py

def catch(request):
    # 사용자가 요청보낸 데이터를 추출해서 context 딕셔너리에 세팅
    message = request.GET.get('message')
    context = {
        'message': message,
    }
    return render(request, 'articles/catch.html', context)
```

```bash
{% extends "articles/base.html" %}

{% block content %}
  <h1>Catch</h1>
  <p>당신이 입력한 데이터는 {{ message }}입니다.</p>
{% endblock content %}
```

# Django URLs

### URL dispatcher

- URL 패턴을 정의하고 해당 패턴이 일치하는 요청을 처리할 view 함수를 연결(매핑)

## Variable Routing

### 현재 URL 관리의 문제점

- 템플릿의 많은 부분이 중복되고, URL의 일부만 변경되는 상황

### Variable Routing

- URL 일부에 변수를 포함시키는 것
- 변수는 view 함수의 인자로 전달할 수 있음

### Variable routing 작성법

```bash
path('articles/<int:num>/', views.detail)
path('hello/<strt:name>/', views.greeting)
```

### Path converters

- URL 변수의 타입을 지정(str, int 등 5가지 타입 지원)

```bash
# urls.py

urlpatterns = [
    path('hello/<str:name>/', views.greeting),
]
```

```bash
# view.py

def greeting(request, name):
    context = {
        'name': name,
    }
return render(request, 'articles/greeting.html', context)
```

```bash
{% extends "articles/base.html" %}

{% block content %}
  <h1>Greeting</h1>
  <p>{{ name }}님 안녕하세요!</p>
{% endblock content %}
```

## App과 URL

### App URL mapping

- 각 앱에 URL을 정의하는 것
- 프로젝트와 각 앱이 URL을 나누어 관리를 편하게 하기 위함

### 두 번째 앱 pages 생성 후 발생할 수 있는 문제

- view 함수 이름이 같거나 같은 패턴의 URL 주소를 사용하게 되는 경우
    
     —> “URL을 각자 app에서 관리하자”
    

```bash
# firstpjt/urls.py

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path("admin/", admin.site.urls),
    path('articles/', include('articles.urls')),
]
```

```bash
# articles/urls.py

from django.urls import path
# 명시적 상대경로
from . import views

urlpatterns = [
    path('index/', views.index),
    path('dinner/', views.dinner),
    path('search/', views.search),
    path('throw/', views.throw),
    path('catch/', views.catch),
    path('hello/<str:name>/', views.greeting),
]
```

### include()

- 프로젝트 내부 앱들의 URL을 참조할 수 있도록 매핑하는 함수
- URL의 일치하는 부분까지 잘라내고, 남은 문자열 부분은 후속 처리를 위해 include된 URL로 전달

# URL 이름 지정

## Naming URL patterns

### url 구조 변경에 따른 문제점

- 기존 ‘articles/’ 주소가 ‘articles/index/’로 변경됨에 따라 해당 url을 사용하는 모든 위치를 찾아가 변경해야 함
- URL에 이름을 지정해서 해결
    - path 함수의 name 인자를 정의해서 사용

## DTL URL tag

# URL 이름 공간

## App_name 속성