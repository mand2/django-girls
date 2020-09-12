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



# 장고 배포

### PythonAnywhere에 블로그 설정하기

1. [PythonAnywhere](www.pythonanywhere.com) 가입

2. Console - bash 로 들어감

3. 배포할 git clone

   ```bash
   $ git clone {url}
   ```

4. 해당 git tree 확인

   ```bash
   $ tree django-girls
   ```

   

5. 가상환경에 python 설치(실제 사용하는 가상환경은 conda이긴 한데, pythonanywhere에서 제공하는 가상환경은 virtualenv라서 이걸로 사용함)

   ```bash
   # virtualenv --python={로컬에서 사용했던 파이썬버전} {가상환경이름}
   $ virtualenv --python=python3.8 django-girls
   ...
   Installing setuptools, pip, wheel...
   done.
   
   # 가상환경 실행 후 자신이 사용했던 버전에 맞춰 django 설치
   $ source django-girls/bin/activate
   $ pip install django==3.0.3
   ...
   Successfully installed asgiref-3.2.10 django-3.0.3 pytz-2020.1 sqlparse-0.3.1
   ```

   

6. github에서 secret_key를 숨겨두라고 해서 따로 파일을 만들고 `.gitignore`에 추가해뒀었기에, 바로 실행하면 에러가 나옴.secret_key 세팅을 따로 해주어야 한다.

   ```bash
   # 해당 파일은 ~/django-girls/my_site/디렉토리에 만들어뒀어서 여기에..
   $ cat > secret_key.py
   secret_key = '{key....}'
   
   $ vi secret_key.py
   ```

7. 데이터베이스 생성
   로컬에서 사용하는 것과 다른 DB를 사용하므로, 초기화 필요

   ```bash
   $ python manage.py migrate
   ```

   

8. 배포설정 > django-girls 튜토리얼 [참고](https://tutorial.djangogirls.org/ko/deploy/#%EA%B0%80%EC%83%81%ED%99%98%EA%B2%BDvirtualenv-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0) 여기서부터 시작하면 됨.



### 웹사이트에서 보여질 view 만들기

웹사이트에서 보여질 view 만들기 (로컬에서 만든다) 
장고에선 url.py, view.py, 실제 구현부(=template) 를 통해 웹사이트를 만듬.

1. 구성요소
   - mysite/url.py 
     	=>  전체적인 url 패턴 관리. 깔끔하게 써야 관리하기 쉽다.
   - blog/url.py 
     	=> mysite에서  호출하는 url 중 blog~로 시작하는 부분에 관련된 url 패턴- 
   - blog/views.py 
     	=> blog/url.py 에서 등록한 패턴을 모아둔 곳. 어떤 html파일을 실제로 렌더링해줄지(구현할지) 세팅함.
     	=> `모델`에서 필요한 정보를 받아와서 `템플릿`에 전달하는 역할- 
   - blog/templates/blog/post_list.html 
     	=> 사이트의 url 주소로 이동할 때 실제 사용자가 보는 view.
2. git push
3. pythonanywhere  에서 재 배포
   pythonanywhere console >> bash에서 git 최신으로 업데이트 
   web>reload 하면 해당 창이 보여짐.





### 장고를 데이터베이스에 연결, 데이터를 저장하기

`쿼리셋(QuerySet)` : 전달받은 **모델의 객체 목록**입니다. 쿼리셋은 데이터베이스로부터 데이터를 읽고, 필터를 걸거나 정렬을 할 수 있습니다.

1. 장고 쉘로 들어간다.

   ```bash
   $ python manage.py shell
   ```

   

2. 객체 전체 조회하기

   ```python
   In [1]: from blog.models import Post
   
   In [2]: Post.objects.all()
   Out[2]: <QuerySet [<Post: 집중 하나도 안된다>, <Post: hi>, <Post: hi2>]>
   ```

3. 글 하나 쓰고, publish 하기

   ```python
   # 현재 등록된 user 정보 확인
   In [4]: User.objects.all()
   Out[4]: <QuerySet [<User: admin>]>
   
   # 변수 me 세팅
   In [5]: me = User.objects.get(username='admin')
   
   # post 만들기    
   In [6]: Post.objects.create(author=me, title='so hungry :(', text='wanna eat something')
   Out[6]: <Post: so hungry :(>
   
   # post 객체 확인
   In [7]: Post.objects.all()
   Out[7]: <QuerySet [<Post: 집중 하나도 안된다>, <Post: hi>, <Post: hi2>, <Post: so hungry :(>]>
   
   # 지금 막 만든 post 변수 세팅
   In [9]: post4 = Post.objects.get(title='so hungry :(')
   In [10]: post4.publish()
   
   ```

   

4. 쿼리셋 필터링 하기

   - 타이틀 이름에 "**hi**"가 들어간 post 만 가져오기

     ```python
     In [8]: Post.objects.filter(title__contains='hi')
     Out[8]: <QuerySet [<Post: hi>, <Post: hi2>]>
     ```

   - 지금 publish 된 것들만 가져오기 (띄엄띄엄 프로젝트를 하느라 다 같은 시간에 published 된 거로 나옴 ㅠ 아마 튜토리얼을 쭉~  하면 방금 등록한 글만 나올듯..)

     ```python
     In [13]: Post.objects.filter(published_date__lte=timezone.now())
     Out[13]: <QuerySet [<Post: 집중 하나도 안된다>, <Post: hi2>, <Post: so hungry :(>]>
     
     # 제대로 가져올 때
     <QuerySet [<Post: so hungry :(>]>
     ```

   - 정렬하기 : 오름차순

     ```python
     In [14]: Post.objects.order_by('created_date')
     Out[14]: <QuerySet [<Post: 집중 하나도 안된다>, <Post: hi2>, <Post: hi>, <Post: so hungry :(>]>
     ```

   - 정렬하기 : 내림차순

     ```python
     In [15]: Post.objects.order_by('-created_date')
     Out[15]: <QuerySet [<Post: so hungry :(>, <Post: hi>, <Post: hi2>, <Post: 집중 하나도 안된다>]>
     ```

   - 쿼리셋 여러가지 사용 `chaining` 이라고 함.

     ```python
     In [19]: 
     	Post.objects
     		.filter(title__contains='h')
     		.order_by('-published_date')
     Out[19]: <QuerySet [<Post: so hungry :(>, <Post: hi2>, <Post: hi>]>
     ```

     


