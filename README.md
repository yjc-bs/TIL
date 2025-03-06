# TIL

<details>
    <summary> ~2/21 </summary>
## Memo

- `ls`
- `mkdir`
- `cd`
- `-v` 등 명령어 정리 필요
- `md`

---

## Tutorial01: 개발 서버 확인

### 개발 서버 실행
```bash
$ python3 manage.py runserver
```

---

### **오류 상황**

```python
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path("polls/", include("polls.urls")),
    path('admin/', admin.site.urls),
]
```

- `include()`는 `django.urls` 모듈에 포함된 기능으로 **import** 해야만 사용 가능

**수정 코드:**
```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path("polls/", include("polls.urls")),
    path('admin/', admin.site.urls),
]
```

---

### **URL 설정 설명**
- **`path()`**: Django에 URL을 지정
- **`include()`**: 각 앱의 `urls.py`를 메인 `urls.py`에 포함 (재사용 용이, 유지보수 목적)

---

## Tutorial02: 데이터베이스 설정 및 모델 생성

### 데이터베이스 설정
**`djtest/settings.py`**
```python
TIME_ZONE = 'Asia/Seoul'
```

---

### 모델 생성
**`polls/models.py`**

    class 모델이름(models.Modle): //models 로 모델이 데이터베이스에 저장
        필드이름1 = modesl.필드타입(필드옵션)
        필드이름2 = modesl.필드타입(필드옵션)

```python
class Question(models.Model):
    question_text = models.CharField(max_length=200)  # 200자 제한
    pub_date = models.DateTimeField("date published")
```

- **`models.CharField`**: 글자 수 제한 (반드시 최대 글자수를 지정해야 함)
- **`models.DateTimeField`**: 날짜와 시간 입력 (text input이 두개 생성됨)

---

### ForeignKey 사용 예시
```python
class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

- **`models.ForeignKey`**: 다른 모델에 대한 링크 정의 (테이블 간 관계 설정, 테이블의 특정 데이터를 참조함)
- **`on_delete` 옵션**:
  - `models.CASCADE`: 부모 객체 삭제 시 자식 객체도 삭제 (기본)
  - `models.PROTECT`: 부모 객체 삭제 불가
  - `models.SET_NULL`: 부모 객체 삭제 시 `NULL` 설정
  - `models.SET_DEFAULT`: 부모 객체 삭제 시 기본값 설정
  - `models.DO_NOTHING`: 아무 일도 하지 않음

- **`models.IntegerField`**: 정수 데이터를 저장(0부터)
---

### 앱 등록
**`djtest/settings.py`**
```python
INSTALLED_APPS = [
    "polls.apps.PollsConfig"  # polls 앱 추가
]
```

---

### Migration (데이터베이스 반영)
데이터베이스에 새로 만든 모델을 추가하는 행위

```bash
$ python3 manage.py migrate
```

---

### Shell 호출
```bash
$ python3 manage.py shell
```

- Django 환경에서 Python 코드를 직접 실행할 수 있는 대화형 인터프리터
- Django 모델, 데이터베이스 설정 등을 바로 실행 가능
(그런데 왜 Shell 을 호출해야할까?)

https://docs.djangoproject.com/en/5.1/intro/tutorial02/
<Br>
    -q.save, q.id // 이해x 우선 입력

    >>> Question.objects.all()
    <QuerySet [<Question: Question object (1)>]> #이렇게 출력됨

---

### 메서드 추가 (사람이 읽기 쉽게)
**`polls/models.py`**
```python
def __str__(self):
    return self.question_text
```

- 모델에 `__str__()` 메서드를 추가하여 대화형 프롬프트에서 읽기 쉽게 출력

---

### 시간 관련 메서드 추가
```python
def was_published_recently(self):
    return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
```

- **`timezone.now()`**: 현재 시간 반환
- 이후 대화형 shell에 하기 내역 입력
- 선택 문항 추가 후 삭제
- c 명령어 

---

### Admin
    admin , yeonjoo.chi@beautyselection.co.kr , 1234qwer

**`polls/admin.py`**


`from .models import Question`로 모델을 가져오고(import) 

`admin.site.register(Question)` 로 모델을 등록을 완료 해야 관리자 페이지에 노출됨

---

## Tutorial03: 뷰 생성

뷰 Views : 
- 화면을 만들어서 보여주는 역할
- 사용자가 url 을 입력하면 해당 url에 연결된 View 함수를 실행함


#우선 튜토리얼 따라서.. 2차에는 f"{}로 작성하기

    path("<int:question_id>/vote/", views.vote, name="vote"),

---
**`polls/views.py`**

```python
def index(request):
    latest_question_list = Question.objects.order_by("-pub_date")[:5]
    output = ", ".join([q.question_text for q in latest_question_list]) #질문 내역이 a, b, c ... 이런식으로 보여짐
    return HttpResponse(output)
```

- `-pub_date`(발행 날짜)를 기준으로 내림차순 정렬 (`pub_date` 오름차순 정렬)
- `[:5]` 정렬된 데이터 중 앞에서부터 5개

---

### 탬플릿

```python
from django.http import HttpResponse
from django.template import loader

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by("-pub_date")[:5]
    template = loader.get_template("polls/index.html")
    context = {
        "latest_question_list": latest_question_list,
    }
    return HttpResponse(template.render(context, request))
```

- `template = loader.get_template("#.html")` ##.html 템플릿으로 불러옴
- `context`넘겨줄 데이터 내용

---
#### shortcuts / render()

- HttpResponse()은 코드가 길어져 관리가 어려움
- html 파일을 불러와서 사용하기 때문에 뷰 함수에서는 데이터만 준비하면 됨


#### shortcuts / get_object_or_404, render
    question = get_object_or_404(Question, pk=question_id)

- question 변수에 다음 질문 객체를 넣음
- Queestion 모델에서 `question_id` 에 해당하는 데이터를 가져오고, 만약 데이터가 없으면 404 에러 페이지가 자동으로 뜸
- pk=id
---

-뷰와 템플릿의 관계
- 뷰 : 모델과 템플릿을 연결하는 역할(?)
- 템플릿 : 꾸

---
하드코딩된 부분 변수로 바꾸기

```polls/index.html```

    <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>

- 하드코딩
- url 구조가 바뀌면 모든 html 파일 수정 필요. 유지보수 힘듬

<br>

    <li><a href="{% url 'detail' question.id %}">{{question.question_text}}</a></li>

- {% url %} 방식
- 'detail' - `polls/urls.py` 에서 `path("<int:question_id>/", views.detail, name="detail"),` 로 개발자가 지정함 (name 부분임)

<br>

#### Url 경로 바꿀때는 템플릿 (x)

```polls/urls.py``` 여기서 수정

    path("이런식으로/<int:question_id>/results/", views.results, name="results"),

---

#### namespace
`polls/urls.py` 

app_name = "polls" 로 지정하여

    <li><a href="{% url 'polls:detail' question.id %}">{{question.question_text}}</a></li>

- 'polls:detail' -앱 네임 지정한 다음엔 이렇게 경로를 안바꿔주면 오류남 (NoReverseMatch at /polls/)

## Tutorial04: 앱 작성하기

#### 폼 form 

`polls/detail.html` 투표 상세 생성 (detail 너무 많아서 헷갈림...)

{% csrf_token %} form 바로 아래 작성, 보안용

`forloop.counter` for 태그 반복 횟수 

---

```python
from django.db.models import F
from django.http import HttpResponse, HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse

from .models import Choice, Question


# ...
def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST["choice"])
    except (KeyError, Choice.DoesNotExist):
        return render(
            request,
            "polls/detail.html",
            {
                "question": question,
                "error_message": "You didn't select a choice.",
            },
        )
    else:
        selected_choice.votes = F("votes") + 1
        selected_choice.save()
        return HttpResponseRedirect(reverse("polls:results", args=(question.id,)))
```

* F - 데이터베이스 연산을 위해 
* selected_choice.votes = F("votes") + 1 - 데이터베이스 votes에 1을 증가시킴


* HttpResponseRedirect - 사용자를 다른 url로 이동시킴
* reverse - url 하드코딩 없이 url 이름으로 이동 (이해x???)

* try ... except ... else

`try`<br>
    - name="choice" 인 값을 가져오게 함


`except` 오류가 있다면<Br>
    - `KeyError` 아무것도 선택하지 않음<Br>
    - `Choice.DoesNotExist` 선택한 항목이 데이터베이스에 없음

`else` 오류가 없다면<br>
    - 데이버베이스에 1 증가, 데이터베이스에 저장, 결과 페이지로 이동

---
클래스형 뷰는 다음 사이클에서 진행
---


* mtv - 장고 기본 구조
* mvc

clinet<->server
    request/ response


<hr/>
pep-8
pep

Web Framework : 어떤 사이트를 만들더라도 필요한 공통적인 작업을 미리해둔 소프트웨어 (jsp, flask 등)
라이브러러

<hr>
form의 method
https method

</details>

<details>
<summary>2/24</summary>
## Tutorial 2차

### 설치

    % django-admin startproject myproject .
마지막에 .을 찍어야 myproject 해당 폴더에 생성됨 (안찍으면 my~폴더 안에 my~폴더가 또 생김)

settings.py : 프로젝트에 운영하는 데 필요한 설정들
urls.py : 사용자가 접속하는 패스에 따라서 그 요청(접속)을 어떻게, 누가 처리할 것인지 지정을 함(라우팅)
manage.py : 프로젝트를 진행하는 데 필요한 기능, 유틸리티 파일

    % manage.py runserver 8888

http://localhost:8000/ 가 이미 사용 중일 경우 -> 대신해서 포트 번호 8888에서 실행

project>app>view 흐름 이해
- 어플리케이션은 app 단위에서 구현
- app 안에 view 안에 함수들로 어플리케이션을 구체적으로 구현함

- 사용자가 각 각의 경로로 접속하면, 그 경로를 project의 url.py 에 지정한 app의 url.py로 위임 -> 지정된 app의 url.py을 통해 그 app의 view - 안의 함수로 위임되어 작업 진행 -> db가 필요한 경우 app의 model을 통해서 사용 -> 최종적으로 클라이언트에게 응답 (html, xml, json 형태로)


### 라우팅 Routing

- 사용자가 접속한 경로를 어떻게 처리할 것인가
- 장고에서는 project의 urls.py 가 가장 큰 틀의 라우팅을 하고 -> 앱 -> 특정 함수로 위임

- 라우팅 실습

`myproject/urls.py`
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('myapp.urls'))
]
```

`myapp/urls.py`
```python
from django.urls import path
from myapp import views

urlpatterns = [
    path('',views.index),
    path('create/',views.create),
    path('read/<id>/',views.read)
]
```
**오류 상황**<br>
패스 끝에 / 유무로 url이 제대로 불러와 지지 않을 수 있음
`path('read/<id>',views.read)` 오류 발생
`path('read/<id>/',views.read)` 정상 작동



`myapp/views.py`
```python
from django.shortcuts import render, HttpResponse

def index(request):
    return HttpResponse('Welcome!')

def create(request):
    return HttpResponse('Create!')

def read(request, id):
    return HttpResponse('Read!'+id)
```
</details>

<details>
    <summary>2/25</summary>
## CRUD : 시스템의 기본 관리 기능 (create, read, update, delete)

### Read ###

```python
from django.shortcuts import render, HttpResponse

topics = [
    {'id':1, 'title':'routing', 'body':'Routing is ..'},
    {'id':2, 'title':'veiw', 'body':'View is ..'},
    {'id':3, 'title':'model', 'body':'Model is ..'},
]

def HtmlTemplate(articleTag):
    global topics
    ol = ''
    for topic in topics:
        ol += f'<li><a href="/read/{topic["id"]}">{topic["title"]}</a></li>'
    return f'''
       <html>
        <body>
        <h1><a href="/">Django</a></h1>
            <ol>
                {ol}
            </ol>
        {articleTag}
        </body>
        </html>                 
    '''

def index(request):
    article = '''
    <h2>Welcome</h2>
    hello, django
    '''
    return HttpResponse(HtmlTemplate(article))

def read(request, id):
    global topics
    article = ''
    for topic in topics:
        if topic['id'] == int(id):
            article = f'<h2>{topic["title"]}</h2>{topic["body"]}'
    return HttpResponse(HtmlTemplate(article))

def create(request):
    return HttpResponse('Create!')
```

### Create

```python
...

def create(request):
    article = '''
    <form action="/create/">
        <p><input type="text" name="title" placeholder="title"></p>
        <p><textarea name="body" placeholder="body"></textarea></p>
        <p><input type="submit"></p>
    </form>
'''
    return HttpResponse(HtmlTemplate(article))
```

-get 방식: 브라우저가 서버로부터 데이터를 읽어오는 방식

/?title=ㅇㅇ&body=ㅇㅇㅇ : `` 변경하는 방식 <큰일남...

-post 방식: 브라우저

method="get" : 기본값, 
method="post" :

### request response object
```python
from django.shortcuts import render, HttpResponse, redirect
from django.views.decorators.csrf import csrf_exempt

nextID = 4
topics = [
    {'id':1, 'title':'routing', 'body':'Routing is ..'},
    {'id':2, 'title':'veiw', 'body':'View is ..'},
    {'id':3, 'title':'model', 'body':'Model is ..'},
]

def HtmlTemplate(articleTag):
    global topics
    ol = ''
    for topic in topics:
        ol += f'<li><a href="/read/{topic["id"]}">{topic["title"]}</a></li>'
    return f'''
       <html>
        <body>
        <h1><a href="/">Django</a></h1>
            <ol>
                {ol}
            </ol>
        {articleTag}
        <ul>
            <li><a href="/create/">create</a></li>
        </ul>
        </body>
        </html>                 
    '''

def index(request):
    article = '''
    <h2>Welcome</h2>
    hello, django
    '''
    return HttpResponse(HtmlTemplate(article))

def read(request, id):
    global topics
    article = ''
    for topic in topics:
        if topic['id'] == int(id):
            article = f'<h2>{topic["title"]}</h2>{topic["body"]}'
    return HttpResponse(HtmlTemplate(article))

@csrf_exempt
def create(request):
    global nextID
    # print('request.method', request.method)
    if request.method == "GET":
        article = '''
        <form action="/create/" method="post">
            <p><input type="text" name="title" placeholder="title"></p>
            <p><textarea name="body" placeholder="body"></textarea></p>
            <p><input type="submit"></p>
        </form>
    '''
        return HttpResponse(HtmlTemplate(article))
    elif request.method == "POST":
        title = request.POST['title']
        body = request.POST['body']
        newTopic = {'id':nextID, 'title':title, 'body':body}
        topics.append(newTopic)
        url = '/read/'+str(nextID)
        nextID = nextID + 1
        return redirect(url)
```

### delete

path('delete/',views.delete, name='delete') 패스 추가

```python
@csrf_exempt
def delete(request):
    global topics
    if request.method == "POST":
        id = request.POST['id']
        newTopics = []
        for topic in topics:
            if topic['id'] != int(id):
                newTopics.append(topic)
        topics = newTopics
        return redirect('/')
```
</details>

<details>
        <summary>2/28</summary>

### update

path('update/<id>/',views.update, name='update'), 패스 추가

```python
@csrf_exempt
def update(request, id):
    global topics
    if request.method == "GET":
        for topic in topics:
            if topic['id'] == int(id):
                selectedTopic = {
                    'title':topic['title'],
                    'body':topic['body']}
        article = f'''
        <form action="/update/{id}/" method="post">
            <p><input type="text" name="title" placeholder="title" value={selectedTopic['title']}></p>
            <p><textarea name="body" placeholder="body">{selectedTopic['body']}</textarea></p>
            <p><input type="submit"></p>
        </form>
    '''
        return HttpResponse(HtmlTemplate(article, id))
    elif request.method =="POST":
        title = request.POST['title']
        body = request.POST['body']
        for topic in topics:
            if topic['id'] == int(id):
                topic['title'] = title
                topic['body'] = body
        return redirect(f'/read/{id}')
```

update 너무 어려움...

</details>

<details>
    <summary>3/4</summary>

#### JAVASCRIPT
### 변수 선언 var, let, const
재선언 : 중복선언
재할당 : 값 변경

    var x = 1;
    var x = 2; // 가능
    x = 3;     // 가능

    let y = 1;
    let y = 2; // ❌ 에러 (재선언 불가)
    y = 3;     // 가능

    const z = 1;
    const z = 2; // ❌ 에러 (재선언 불가)
    z = 3;      // ❌ 에러 (재할당 불가)


`var` 재선언 가능o, 재할당o <- 덮어쓰기 발생<br>
`let` 변수 재선언 불가x, 재할당 가능o <br>
`const` 변수 재선언 불가x, 재할당 불가x

### 정규식

1,000 단위마다 , 추가하는 함수

```javascript
function numberWithCommas(x) {
  return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',');
}
```

```javascript
"총 금액: 1000원, 할인: 200원".replace(/\d+/g, function(match) {
    return Number(match).toLocaleString();
});
```

toLocaleString() 메서드는 숫자나 날짜 객체를 문자열로 변환할 때 사용

정규식 연습하기 : 
https://kevinitcoding.tistory.com/entry/%EC%A0%95%EA%B7%9C-%ED%91%9C%ED%98%84%EC%8B%9D%EC%9D%B4%EB%9E%80


</details>

<details>
    <summary>3/5</summary>

### web font

* <link> 태그나  @import 구문에 다운로드 주소를 링크
* eot, woff 가볍기 때문에 먼저 지정

B: HTML 요청 > DOM 구성 >
B: CSS 요청 > CSSOM 구성 >
B: 렌더링에 필요한 폰트 요청 > B: 렌더링 진행 - 폰트가 준비되어있지 않다면 렌더링 x > 폰트가 준비되면 텍스트 공란을 채우거나 대체 폰트로 렌더링

* FIOT : 렌더링 되는 동안 아무것도 보이지 않음
* FOUT : 렌더링 되는 동안 기본 시스템 폰트라도 보이게함

```html
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@100..900&family=Roboto:ital,wght@0,100..900;1,100..900&family=Tenor+Sans&display=swap" rel="stylesheet">
```

* `preconnect` : 브라우저가 사이트와의 연결을 에상하고 도메인에 필요한 사전 작업을 미리 진행하게 함 (단 리소스 사용 큼, 남용 금지)

</details>

<details>
    <summary>3/6</summary>

### form 의 method 속성
 input의 내용 등을 서버로 전송하는 방법을 지정해 줌

* 파라미터 : 클라이언트가 요청 시 서버의 특정 주소로 넘겨주는 데이터
* query sting : https://..//##?파라미터 이름=파라미터 값

* get : 주로 조회
    * url에 폼 데이터를 추가하여 서버로 전달 (주소창에 전달)
    * 브라우저 캐시 저장O
    * query string 포함되어 전송됨, 길이 제한o
    * 보안상 취약, 공개되어도 무방한 정보들에 적용 / 거의 모든 페이지에 적용

* post : 주로 등록
    * 폼 데이터를 별도로 첨부하여 서버로 전달 (body에 전달)
    * 브라우저 캐시 저장x, 히스토리에 남지 않음
    * query string 과는 별도로 전송됨, 길이 제한x
    * 보안성 높음 / 로그인, 회원가입, 글 작성, 업로드 페이지 등

### http 요청 method
* http : request 요청 - response 응답

* GET, POST, PUT, PATCH, DELETE / HEAD, CONNECT, OPTIONS, TRACE

* 멱등성 : 동일한 요청을 1번 = 여러번 같은 효과(응답이 같을 때), 서버의 상태도 동일할 때
    * GET은 멱등하고, POST는 멱등하지 x(응답이 다를 수 있음)
https://developer.mozilla.org/ko/docs/Glossary/Idempotent

* HTTP Request Message : Start line / Headers / Body 로 구성됨
    * GET은 body가 없고, POST는 body가 존재

<hr>

### PEP, PEP-8
PEP : 파이썬 코딩 규약
PEP-8 : Style guide for python code

https://peps.python.org/
https://peps.python.org/pep-0008/
https://wikidocs.net/21733



1. 들여쓰기
    * 4칸 스페이스 
    * 탭 사용x
2. 최대 줄 길이 79자
3. 연산자 앞에서 줄바꿈 
4. 빈 줄 사용
    * 최상위 함수와 클래스 정의 앞 뒤 2빈줄
    * 클래스 내 메서드 정의 앞 뒤 1빈줄
    * 그 외 관련된 함수들 끼리 그룹처럼 보이도록 빈줄 추가
5. 파일 내 함수와 클래스 순서
    * 모듈 docstring (모듈에 대한 설명)
    * from __future__ import 문 (있는 경우)
    * 모듈 import
    * 전역 변수 선언
    * 함수 정의
    * 클래스 정의
</details>