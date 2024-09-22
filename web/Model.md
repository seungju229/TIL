# Model

## Django Model

- DB의 테이블을 정의하고 데이터를 조작할 수 있는 기능들을 제공
    - 테이블 구조를 설계하는 청사진

## Model class

- 파이썬으로 작성한 모델 클래스는 최종적으로 DB에 테이블 구조를 만듦

# Model Field

- DB 테이블의 필드(열)를 정의하며, 해당 필드에 저장되는 데이터 타입과 제약 조건을 정의

## Field types

- 데이터베이스에 저장될 “데이터의 종류”를 정의
    
    ```bash
    # app의 models.py
    
    from django.db import models
    
    class Article(models.Model):  # import 되어 있는 models모듈의 Model 클래스를 상속받겠다.
        title = models.CharField(max_length=10)  # 초기값을 max_length = 10으로 설정
        content = models.TextField()
        
       # 변수는 필드 이름 
    ```
    

### 주요 필드 유형

- CharField()
    - 제한된 길이의 문자열 저장
    - 필드의 최대 길이를 결정하는 max_length는 필수 옵션
- TextField()
    - 길이 제한이 없는 대용량 텍스트를 저장
    - 무한대는 아니며 사용하는 시스템에 따라 달라짐

## Field options

- 필드의 “동작”과 “제약 조건”을 정의

### 제약 조건

- 특정 규칙을 강제하기 위해 테이블의 열이나 행에 적용되는 규칙이나 제한 사항
    - ex) 숫자만 저장되도록, 문자가 100자 까지만 저장되도록

# Migrations

- model 클래스의 변경 사항(필드 생성, 수정 삭제 등)을 DB에 최종 반영하는 방법
    1. model.py에서 model class 작성해서 설계도 초안 만들기
        
        (makemigrations)
        
    2. migration 파일에서 최종 설계도 만들기(django가 해 줌)
        
        (migrate)
        
    3. 최종 설계도를 db.sqlite로 최종 전달(DB에 저장)

### 핵심 명령어 2가지

```bash
$ python manage.py makemigrations

# model class를 기반으로 최종 설계도(migration) 작성
```

```bash
$ python manage.py migrate

# 최종 설계도를 DB에 전달하여 반영
```

## 추가 Migrations

- 이미 생성된 테이블에 필드를 추가해야 한다면?
- 이미 기존 테이블이 존재하기 때문에 필드를 추가할 때 필드의 기본 값 설정이 필요
- 현재 대화를 유지하면서 직접 기본 값을 입력하는 방법 or
    
    현재 대화에서 나간 후 models.py에 기본 값 관련 설정을 하는 방법
    
    - 아무것도 입력하지 않고 enter를 누르면 Django가 제안하는 기본 값으로 설정됨
- model class에 변경사항이 생겼다면 반드시 1. 새로운 설계도를 생성하고, 2. 이를 DB에 반영해야 한다.
    - model class 변경 → makemigrations → migrate

```bash
class Article(models.Model):
    title = models.CharField(max_length=10)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
```

- auto_now
    - 데이터가 저장될 때마다 자동으로 현재 날짜 시간을 저장
    - 수정한 날짜
- auto_now_add
    - 데이터가 처음 생성될 때만 자동으로 현재 날짜 시간을 저장
    - 작성한 날짜

# Admin site

## Automatic admin interface

- Django가 추가 설치 및 설정 없이 자동으로 제공하는 관리자 인터페이스
    - 데이터 확인 및 테스트 등을 진행하는 데 유용
1. admin 계정 생성
    
    ```bash
    $ python manage.py createsuperuser
    ```
    
    - email은 선택사항이기 때문에 입력하지 않고 진행 가능
    - 비밀번호 입력 시 보안상 터미널에 출력되지 않음
    - migrations를 하지 않으면 admin 계정 생성되지 않음
        - table이 없어서 admin 계정이 저장될 곳이 없기 때문
2. DB에 생성된 admin 계정 확인
    - DB auth_user에 계정 생성됨
3. admin에 모델 클래스 등록
    - admin.py에 작성한 모델 클래스를 등록해야만 admin site에서 확인 가능
    
    ```bash
    from django.contrib import admin
    from .models import Article
    
    # Register your models here.
    # admin site에 등록한다.
    admin.site.register(Article)
    ```
    
4. admin site로그인 후 등록된 모델 클래스 확인
5. 데이터 생성, 수정, 삭제 테스트
6. 테이블 확인

# 참고

## 데이터베이스 초기화

1. migration 파일 삭제
2. db.sqlite3 파일 삭제
    
    ( __ init __.py 와 migrations 폴더는 지우지 않도록 주의)
    

## Migrations관련

```bash
$ python manage.py showmigrations
```

- migrations 파일들이 migrate 됐는지 안됐는지 여부를 확인하는 명령어
- [X]표시가 있으면 migrate가 완료되었음을 의미

```bash
$ python manage.py sqlmigrate articles 0001
```

- 해당 migrations 파일이 SQL 언어(DB에서 사용하는 언어)로 어떻게 번역 되어 DB에 전달되는지 확인하는 명령어

## SQLite

- 데이터베이스 관리 시스템 중 하나이며 Django의 기본 데이터베이스로 사용됨
    - 파일로 존재하며 가볍고 호환성이 좋음