# 장고 프로젝트 1

장고걸즈 튜토리얼 기본편 [주소](https://tutorial.djangogirls.org/ko/)

<br>

### 환경설정
IDE : pycharm 2019.03

python : 3.8.5

ENV : anaconda ~ `django-girls`로 환경 세팅

<br>

## 장고란?
파이썬으로 만들어진 무료 오픈소스 웹 애플리케이션 프레임워크


#### 장고의 특징:
장고에서는 디렉토리와 파일명이 매우 중요. \
파일명을 마음대로 변경해서도 안되고 다른 곳으로 옮겨도 안됩니다. \
장고는 중요한 것들을 찾을 수 있게 특정한 구조를 유지해야 합니다.


#### 기본 구조 
```
django-girls
├───django-girls (mysite 로 변경)
    ├───manage.py
    └───mysite
            settings.py
            urls.py
            wsgi.py
            __init__.py
```

- `manage.py`: 스크립트인데, 사이트 관리를 도와주는 역할을 합니다. \
이 스크립트로 다른 설치 작업 없이, 컴퓨터에서 웹 서버를 시작할 수 있습니다.

- `settings.py`: 웹사이트 설정이 있는 파일입니다.

- `urls.py`: urlresolver가 사용하는 패턴 목록을 포함하고 있습니다. \
우편배달부(`urls.py`)는 어느 곳(패턴 목록)으로 편지를 배달해야 하는지 판단해야 한다. \
지금 그 파일들을 수정하지 않을 거니 무시하세요. 실수로 파일을 지우지 않게 조심하세요!


처음 세팅할 때, django-girls 안에 django-girls 가 있음.\ 
내부 사이트명을 변경하려면 rename만!! refactor하면 에러남.
> `django-girls` 에서 `mysite`로 명칭 변경


#### 1. 설정 변경
`mysite/settings.py` 타임존 설정. 웬만하면 UTC로 세팅하면 좋겠지만.. 일단 튜토리얼대로~👀

💥 주의사항
로컬에서 돌릴 때는 아래와 같이 세팅되면 **호스트는 ['localhost', '127.0.0.1', '[::1]'] 에 대해서만 유효**. 
```python
DEBUG = True
ALLOWED_HOSTS = []
``` 
배포 시 `ALLOWED_HOSTS = ['127.0.0.1', '.pythonanywhere.com']`로 세팅할 것. \
`DEBUG = false`를 해야할 지는 모르겠음

#### 2. DB
이미 내장되어 있는 `sqlite3` 사용.

<br>

#### 3. 모델 생성
1. 모델용 app 생성 (blog 로 칭함.)
```shell script
python manage.py startapp blog
# directory : /
```

2. 설정 세팅
mysite/settings.py -> `INSTALLED_APPS`에 blog 추가.


3. 새로 알아낸 점.
````python
author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
````
<b>class ForeignKey(to, on_delete, **options)</b>
- 다대일 관계에서 사용됨. 
- `to`, `on_delete` 인자는 필수로 있어야 함.
- 어떤 모델을 사용할지 모르겠다면 모델명을 직접 입력해도 된다.
- 외래키가 세팅되는 방식은 `ForeignKey.swappable`에 따라 다르다. 직접 참조 / 간접 참조.
  - 간접참조: `true` 인 경우. \
  외래키가 settings.AUTH_USER_MODEL 의 현재 값에 일치하는 모델이라면, \
migration이 세팅의 참조값(== 간접으로 참조한다)을 사용한다는 점임. 
<br>

  - 직접참조: `false` 인 경우. 모델을 직접 참조함. 함부로 false 세팅하지 말 것. user가 커스텀 한, 자신이 지원 가능한 모델만 사용 가능함. \
그 외의 모델은 에러. 항상 swapped 된 모델을 참조한다면, swappable 값을 false로 세팅해도 됨.
- `on_delete` 에 가능한 값들
    - CASCADE
    - PROTECT
    - SET_NULL
    - SET_DEFAULT
    - SET()
    - DO_NOTHING


#### 4. 모델 테이블 생성
`python manage.py makemigrations blog` : 마이그레이션 준비

`python manage.py migrate blog` : 마이그레이션 실행. 실제 DB에 모델 추가 반영.

