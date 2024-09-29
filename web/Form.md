# Django Form

- 사용자 입력 데이터를 수집하고, 처리 및 유효성 검사를 수행하기 위한 도구
- 유효성 검사를 단순화하고 자동화할 수 있는 기능 제공

## HTML ‘form’

- 지금까지 사용자로부터 데이터를 제출받기 위해 활용한 방법
- 그러나 비정상적 혹은 악의적인 요청을 필터링 할 수 없음
    - 유효한 데이터인지 확인 필요!!
    
    ## 유효성 검사
    
    - 수집한 데이터가 정확하고 유효한지 확인하는 과정
    - 입력 값, 형식, 중복, 범위, 보안 등 많은 것을 고려해야 함
    - 이런 과정과 기능을 직접 개발하는 것이 아닌 Django가 제공하는 Form 사용

## Form class

```python
# articles/forms.py

from django import forms

class ArticleForm(forms.Form):
    title = forms.CharField(max_length=10)
    content = forms.CharField()
```

```python
# articles/views.py

from .forms import ArticleForm

def new(request):
    form = ArticleForm()
    context = {
        'form': form,
    }
    return render(request, 'articles/new.html', context)
```

```bash
# articles/new.html

<body>
  <h1>New</h1>
  <form action="{% url "articles:create" %}" method="POST">
    {% csrf_token %}
    {{ form }}
    <input type="submit">
  </form>
</body>
```

### Form rendering options

- label, input 쌍을 특정 HTML 태그로 감싸는 옵션
    
    ```python
    # articles/new.html
    
    <body>
      <h1>New</h1>
      <form action="{% url "articles:create" %}" method="POST">
        {% csrf_token %}
        {{ form.as_p }}
        <input type="submit">
      </form>
    </body>
    ```
    

## Widgets

- HTML ‘input’ element의 표현을 담당
- input 요소의 속성 및 출력 되는 부분을 변경하는 것

```python
# articles/forms.py

from django import forms

class ArticleForm(forms.Form):
    title = forms.CharField(max_length=10)
    content = forms.CharField(widget=forms.Textarea)
```

# Django ModelForm

### Form

- 사용자 입력 데이터를 DB에 저장하지 않을 때
- 검색, 로그인

### ModelForm

- 사용자 입력 데이터를 DB에 저장해야 할 때
- 게시글 작성, 회원가입
- Model 과 연결된 Form을 자동으로 생성해주는 기능 제공
    - Form + Model
    
    ```python
    # articles/forms.py
    
    from django import forms
    from .models import Article
    
    class ArticleForm(forms.ModelForm):
        class Meta:                 # 파이썬의 inner class와 같은 문법적인 관점으로 접근하지 말 것
            model = Article         # models.py에 Article 모델 정의됨
            fields = '__all__'      # field라고 하면 인식 못 함
    ```
    
- 전에는 charfield에서 widget 설정을 해야 textfield로 바뀌었는데, meta data를 이용하면 모델 등록만 해주면 되니까 간단함

## Meta class

- model form의 정보를 작성하는 공간

`meta data`

- 데이터의 데이터
- ex) 사진이라는 데이터 안에 (노출 값, 위치, 조리개 값, 조도, 형식 등)의 데이터가 있음
- 이러한 데이터가 사진의 meta data
- 모델 폼의 정보를 작성하기 때문에 meta class(모델 폼의 정보를 작성하는 공간)

### ‘fields’ 및 ‘exclude’ 속성

- exclude 속성을 사용해서 모델에서 포함하지 않을 필드를 지정할 수도 있음

```python
# articles/forms.py

from django import forms
from .models import Article

class ArticleForm(forms.ModelForm):
    class Meta:
        model = Article         
        exclude = ('title',)    # title field는 포함하지 않겠다.
```

## ModelForm 적용

- 유효성 검사를 위해 create view 함수에서 저장 2번 함수 사용(save하기 전에 유효성 검사하기 위해)

```python
#articles/views.py

from django.shortcuts import render, redirect
from .forms import ArticleForm

def create(request):
		form = ArticleForm(request.POST)
    if form.is_valid():
        article = form.save()
        return redirect('articles:detail', article.pk)
    context = {
        'form': form,
    }
    return render(request, 'articles/new.html', context)

# is_valid()하나로 내부적으로 유효성 검사 다 진행
# 통과하면 save 하고 redirect
# 성공하면 게시글 하나가 return. save 결과가 instance 하나 생성
# 실패하면 페이지를 다시 렌더링, 실패한 이유 데이터를 같이 받음
```

### 공백 데이터가 유효하지 않은 이유와 에러 메세지가 출력 되는 과정

- 별도로 명시하지 않았지만 모델 필드에는 기본적으로 빈 값은 허용하지 않는 제약 조건이 설정되어 있음
- 빈 값은 is_valid()에 의해 False로 평가되고 form 객체에는 그에 맞는 에러 메세지가 포함되어 다음 코드로 진행됨

### ModelForm을 적용한 edit 로직

```python
# articles/views.py

def edit(request, pk):
    article = Article.objects.get(pk=pk)
    form = ArticleForm(instance=article)      # 인스턴스 데이터 넣어야 기존 정보가 나옴
    context = {
        'article': article,
        'form': form,
    }
    return render(request, 'articles/edit.html', context)
```

```html
# articles/edit.html
<body>
  <h1>Update</h1>
  <form action="{% url "articles:update" article.pk %}" method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <input type="submit" value="수정">
  </form>
</body>
```

### ModelForm을 적용한 update 로직

```python
# articles/view.py

def update(request, pk):
    article = Article.objects.get(pk=pk)
    # 1. 모델폼 인스턴스 생성 (+사용자 입력 데이터 & 기존 데이터)
    form = ArticleForm(request.POST, instance=article) 
    # 인스턴스 데이터 넣어야 기존 정보가 나옴. 안 넣으면 create랑 같음
    
    # 2. 유효성 검사
    if form.is_valid():
        form.save()
        return redirect('articles:detail', article.pk)
    context = {
        'article': article,
        'form': form,
    }
    return render(request, 'articles/edit.html', context)

```

### save()

: 데이터베이스 객체를 만들고 저장하는 ModelForm의 인스턴스 메서드

- 키워드 인자 instance 여부를 통해 생성과 수정을 결정
    
    ```python
    # create
    
    form = ArticleForm(request.POST)
    form.save()
    ```
    
    ```python
    # update
    
    form = ArticleForm(request.POST, instance = article)
    form.save()
    ```
    

# HTTP 요청 다루기

## View 함수 구조 변화

- new & create view 함수 모두 데이터 생성을 구현하는 데 활용되지만, new는 **GET** method 요청만을, create는 **POST** method 요청만을 처리함
- 이 두 함수를 하나로 구조화

## new & create 함수 결합

```python
# articles/views.py

def create(request):
    # 요청 메서드가 POST일 때
    if request.method == 'POST':
        form = ArticleForm(request.POST)
        if form.is_valid():
            article = form.save()
            return redirect('articles:detail', article.pk)
    # 요청 메서드가 POST가 아닐 때(GET, PUT, DELETE 등 다른 메서드)
    else:
        form = ArticleForm()
    context = {
        'form': form,
    }
    return render(request, 'articles/create.html', context)
    # render에서 new 템플릿을 create 템플릿으로 변경
```

```python
#articles/urls.py

# 기존 new 관련 코드 수정
# 사용하지 않게 된 new url 제거 

from django.urls import path
from . import views

app_name = 'articles'
urlpatterns = [
    path('', views.index, name='index'),
    path('<int:pk>/', views.detail, name='detail'),
    # path('new/', views.new, name='new'),
    path('create/', views.create, name='create'),
    path('<int:pk>/delete/', views.delete, name='delete'),
    path('<int:pk>/update/', views.update, name='update'),
]
```

```html
# articles/index.html
# articles/create.html
# new 관련 키워드를 create로 변경

<body>
  <h1>Create</h1>
  <form action="{% url "articles:create" %}" method="POST">
    {% csrf_token %}    
    {{ form.as_p }}
    <input type="submit">
  </form>
</body>
```

## edit & update 함수 결합

- new & create 와 똑같..

# 참고

## ModelForm의 키워드 인자 구성

```python
# articles/views.py

form = ArticleForm(request.POST, instance = article)
```

- data 는 첫번째에 위치한 키워드 인자이기 때문에 생략 가능
- instance는 9번째에 위치한 키워드 인자이기 때문에 생략 불가

## Widgets 응용

- widgets 사용하려면 forms.ModelForm 상속 받아야 함