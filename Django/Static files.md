# Static files

- 서버 측에서 변경되지 않고 고정적으로 제공되는 파일
- 이미지, JS, CSS 파일 등

## Static files 제공하기

- 웹 서버의 기본 동작은 특정 위치(URL)에 있는 자원을 요청(HTTP request) 받아서 응답(HTTP response)을 처리하고 제공하는 것
- 웹 서버는 요청 받은 URL로 서버에 존재하는 정적 자원을 제공
- 정적 파일을 제공하기 위한 경로(URL)가 있어야 함

## Static files 기본 경로

- articles/static/articles/ 경로에 이미지 파일 배치
- static files 경로는 DTL의 static tag를 사용해야 함
    - built-in tag 가 아니어서 load tag를 사용해서 import 후 사용 가능.
    - load 아래부터 static tag 사용 가능
- 약속된 이후의 정적 파일의 경로 사용(확장자명까지 작성)
- base.html에서 load static을 해도 자식 템플릿에서 static 태그를 사용하려면 다시 load static 해야됨(상속 X)

```html
# articles/index.html

{% load static %}

<img src="{% static "articles/sample-1.png" %}" alt="sample-image">
```

### STATIC_URL

- 기본 경로 및 추가 경로에 위치한 정적 파일을 참조하기 위한 URL
    - 실제 파일이나 디렉토리 경로가 아니며, URL로만 존재
    
    ```python
    # settings.py
    
    STATIC_URL = 'static/'
    ```
    

- URL + STATIC_URL + 정적파일 경로
    - http://127.0.0.1:8000/static/articles/sample-1.png

## Static files 추가 경로

- STATICFILES_DIRS 에 문자열 값으로 추가 경로 설정

### STATICFILES_DIRS

- 정적 파일의 기본 경로 외에 추가적인 경로 목록을 정의하는 리스트

### 추가 경로 static file 제공하기

1. 임의의 추가 경로 설정

```python
# settings.py

STATICFILES_DIRS = [
    # Python 객체지향 경로 시스템
    BASE_DIR / 'static',
]
# 시작점부터 /로 뻗어 나감
# 추가 경로는 BASE_DIR 이후에 static 폴더
```

1. 추가 경로에 이미지 파일 배치
2. static tag 를 이용해 이미지 파일에 대한 경로 제공

```python
# articles/index.html

<img src="{% static "sample-2.png" %}" alt="sample-img">
```

1. 이미지 제공 받기 위해 요청하는 Request URL 확인

# Media files

- **사용자가** 웹에서 업로드하는 정적 파일

## 이미지 업로드

### ImageField()

- 이미지 업로드에 사용하는 모델 필드
    - 이미지 객체가 직접 DB에 저장되는 것이 아닌 ‘이미지 파일의 경로’ 문자열이 저장됨

### 미디어 파일을 제공하기 전 준비사항

1. settings.py에 MEDIA_ROOT, MEDIA_URL 설정
    
    ### MEDIA_ROOT
    
    - 미디어 파일들이 위치하는 디렉토리의 절대 경로
    
    ```python
    # settings.py
    
    MEDIA_ROOT = BASE_DIR/'media'
    ```
    
    ### MEDIA_URL
    
    - MEDIA_ROOT에 제공되는 미디어 파일에 대한 주소 생성(STATIC_URL과 동일한 역할)
    
    ```python
    # settings.py
    
    MEDIA_URL = 'media/'
    ```
    
2. 작성한 MEDIA_ROOT와 MEDIA_URL에 대한 URL 지정
    
    ```python
    # crud/urls.py
    
    from django.contrib import admin
    from django.urls import path, include
    from django.conf import settings 
    from django.conf.urls.static import static
    
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('articles/', include('articles.urls')),
    ] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
    
    # static(URL주소, 미디어 파일의 실제 위치)
    # static은 path경로가 아니어서 리스트 밖으로 빼는 것이 일반적임
    # 이게 추가가 돼야 사용자가 업로드 하고 나서 url제공받을 수 있음
    ```
    

### 업로드

1. models. py 에 image field 추가
    
    ```python
    # models.py
    # ImageField는 빈 값 허용해야 함 (이미지를 업로드 안 할 수도 있으니까)
    # 기존 필드 사이에 작성해도 실제 테이블 생성 시에는 가장 우측(뒤)에 추가됨
    
    class Article(models.Model):
        title = models.CharField(max_length=10)
        content = models.TextField()
        image = models.ImageField(blank=True)
        created_at = models.DateTimeField(auto_now_add=True)
        updated_at = models.DateTimeField(auto_now=True)
    ```
    
2. migration 진행
    - pillow 라이브러리 설치해야 ImageField 사용 가능(설치 이후 requirements.txt 업데이트)
    
    ```python
    pip install pillow
    ```
    
3. form 요소의 enctype 속성 추가
    - enctype은 데이터 전송 방식을 결정하는 속성
    
    ```html
    <h1>Create</h1>
      <form action="{% url "articles:create" %}" method="POST" enctype="multipart/form-data">
        {% csrf_token %}
        {{ form.as_p }}
        <input type="submit">
      </form>
    ```
    
4. ModelForm의 2번째 인자로 요청 받은 파일 데이터 작성
    - ModelForm의 상위 클래스 BaseModelForm의 생성자 함수의 2번째 위치 인자로 파일을 받도록 설정되어 있음
    
    ```python
    # articles/views.py
    
    def create(request):
        if request.method == 'POST':
            form = ArticleForm(request.POST, request.FILES)
    ...
    ```
    

## 업로드 이미지 제공

1. ‘url’ 속성 변경
    
    ```python
    # articles/detail.html
    
    <img src="{{ article.image.url }}" alt="image">
    
    # article.image.url : 업로드 파일의 경로
    # article.image : 업로드 파일의 파일 이름
    
    ```
    
2. 이미지 데이터가 있는 경우에만 이미지 출력할 수 있도록 처리
    
    ```python
    # articles/detail.html
    
    {% if article.image %}
        <img src="{{ article.image.url }}" alt="image">
    {% endif %}
    ```
    

## 업로드 이미지 수정

1. 수정 페이지 form 요소에 enctype 속성 추가
    
    ```python
    # articles/update.html
    
     <h1>Update</h1>
      <form action="{% url "articles:update" article.pk %}" method="POST" **enctype="multipart/form-data"**>
        {% csrf_token %}
        {{ form.as_p }}
        <input type="submit" value="수정">
      </form>
    ```
    
2. update view 함수에서 업로드 파일에 대한 추가 코드 작성
    
    ```python
    # articles/views.py
    
    def update(request, pk):
        article = Article.objects.get(pk=pk)
        if request.method == 'POST':
            form = ArticleForm(request.POST, **request.FILES**, instance=article)
    ...
    ```