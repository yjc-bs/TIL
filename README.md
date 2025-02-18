# TIL

## Tutorial00

ls

mkdir

cd

-v 등 정리 필요

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

    djtest/settings.py




