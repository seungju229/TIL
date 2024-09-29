# ORM

- Object-Relational-Mapping
- 객체 지향 프로그래밍 언어를 사용하여 호환되지 않는 유형의 시스템 간에 데이터를 변환하는 기술
- Django 와 DB 간에 사용하는 언어가 다르기 때문에 Django 에 내장된 ORM이 중간에서 해석

# QuerySet API

- ORM 에서 데이터를 검색, 필터링, 정렬 및 그룹화 하는 데 사용하는 도구
    - API를 사용하여  SQL이 아닌 python 코드로 데이터를 처리
- python의 모델 클래스와 인스턴스를 활용해 DB에 데이터를 저장, 조회, 수정, 삭제 하는 것(CRUD)

## Query

- 데이터베이스에 특정한 데이터를 보여 달라는 요청
- “쿼리문을 작성한다.”
    - “원하는 데이터를 얻기 위해 데이터베이스에 요청을 보낼 코드를 작성한다.”
- 파이썬으로 작성한 코드가  ORM에 의해 SQL로 변환되어 데이터베이스에 전달되며, 데이터베이스의 응답 데이터를 ORM이 QuerySet이라는 자료 형태로 변환하여 우리에게 전달

## QuerySet

- 데이터베이스에서 전달받은 객체 목록
    - 순회가 가능한 1개 이상의 데이터
- Django ORM을 통해 만들어진 자료형
- 데이터베이스가 단일한 객체를 반환할 때는  QuerySet이 아닌 모델의 인스턴스로 반환됨

## QuerySet API 구문

### Article.objects.all()

### Article

- Model class

### objects

- Manager
- 뒤에 queryset api의 메서드를 보관하는 곳

### all()———-! 중요

- article 모델로 인해 생성된 테이블 데이터의 모든 것을 달라
- 전체 조회 구문

# QuerySet API  실습

## QuerySet API 실습 사전 준비

### 외부 라이브러리 설치 및 설정

```bash
$ pip install ipython django-extensions
```

```bash
# settings.py

INSTALLED_APPS = [
    'articles',
    'django_extensions',
    'django.contrib.admin',
    'django.contrib.auth',
    ...,
]
```

```bash
$ pip freeze > requirements.txt 
```

## Django shell 실행

```bash
$ python manage.py shell_plus
```

### Django shell

- Django 환경 안에서 실행되는 python shell
    - 입력하는 QuerySet Api 구문이 Django 프로젝트에 영향을 미침

## Create

### 방법 1

```bash
# 특정 테이블에 새로운 행을 추가하여 데이터 추가

In [1]: article = Article() # Article(class)로부터 article(instance) 생성

In [2]: article
Out[2]: <Article: Article object (None)>

In [3]: article.title = 'first' # 인스턴스 변수(title)에 값을 할당

In [4]: article.content = 'django!' # 인스턴스 변수(content)에 값을 할당

In [5]: article.title
Out[5]: 'first'

In [6]: article.content
Out[6]: 'django!'

In [8]: Article.objects.all()
Out[8]: <QuerySet []> # 데이터베이스로 저장하지 않아서 빈 쿼리셋
```

```bash
# 데이터베이스로 저장. save하지 않으면 아직 DB에 값이 저장되지 않음

In [9]: article.save()

In [10]: article
Out[10]: <Article: Article object (1)> # 숫자는 pk 값. id를 pk로 씀

In [12]: Article.objects.all()
	Out[12]: <QuerySet [<Article: Article object (1)>]> # 저장된 것 확인
```

### 방법2

```bash
# 초기 값 미리 설정

In [14]: article = Article(title = 'second', content = 'django!') 

In [15]: article
Out[15]: <Article: Article object (None)>

In [16]: article.title
Out[16]: 'second'

In [17]: article.content
Out[17]: 'django!'

	In [18]: article.save() # 저장

In [19]: article
Out[19]: <Article: Article object (2)>

In [20]: Article.objects.all()
Out[20]: <QuerySet [<Article: Article object (1)>, <Article: Article object (2)>]>
```

### 방법3

```bash
# create() 메서드 활용
# 위 두 가지 방법과 달리 바로 생성된 데이터 반환

In [21]: Article.objects.create(title = 'third', content = 'django!')
Out[21]: <Article: Article object (3)>
```

### save()

- 객체를 데이터베이스에 저장하는 인스턴스 메서드

## Read

### 대표적인 조회 메서드

- Return new QuerySets
    
    :조회할 결과가 없어도 QuerySet 반환함
    
    - all()
        - 전체 데이터 조회
        
        ```bash
        Article.objects.all()
        ```
        
    - filter()
        - 조회를 할 때 조건을 걸 수 있음
        - 주어진 매개변수와 일치하는 객체를 포함하는 QuerySet 반환
        
        ```bash
        # content 내용이 'django!'인 항목들을 조회하겠다.
        
        In [23]: Article.objects.filter(content = 'django!')
        Out[23]: <QuerySet [<Article: Article object (1)>, <Article: Article object (2)>, <Article: Article object (3)>]>
        
        # title이 'first'인 항목의 content 추출
        
        In [27]: article = Article.objects.filter(title = 'first') # article 변수에 할당
        
        In [28]: article
        Out[28]: <QuerySet [<Article: Article object (1)>]>
        
        In [29]: article[0]
        Out[29]: <Article: Article object (1)>
        
        In [30]: article[0].content
        Out[30]: 'django!'
        ```
        
- Do not return QuerySets
    
    : 단일 객체 조회
    
    - get()
        - 주어진 매개변수와 일치하는 객체를 반환
        - 조건에 맞는 객체가 **없다면** 예외 발생
        - 조회를 했을 때 객체가 **여러 개라면** 예외 발생
        - 위와 같은 특징 때문에 primary key와 같이 고유성을 보장하는 조회에서 사용해야 함
        
        ```bash
        In [24]: Article.objects.get(pk = 1)
        Out[24]: <Article: Article object (1)>
        
        In [25]: Article.objects.get(pk = 100)
        DoesNotExist: Article matching query does not exist.
        
        In [26]: Article.objects.get(content = 'django!')
        MultipleObjectsReturned: get() returned more than one Article -- it returned 3
        ```
        

## Update

```bash
In [32]: article = Article.objects.get(pk=2) # 조회 먼저(READ)

In [33]: article
Out[33]: <Article: Article object (2)>

In [34]: article.content
Out[34]: 'django!'

In [35]: article.content = 'ssafy'

In [36]: article.content
Out[36]: 'ssafy'

In [37]: article.save() # save 요청해야 db에 최종적으로 저장됨
```

## Delete

```bash
In [38]: article = Article.objects.get(pk=2)

In [39]: article.delete()
Out[39]: (1, {'articles.Article': 1})

In [41]: Article.objects.get(pk=2)
DoesNotExist: Article matching query does not exist.

# 지워진 번호는 재사용하지 않음. 3번 지우고 행 다시 만들어도 3번이 다시 만들어지지 않고 바로 4번으로 만들어짐
```

# ORM with view

## 전체 게시글 조회

```bash
# pjt urls.py

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('articles/', include('articles.urls')),
]
```

```bash
# articles urls.py

from django.urls import path
from . import views

app_name = 'articles'
urlpatterns = [
    path('', views.index, name = 'index'),
]
```

```bash
# views.py

from django.shortcuts import render
# 모델 클래스 가져오기
from .models import Article

# Create your views here.
def index(request):

    # 게시글 전체 조회 요청 to DB
    articles = Article.objects.all()
    context = {
        'articles':articles,
    }
    return render(request, 'articles/index.html',context)
```

```bash
# index.html

<body>
    <h1>Articles</h1>
    <p>{{articles}}</p>
    {% for article  in articles %}
        <p>글 번호: {{article.pk}}</p>
        <p>글 제목: {{article.title}}</p>
        <p>글 내용: {{article.content}}</p>
        <hr>
    {% endfor %}
    
</body>
```

# 참고

## Field lookups

- Query에서 조건을 구성하는 방법
- QuerySet 메서드 filter(), exclude() 및 get()에 대한 키워드 인자로 지정됨

```bash
# Field lookups 예시
# 내용에 'dja'가 포함된 모든 게시글 조회
Article.objects.filter(content__contains = 'dja')

# 제목이 he로 시작하는 모든 게시글 조회
Article.objects.filter(title__startswith = 'he')
```

## ORM, QuerySet API를 사용하는 이유

1. 데이터베이스 추상화
    - 개발자는 특정 데이터베이스 시스템에 종속되지 않고 일관된 방식으로 데이터를 다룰 수 있음
2. 생산성 향상
    - 복잡한 SQL 쿼리를 직접 작성하는 대신 Python 코드로 데이터베이스 작업을 수행할 수 있음
3. 객체 지향적 접근
    - 데이터베이스 테이블을 python 객체로 다룰 수 있어 객체 지향 프로그래밍의 이점을 활용할 수 있음