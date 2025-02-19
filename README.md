# TIL

## Memo

ls

mkdir

cd

-v 등 명령어 정리 필요

md

## Tutorial01

개발 서버 확인 

    $ python3 manage.py runserver
<br>

**오류 상황**

    from django.contrib import admin
    from django.urls path

    urlpatterns = [
        path("polls/", include("polls.urls")),
        path('admin/', admin.site.urls),
    ]

include()는 django.urls 모듈에 포함된 기능으로 import 해야만 사용 가능<br><br>

    from django.contrib import admin
    from django.urls import include, path

    urlpatterns = [
        path("polls/", include("polls.urls")),
        path('admin/', admin.site.urls),
    ]

<b>path()</b> django에 url을 지정<br>
<b>include()</b> 각 앱의 urls.py를 메인 urls.py에 포함시킴 (재사용 용이, 유지보수 목적)


## Tutorial02

데이터베이스 설정 (djtest/settings.py)

     TIME_ZONE = 'Asia/Seoul'


모델 생성

polls/models.py

    class 모델이름(models.Modle): //models 로 모델이 데이터베이스에 저장
        필드이름1 = modesl.필드타입(필드옵션)
        필드이름2 = modesl.필드타입(필드옵션)
<Br>

    class Question(models.Model):
            question_text = models.CharField(max_length=200) //200자 제한
            pub_date = models.DateTimeField("date published") // 


models.CharField - 글자 수 제한.(반드시 최대 글자수를 지정해야함 200자)
models.DateTimeField - 날짜, 시간 두개의 text input이 생성됨.(날짜 발행시점)


    class Choice(models.Model):
            question = models.ForeignKey(Question, on_delete=models.CASCADE)
            choice_text = models.CharField(max_length=200)
            votes = models.IntegerField(default=0)

models.ForeignKey - 다른 모델에 대한 링크 정의, 테이블 간의 관계 정의, 테이블의 특정 데이터를 참조함

    question = models.ForeignKey(Question, on_delete=models.CASCADE) //Question 모델을 참조하고, 참조된 Question이 삭제되면 연결된 Choice도 삭제됨
<br>

        models.CASCADE - 부모 객체 삭제되면, 자식 객체도 삭제 (기본)
        models.PROTECT - 부모 객체 삭제 못하게 막음
        models.SET_NULL -
        models.SET_DEFAULT -
        models.DO_NOTHING - 아무일x



models.IntegerField - 정수 데이터를 저장(0부터)

<hr/>
설정(djtest/settings.py)에 polls 앱 포함시키기

    INSTALLED_APPS = [
        "poll.sapps.PollsConfig" //추가

<hr/>

### Migration
데이터베이스에 새로 만든 모델을 추가하는 행위

    python3 manage.py migrate // 후 모델이 데이터 베이스에 저장됨

### Shell 호출
    python3 manage.py shell //Django 환경에서 python 코드를 직접 실행할 수 있게 하는 대화형 인터프리터

Django 프로젝트를 로드한 상태에서 실행, 즉 django 모델, 데이터베이스 설정 등을 바로 실행 (뭐가 다른지는 아직 잘 모르겠음, 그리고 꼭 이렇게 shell을 호출해야만 하는 이유는..?)

https://docs.djangoproject.com/en/5.1/intro/tutorial02/
순서대로 일단 입력<Br>
    -q.save, q.id // ..? 이해x 일단 넘어감

-여기까지 완료됐다면, 

    >>> Question.objects.all()
    <QuerySet [<Question: Question object (1)>]> //이렇게 나옴

### 메서드 추가 (사람이 읽기 쉽게)

polls/models.py 

        def __str__(self):
            return self.question_text

모델에 메서드를 추가하는 이유 : 대화형 프롬프트 처리의 편의성, 

__str__()

        def was_published_recently(self):
            return self.pub_date >= timezone.now() - datetime.timedelta(days=1)

시간대 관련 유틸리티 추가

까지 저장하고 다시 대화형 셸 시작 python3 manage.py shell에 순서대로 입력...

-선택문항을 하나씩 넣어주고 삭제하는 실습(?)<br>
-c. ?

<hr/>

### Admin

admin , yeonjoo.chi@beautyselection.co.kr , 1234qwer

http://127.0.0.1:8000/admin/






