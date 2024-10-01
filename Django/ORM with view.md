# Read

## 단일 게시글 조회

```bash
# articles/urls.py

app_name = 'articles'
urlpatterns = [
    path('', views.index, name='index'),
    path('<int:pk>/', views.detail, name = 'detail'),
]
```

```bash
# articles/views.py

def detail(request, pk):
    # url로부터 전달받은 pk를 활용해 데이터를 조회
    # article = Article.objects.get(id=pk)
    article = Article.objects.get(pk=pk)
    context = {
        'article': article,
    }
    return render(request, 'articles/detail.html', context)
    
def index(request):
    # 게시글 전체 조회 요청 to DB
    articles = Article.objects.all()
    context = {
        'articles': articles,
    }
    return render(request, 'articles/index.html', context)
```

```bash
# templates/articles/detail.html

<body>
  <h1>Detail</h1>
  <h3>{{ article.pk }}번째 글</h3>
  <hr>
  <p>제목: {{ article.title }}</p>
  <p>내용: {{ article.content }}</p>
  <p>작성일: {{ article.created_at }}</p>
  <p>수정일: {{ article.updated_at }}</p>
  <hr>
  <a href="{% url "articles:index" %}">[back]</a>
</body>

# templates/articles/index.html

<body>
  <h1>Articles</h1>
 
  {% for article in articles %}
    <p>글 번호: {{ article.pk }}</p>
    <a href="{% url "articles:detail" article.pk %}">
      <p>글 제목: {{ article.title }}</p>
    </a>
    <p>글 내용: {{ article.content }}</p>
    <hr>
  {% endfor %}

</body>
```

# Create

- 사용자 입력 데이터를 받을 페이지를 렌더링하는 `new` view 함수
- 사용자가 입력한 요청 데이터를 받아 DB에 저장하는 `create` view 함수

## 1. new 기능 구현

```bash
# articles/urls.py

app_name = 'articles'
urlpatterns = [
		...
    path('new/', views.new, name='new'),
]
```

```bash
# articles/views.py

def new(request):
    # 게시글 작성 페이지 응답
    return render(request, 'articles/new.html')
```

```bash
# templates/articles/new.html

<body>
  <h1>New</h1>
  <form action="{% url "articles:create" %}" method="POST">
    {% csrf_token %}
    <input type="text" name="title">
    <textarea name="content" id=""></textarea>
    <input type="submit">
  </form>
</body>
```

## 2. create 기능 구현

```bash
# articles/urls.py

app_name = 'articles'
urlpatterns = [
		...
    path('create/', views.create, name='create'),
]
```

```bash
# articles/views.py

def create(request):
    # 1. 사용자 요청으로부터 입력 데이터를 추출
    title = request.POST.get('title')
    content = request.POST.get('content')

    # 저장 1
    # article = Article()
    # article.title = title
    # article.content = content
    # article.save()

    # 저장 2 
    article = Article(title=title, content=content)
    article.save()

    # 저장 3
    # Article.objects.create(title=title, content=content)

    # return redirect('articles:index')
    return redirect('articles:detail', article.pk)

def detail(request, pk):
    # url로부터 전달받은 pk를 활용해 데이터를 조회
    # article = Article.objects.get(id=pk)
    article = Article.objects.get(pk=pk)
    context = {
        'article': article,
    }
    return render(request, 'articles/detail.html', context)
```

```bash
# templates/articles/detail.html

<body>
  <h1>Detail</h1>
  <h3>{{ article.pk }}번째 글</h3>
  <hr>
  <p>제목: {{ article.title }}</p>
  <p>내용: {{ article.content }}</p>
  <p>작성일: {{ article.created_at }}</p>
  <p>수정일: {{ article.updated_at }}</p>
  <hr>
  <form action="{% url "articles:delete" article.pk %}" method="POST">
    {% csrf_token %}
    <input type="submit" value="삭제">
  </form>
  <a href="{% url "articles:index" %}">[back]</a>
</body>
```

# Redirect

- 서버는 데이터 저장 후 페이지를 응답하는 것이 아닌 사용자를 적절한 기존 페이지로 보내야 한다.
- 사용자가 GET 요청을 한번 더 보내도록 해야 함
- 서버가 클라이언트를 직접 다른 페이지로 보내는 것이 아님
- redirect() : 클라이언트가 인자에 작성된 주소로 다시 요청을 보내도록 하는 함수

# HTTP request methods

## GET method

- 서버로부터 데이터를 요청하고 받아오는 데**(조회)** 사용
- 검색 쿼리 전송
- 웹 페이지 요청
- api에서 데이터 조회

### 특징

1. 데이터 전송
    - url의 쿼리 문자열을 통해 데이터를 전송
2. 데이터 제한
    - url 길이에 제한이 있어 대량의 데이터 전송에는 적합하지 않음
3. 브라우저 히스토리
    - 요청 url이 브라우저 히스토리에 남음
4. 캐싱(get에서만 가능)
    - 브라우저는 get 요청의 응답을 로컬에 저장할 수 있음
    - 동일한 url로 다시 요청할 때, 서버에 접속하지 않고 저장된 결과를 사용
    - 페이지 로딩 시간을 크게 단축

## POST method

- 서버에 데이터를 제출하여 리소스를 변경**(생성, 수정, 삭제)**하는 데 사용
- 로그인 정보 제출
- 파일 업로드
- 새 데이터 생성(ex. 새 게시글 작성)
- api에서 데이터 변경 요청

### 특징

1. 데이터 전송
    - http body를 통해 데이터 전송
2. 데이터 제한
    - get에 비해 더 많은 양의 데이터 전송할 수 있음
3. 브라우저 히스토리
    - post 요청은 브라우저 히스토리에 남지 않음
4. 캐싱
    - post 요청은 기본적으로 캐시할 수 없음
    - post 요청이 일반적으로 서버의 상태를 변경하는 작업을 수행하기 때문

# HTTP response status code

: 서버가 클라이언트의 요청에 대한 처리 결과를 나타내는 3자리 숫자

## 403 Forbidden

: 서버에 요청이 전달되었지만, 권한 때문에 거절되었음을 의미

## CSRF(Cross-Site-Request-Forgery)

: 사이트 간 요청 위조

- 사용자가 자신의 의지와 무관하게 공격자가 의도한 행동을 하여 특정 웹 페이지를 보안에 취약하게 하거나 수정, 삭제 등의 작업을 하게 하는 공격 방법

## CSRF Token 적용

- Django 서버는 해당 요청이 db에 데이터를 하나 생성하는 요청에 대해 “Django가 직접 제공한 페이지에서 데이터를 작성하고 있는 것인지”에 대한 확인 수단이 필요
- 겉모습이 똑같은 위조 사이트나 정상적이지 않은 요청에 대한 방어 수단
- 요청 데이터 + **인증 토큰** → 게시글 작성
- DTL의 **CSRF_token 태그**를 사용해 사용자에게 토큰 값 부여
- 요청 시 토큰 값도 함께 서버로 전송될 수 있도록 하는 것

# Delete

## Delete 기능 구현

```bash
# articles/urls.py

urlpatterns = [
		...
    path('<int:pk>/delete/', views.delete, name='delete'),
]
```

```bash
# articles/views.py

def delete(request, pk):
    # 어떤 게시글 삭제할지 조회
    article = Article.objects.get(pk=pk)

    # 조회한 게시글 삭제
    article.delete()
    return redirect('articles:index')
```

```bash
# templates/articles/detail.html

<body>
  <h1>Detail</h1>
  ...
  <hr>
  <form action="{% url "articles:delete" article.pk %}" method="POST">
    {% csrf_token %}
    <input type="submit" value="삭제">
  </form>
  <a href="{% url "articles:index" %}">[back]</a>
</body>
```

# Update

- 사용자 입력 데이터를 받을 페이지를 렌더링하는 `edit` view 함수
- 사용자가 입력한 데이터를 받아 DB에 저장하는 `update` view 함수

## 1. edit 기능 구현

```bash
# articles/urls.py

urlpatterns = [
		...
    path('<int:pk>/edit/', views.edit, name='edit'),
]
```

```bash
# articles/views.py

def edit(request, pk):
    # 어떤 게시글을 수정할지 조회
    article = Article.objects.get(pk=pk)
    context = {
        'article': article,
    }
    return render(request, 'articles/edit.html', context)
```

```bash
# templates/articles/edit.html

<body>
  <h1>Edit</h1>
  <form action="{% url "articles:update" article.pk %}" method="POST">
    {% csrf_token %}
    <input type="text" name="title" value="{{ article.title }}">
    <textarea name="content" id="">{{ article.content }}</textarea>
    <input type="submit" value="수정">
  </form>
</body>
```

## 2. update 기능 구현

```bash
# articles/urls.py

urlpatterns = [
		...
    path('<int:pk>/update/', views.update, name='update'),
]
```

```bash
# articles/views.py

def update(request, pk):
    # 1. 어떤 게시글 수정할지 조회
    article = Article.objects.get(pk=pk)
    # 2. 사용자로부터 받은 새로운 입력 데이터 추출
    title = request.POST.get('title')
    content = request.POST.get('content')
    # 3. 기존 게시글의 데이터를 사용자로 받은 데이터로 새로 할당
    article.title = title
    article.content = content
    # 4. 저장
    article.save()

    return redirect('articles:detail', article.pk)
```