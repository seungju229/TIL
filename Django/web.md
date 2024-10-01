# Web Application

## 클라이언트와 서버

- 클라이언트
    - 서비스를 요청하는 주체(웹 사용자의 인터넷이 연결된 장치, 웹 브라우저)
- 서버
    - 클라이언트의 요청에 응답하는 주체
    - 웹 페이지, 앱을 저장하는 컴퓨터

## 우리가 웹 페이지를 보게 되는 과정

1. 웹 브라우저(클라이언트)에서 ‘google.com’을 입력 후 엔터
2. 웹 브라우저는 인터넷에 연결된 전세계 어딘가에 있는 구글 컴퓨터(서버)에게 ‘메인 홈페이지.html’파일을 달라고 요청
3. 요청 받은 구글 컴퓨터는 데이터베이스에서 ‘메인 홈페이지.html’ 파일을 찾아 응답
4. 웹 브라우저는 전달받은 ‘메인 홈페이지.html’파일을 사람이 볼 수 있도록 해석해주고 사용자는 구글의 메인 페이지를 보게 됨

## Frontend & Backend

- Frontend(프론트엔드)
    - 사용자 인터페이스(UI)를 구성하고, 사용자가 애플리케이션과 상호작용할 수 있도록 함
    - HTML, CSS, JavaScript, 프론트 프레임워크 등
- Backend(백엔드)
    - 서버 측에서 동작하며, 클라이언트의 요청에 대한 처리와 데이터베이스와의 상호작용 등을 담당
    - 서버언어(Python, Java 등) 및 백엔드 프레임워크, 데이터베이스, API, 보안 등

# Framework

## Django Framework

- python 기반의 대표적인 웹 프레임워크
- 대규모 트래픽 서비스에서도 안정적인 서비스 제공

## 가상 환경

- python 애플리케이션과 그에 따른 패키지들을 격리하여 관리할 수 있는 독립적인 실행 환경

### 가상 환경이 필요한 시나리오

1. 한 개발자가 2개의 프로젝트 A, B를 진행할 때,  프로젝트 A와 B가 각각 다른 requests 패키지 버전을 사용해야 한다고 가정. 파이썬 환경에서는 패키지는 1개의 버전만 존재할 수 있으므로 다른 패키지 버전 사용을 위한 독립적인 개발 환경이 필요함.
2. 한 개발자가 2개의 프로젝트 A, B를 진행할 때, 각각 water와 fire이라는 패키지 사용한다고 가정. 파이썬 환경에서 water와 fire 패키지를 함께 사용하면 충돌이 발생하기 때문에 설치할 수 없으므로 패키지 충돌을 피하기 위해 각각 독립적인 개발 환경 필요

### 1. 새로운 가상환경 “생성”

```bash
python -m venv venv

# venv라는 이름의 가상환경 생성
# 임의 이름으로 생성이 가능하나 관례적으로 venv 이름을 사용
```

### 2. 가상환경 “실행”

```bash
source venv/Scripts/activate

# 새롭게 생성한 venv라는 가상환경에서 개발 시작해야됨.
# 가상환경을 켜야 함. (in / out 개념이 아니라 on / off개념)
# 가상환경을 on 하면 아래 (venv)가 생김.
```

### 3. (선택사항) 가상환경 실행여부 확인

```bash
pip list
```

## 패키지 설치

### 1. 패키지 목록 생성 (requirements.txt)

```bash
pip freeze > requirements.txt
```

### 2. 패키지 목록 기반 설치

```bash
pip install -r requirements.txt

# requirements.txt를 읽어서 설치하겠다.
```

### 패키지 목록이 필요한 이유

- 만약 2명의 개발자가 하나의 프로젝트를 함께 개발한다고 했을 때, 팀원이 프로젝트를 위해 어떤 패키지를 설치했고, 어떤 버전을 설치 했는지 가상 환경 상황을 알 수 없기 때문에 가상 환경에 대한 정보 즉 **패키지 목록**이 공유되어야 한다.
- 가상 환경 자체를 공유하는 것은 패키지 자체의 용량이 너무 크기 때문에 패키지 목록만을 github에 공유함.
- 가상환경 폴더 venv는 gitignore에 작성되어 원격 저장소에 공유되지 않음
    - 저장소 크기를 줄여 효율적인 협업과 배포를 가능하게 하기 위함(requirements.txt를 공유)

### 패키지 목록 파일 주의사항

- 활성화된 가상환경에서 실행해야 정확한 패키지 목록 생성
- 시스템 전역 패키지와 구분 필요

## Django 프로젝트 생성

### 장고 설치

```bash
pip install django
```

### 프로젝트 생성 명령어

```bash
django-admin startproject 프로젝트명 .
```

- 참고) 프로젝트 생성 시 `.` 유무에 따른 생성 결과 차이
    - `.`자리의 명령어는 “해당 위치에 프로젝트 생성”이다.
    - 따라서 `.`이 있으면 현재 디렉토리에 프로젝트를 생성한다. 
    기본값은 프로젝트의 이름과 동일한 폴더이다.
    - `.` 을 사용했을 때
        
        ```powershell
        firstpjt/
        	settings.py
        	...
        manage.py
        ```
        
    - `.` 을 사용하지 않았을 때
        
        ```powershell
        firstpjt/
        	firstpjt/
        		settings.py
        		...
        	manage.py
        ```
        

### 서버 실행

```bash
python manage.py runserver
```

# Django Design Pattern

## Design Pattern

- 소프트웨어 설계에서 발생하는 문제를 해결하기 위한 일반적인 해결책
- 공통적인 문제를 해결하는데 쓰이는 형식화 된 관행
- “애플리케이션의 구조는 이렇게 구성하자” 라는 관행

### MVC 디자인 패턴

- Mode, View, Controller
- 애플리케이션을 구조화하는 대표적인 패턴
- 데이터, 사용자 인터페이스, 비즈니스 로직을 분리
- 시각적 요소와 뒤에서 실행되는 로직을 서로 영향 없이, 독립적이고 쉽게 유지 보수할 수 있는 애플리케이션을 만들기 위해

### MTV 디자인 패턴

- Model, Template, View
- Django에서 애플리케이션을 구조화하는 패턴
- 기존 MVC 패턴과 동일하나 단순히 명칭을 다르게 정의한 것

## Project & App

### Django project

- 애플리케이션의 집합
- DB설정, URL연결, 전체 앱 설정 등을 처리
- 전체적인, 포괄적인 기능 담당

### Django application

- 독립적으로 작동하는 기능 단위 모듈
- 각자 특정한 기능을 담당하며 다른 앱들과 함께 하나의 프로젝트를 구성

## 앱을 사용하기 위한 순서

### 앱 생성

- 1 단계) 명령어 입력
    - 앱 이름은 복수형으로 지정하는 것을 권장
    - 앱(**복수형**)과 모델(**단수형**) 사이의 관계가 명확해지기 때문에, 이러한 작명 규칙 또한 관행적
    
    ```bash
    	python manage.py startapp 앱이름
    ```
    
- 2 단계) 앱 등록
- settings.py 의 installed_apps 리스트에 생성한 앱을 추가
    - 반드시 앱을 생성한 후에 등록
    - 등록 후 생성은 불가능
    
    ```bash
    # settings.py
    
    INSTALLED_APPS = [
        '앱이름',    # 콤마 잊지 마세요.
        ....
    ]
    ```
    

## 프로젝트 구조

- settings.py
    - 프로젝트의 모든 설정을 관리
- urls.py
    - 요청 들어오는 URL에 따라 이에 해당하는 적절한 views를 연결
- __init __.py
    - 해당 폴더를 패키지로 인식하도록 설정하는 파일
    - 수업 과정에서 수정 안 함
- asgi.py
    - 비동기식 웹 서버와의 연결 관련 설정
    - 수업 과정에서 수정 안 함
- wsgi.py
    - 웹 서버와의 연결 관련 설정
    - 수업 과정에서 수정 안 함
- manage.py
    - django 프로젝트와 다양한 방법으로 상호작용하는 커맨드라인 유틸리티
    - 수업 과정에서 수정 안 함

## 앱 구조

- admin.py
    - 관리자용 페이지 설정
- models.py
    - db와 관련된 model을 정의
    - MTV패턴의 M
- views.py
    - http 요청을 처리하고 해당 요청에 대한 응답을 반환(url, model, template과 연계)
    - MTV패턴의 V
- apps.py
    - 앱의 정보가 작성된 곳
    - 수업 과정에서 수정 안 함
- tests.py
    - 프로젝트 테스트 코드를 작성하는 곳
    - 수업 과정에서 수정 안 함

# 요청과 응답

## Django에서의 요청과 응답

- Django 는 urls → views → templates 에 따라 요청 → 응답을 구성하므로 코드도 이에 따라 작성한다.
    1. 브라우저가 [http://127.0.0.1:8000/index/](http://127.0.0.1:8000/index/로)  로 요청
    2. urls.py에서 index/주소와 일치하는 view 함수 호출
    3. views.py에서 index view 함수가 응답 객체 생성 및 반환
    4. 브라우저는 index view함수가 반환한 응답 객체를 해석해 페이지를 렌더링

### 1. URLs

- [http://127.0.0.1:8000/index/](http://127.0.0.1:8000/index/로) 로 요청이 들어왔을 때 request객체를 views모듈의 index view함수에 전달하며 호출
- url 경로는 반드시 ‘/’로 끝나야 함
    
    ```bash
    from django.contrib import admin
    from django.urls import path
    from articles import views
    
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('index/', views.index),
    ]
    ```
    

### 2. Views

- view 함수가 정의되는 곳
- 특정 경로에 있는 template과 request객체를 결합해 응답 객체를 반환
- 모든 view함수는 첫 번째 인자로 요청 객체를 필수적으로 받음
- 매개변수 이름이 request가 아니어도 되지만 그렇게 작성하지 않음
    
    ```bash
    def index(request):
        return render(request, 'index.html')
    ```
    

### 

# 참고

## 가상환경 생성 루틴 + git

1. 가상환경(venv)생성
    
    ```bash
    python -m venv venv
    ```
    
2. 가상환경 활성화
3. Django 설치
4. 패키지 목록 파일 생성 (패키지 설치시마다 진행)
5. .gitignore파일 생성(첫 add 전)
6. git 저장소 생성( git init)
7. Django 프로젝트 생성

## render 함수

- 주어진 템플릿을 주어진 컨텍스트 데이터와 결합하고 렌더링 된 텍스트와 함께 HttpResponse 응답 객체를 반환하는 함수
- request
    - 응답을 사용하는 데 사용되는 요청 객체
- template_name
    - 템플릿 이름의 경로
- context
    - 템플릿에서 사용할 데이터(딕셔너리 타입으로 작성)

```bash
render(request, template_name, context)
```