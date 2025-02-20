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
```python
class Question(models.Model):
    question_text = models.CharField(max_length=200)  # 200자 제한
    pub_date = models.DateTimeField("date published")
```

- **`models.CharField`**: 글자 수 제한 (반드시 최대 글자수를 지정해야 함)
- **`models.DateTimeField`**: 날짜와 시간 입력

---

### ForeignKey 사용 예시
```python
class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

- **`models.ForeignKey`**: 다른 모델에 대한 링크 정의 (테이블 간 관계 설정)
- **`on_delete` 옵션**:
  - `models.CASCADE`: 부모 객체 삭제 시 자식 객체도 삭제
  - `models.PROTECT`: 부모 객체 삭제 불가
  - `models.SET_NULL`: 부모 객체 삭제 시 `NULL` 설정
  - `models.SET_DEFAULT`: 부모 객체 삭제 시 기본값 설정
  - `models.DO_NOTHING`: 아무 일도 하지 않음

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
- **`
