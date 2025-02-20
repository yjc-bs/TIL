# TIL

---

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