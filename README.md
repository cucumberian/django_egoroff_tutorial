# 1

## 1.1 Что такое джанго

Джанго - фреймворк. Фреймворк - типовое решение для решения типовых проблем.
## Установка Django
Установка последней версии
```shell
pip3 install django
```
Установка определённой версии
```shell
pip3 install django==3.2
```

После этого станет доступна команда `django-admin`.

## 1.4 Создание проекта на Django
```shell
django-admin startproject project_name
```
При этом создастся в одноименный каталог с файлом с именем `manage.py` и внутри ищё один каталог с именем проекта.

# 1.5 Состав проекта
- `manage.py` - не стоит менять этот файл. Этот файл используется в командной строке для внесения изменений в джанго-проект. Он принимает на вход аргументы, обрабатывает их и изменяет проект.
- `__init__.py` - пустой, служит для того, чтобы каталог с ним стал питоновским пакетом.
- `asgi.py`, `wsgi.py` - необходимы при деплое проекта.
- `setting.py` - содержит константы, отвечающие за настройки всего проекта.
- `url.py` - регистрирует все страницы для сайта.

## 1.6 Запуск сервера
Файл `manage.py` делает то же, что и `django-admin`, но подставляет нужные переменные окружения.
По
тому запускает этот файл через интерпретатор питона
```shell
python3 manage.py runserver 8000
```
Последним аргументом опционально указывается порт, на котором будет работать дев сервер.

## 1.7 Проект состоит из из приложений
Проект в джанго стоит из приложений. Чистый проект в django уже состоит из нескольких приложений.
Список приложений можно увидеть в настройках (файл `setup.py`) в переменной `INSTALLED_APPS`
Для создания собственный приложений надо воспользоавться командой
```shell
python3 manage.py startapp app_name
```
при этом создается одноименная папка.

# 2
## 2.1
## 2.2
URL - Uniform Resource Locator. 
- http:// - протокол
- доменное имя
- ссылка на index.html - главную страницу сайта
- каждому url соответствует определённый результат на сайте
- часть совпадает с доменным именем - остальная роут - маршрут

Возможный пример роута `car-online.ru`:
- `/` - стартовая страница
- `/cars` - список марок машин
- `/cars/volvo` - список моделей марки вольво
- `/cars/volva/xc-90` - информации ок конкретной марки вольво

Для каждого ендпоинта надо описать логику. Логика описывается во views. `views.py`
Это может быть либо функция, любо класс для каждого url.

## 2.3
### urls
Переменная URLConf, которая задает список страниц на стайте (список эндпоинтов) называется `urlpatterns` и находится в файле `urls.py`. Содержит список всех url, которые обрабатываются бэкэндом.
```python
from horoscope.views import leo

urlpatterns = [
    path('leo/', leo),
]
```
При этом будет получено status код 301, если клиент зашел на ссылку без слеша. В этом случае браузер перенаправит пользователя на адрес со слешем на конце.

### views
Теперь надо определеить представление, которое будет срабатывать при переходе по новому url.
создадим нужное представление в файле `views.py`.
```python
from django.http import HttpResponse

def leo(request):
    return HttpResponse("<p>Hello from Django!</p>")
```

## 2.4 Создаем собственный URLconf. `django.urls.include`
https://django.fun/ru/docs/django/4.1/topics/http/urls/

Это файл `urls.py`.
Можно импортировать роуты из другого приложения, чтобы каждый раз не подставлять путь приложения. Таким образом можно подключить роут приложения по адресу приложения.

Для этого в индексном роуте есть инструкция по импорту дочерних роутов из приложений джанго.
Вместо приложения роуты также можно импортировать из пакета (директории с `__init__.py`).
```python
# project_folder/urls.py
from django.urls import include
from django.url import path

urlpatterns = [
    path('horoscope/', include('app_name.views')),
]
```
В дочернем роуте надо создать такой же параметр `urlpatterns`. В подключаемых роутах также можно подключить другой роут или контроллер (вьюху).
```python
# app_folder/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('leo/', views.leo),
]
```

## Отладка Django 
В VSC может придётся создать следующий файл конфигурации.
```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: Django",
            "type": "python",
            "request": "launch",
            "program": "${workspaceFolder}/django-projects/my_site/manage.py",
            "args": [
                "runserver", "777"
            ],
            "django": true,
            "justMyCode": true
        }
    ]
}
```
## 2.6 Динамические URL в Django. Динамические параметры в роутах.
Для того, чтобы Django воспринимал параметр в роуте его надо поместить в `<parameter>`.
Также данный паарметр надо передать внутрь нашего вьюшки, которая обрабатывает запросы от этого роута.
```python
# urls.py
urlpatterns = [
    path('<sign>', view.sign_controller),
]
```
```python
#views.py
def sign_controller(request, sign):
    pass
```
По-умолчанию все параметры считываются в виде строки.

## 2.7 Конвертеры роутов.
Конвертеры позволяют конвертировать динамические параметры в нужный ип данных или брать только часть url строки.

При этом система автоматически заресолвит тот роут, что первым подойдёт по списку.
Поэтому роут с самыми общими правилами надо ставить самым последним, иначе до остальных роутов дело может так и не дойти.

| конвертер | описание |
| -- | -- |
| `int` | целое число |
| `str` | строка до первого символа `/` |
| `slug` | слаг - строка из букв, цифр, `-` и `_` |
| `uuid` | тип уникального идентификатора - прописные буквы разделённые тире |
| `path` | включает не пустую строку, содержащую весь путь включая последующие `/`|

Также можно регистрировать свои собственные конверторы.

```python
# horoscope/urls.py

urlpatterns = [
    path('<str:sign_of_zodiac>', get_info_by_sign),
    path('<int:zodiac_by_number>', get_info_by_number),
]
```

```python
def get_info_by_number(request, zodiac_by_number: int):
    pass
```

## 2.8 Redirect URL. `django.http.HttpResponseRedirect` `django.shortcuts.redirect`
### `HttpResponseRedirect`
Перенаправление (redirect) - это способ перенаправления пользователей с одной страницы на другую. При этом он может пригодиться например при перенаправлении пользователей с адреса `week_days/1/` на `week_days/monday/`.

Redirect в Django можно выполнить при помощи класса `HttpResponseRedirect`:
```python
from django.http import HttpResponseRedirect

def my_controller(request):
    return HttpResponseRedirect("https://yandex.ru")
```
За классом `HttpResponseRedirect` возвращает статус код __302__.
Есть и другие классы, которые возвращают другие коды редиректа:
класс | код
-- | --
`HttpResponseRedirect` | __302__
`HttpResponsePermanentRedirect` | __301__
`HttpResponseNotModified` | __304__
### `redirect`
Это функция - обертка, которая возвращает или класс `HttpResponseRedirect` или класс `HttpResponsePermanentRedirect`.
```python
from django.shortcuts import redirect

def my_controller(request):
    return redirect('http:google.com', permanent=True,)
```

## 2.9 Псевдонимы для роутов. Функция `reverse` в Django. `django.urls.reverse`
### Псевдонимы для роутов
```python
# urls.py

urlpatterns = [
    path('some_address/', views.controller, name='route_alias_name'),
]
```
Теперь осталось передать это имя функции `reverse` в контроллере.


### `reverse`
Она позволяет генерировать url адреса согласно имени url адреса в приложении.
```python
reverse(viewname, urlconf=None, args=None, kwargs=None, current_app=None)
```
- `viewname` - строка, содержащая имя URL адреса. _Обязательный аргумент_.
- `urlconf` - позволяет задать имя модуля с настройками url адресов `urls.py`. Нужен для того, чтобы задать другой модуль со своим файлом urls.py.
- `args` - список или кортеж элементов для передачи в `url` адрес:
    ```python
    reverse('my_view_name', args=[1])
    ```
- `kwargs` - словарь именованных аргументов для передачи в url адрес.
    ```python
    reverse('my_view_name2', kwargs={'width': 2})
    ```

```python
# views.py
from django.urls import reverse

def controller(request):
    redirect_url = reverse('square_area', kwargs={'width': 2})
    return HttpResponseRedirect(redirect_url)
```
### `app_name`
Можно задать пространство имен во _urls.py_ при помощи переменной `app_name`
```python
#urls.py

app_name = "horoscope"
urls = [
    path("/", views.index, name="zodiac_index"),
]
```
тогда надо при указании псевдонимов использовать это пространство имен:
```python
# views.py

from django.urls import reverse
pass
redirect_url = reverse(viewname="horoscope:zodiac_index")
```

## 2.10 Создание главного меню
Страница для всех знаков зодиака.
Для этого надо внедрить html код.

## 2.12 Собственные конвертеры
Пример базовых конвертеров можно найти в модуле `from django.urls import converters`.
Конвертер представляет собой класс из трех составляющих:
- `regex` - переменная, описывающая регулярное выражение
- `to_python(self, value)` - функция, конвертирущая найденную строку в питоновский тип
- `to_url(self, value)` - функция, конвертирующая питоновский объект в представление строки

Данный класс помещается в файл `converters.py`.

```python
# converters.py
class FourDigitsConverter:
    regex = r'\d{4}'

    def to_python(self, value) -> int:
        return int(value)
    
    def to_url(self, value) -> str:
        return f"{value:04}"
```
Для регистрации конвертера нужно импортировать 
```python
from django.urls import register_converters
```

```python
# urls.py
from django.urls import register_converters
from . import converters    # импорт модуля с классом конвертера

# регистрация собственного конвертера
register_converters(converters.FourDigitConverter, 'yyyy')

urlpatterns = [
    path('<yyyy:year_num>', views.get_info-about_year,),
]
```

## 2.13 Тестирование представлений
Юнит тесты или модульное тестирование.
Будем тестировать отдельные модули - представления во вьюхах.
Обычно тестирование приложения в джанго происходит таким образом - что проверяются адреса страниц и то, что пользователь может по этим адресам получить контент.

Обычно тесты на уровне приложения создаются в корне проекта в каталоге `tests`.
Но мы будем писать тесты на уровне приложения в каталоге приложения - файле `tests.py`.


Создаем класс, отнаследованный от `TestCase` и внутри пишем набор функция, каждая их которых тестирует одно наше представление.

Названия методов для тестирования в классе должны начинаться с `test_`.

Самый важный для нас атрибут это `self.client`. Под ним подразумевается объект, который заменяет нам браузер. Браузер может отправлять все виды запросов (в том числе `get` и `post`).
Также есть метод `assertEqual`, который проверяет два параметра на равенство.
```python
response = self.client.get(url)
status_code = response.status_code
self.assertEqual(200, status_code)
```

```python
# tests.py
from django.test import TestCase


class TestHoroscope(TestCase):
    
    def test_index(self):
        response = self.client.get('/horoscope/')
        self.assertEqual(200, response.status_code)
    
    def test_libra_redirect(self):
        response = self.client.get('/horoscope/7/')
        self.assertEqual(302, response.status_code)
        self.assertEqual(response.url, '/horoscope/libra/')
```


Можно из ответа получить страницу в байтах: `response.content`. 
Контент можно декодировать в строку: `response.content.decode()`.
Далее можно проверить на вхождение чего-то в результате:
```python
def test_libra(self):
    response = self.client.get('/horoscope/')
    self.assertEqual(200, response.status_code)
    self.assertIn('libra', response.content.decode())
```

И конечно можно и нужно использовать функцию `reverse` для ресолва url адресов по их псевдонимам.

### Типы проверок
assert | выражение
-- | --
`assertEqual(a, b)` | `a == b`
`assertNotEqual(a, b)` | `a != b`
`assertTrue(x)` | `bool(x) is True`
`assertFalse(x)` | `bool(x) is False`
`assertIs(a, b)` | `a is b`
`assertIsNot(a, b)` | `a is not b`
`assertIsNone(x)` | `x is None`
`assertIsNotNone(x)` | `x is not None`
`assertIn(a, b)` | `a in b`
`assertNotIn(a, b)` | `a not in b`
`assertIsInstance(a, b)` | `isinstance(a, b)`
`assertNotIsInstance(a, b)` | `not isinstance(a, b)`


### Команда для запуска тестов 
Команда для запуска тестирования приложения:
```shell
python3 manage.py test app_name
```

### отладка с тестами в VSCode
```json
{
   "name":"Python: horoscope_test",
   "type":"python",
   "request":"launch",
   "python":"путь до python.exe",
   "program":"путь до manage.py",
   "args":[
      "test",
      "horoscope"
   ],
   "django":true,
   "justMyCode":true
}
```

# 3. Шаблоны и статические файлы
## 3.1 Что такое шаблон и как его создать
Django поддерживает кроме своего шаблонизатора Django Template Language ещё и сторонние шаблонизаторы, например jinja2.

Шаблон в джанге - это статические html файл, который состоит из статических частей и динамических.

Шаблоны создаются в каталоге приложения в директории `templates/`.
По договоренности внутри создается еще один каталог с именен приложений.
Например валидное такое название файла: `django_project/app_name/templates/app_name/app_name_index.html`.

Ещё может понадобиться зарегистрировать свой шаблон в файле `settings.py`.
В файле `settings.py` в переменной `TEMPLATES` хранятся настройки шаблонизатора.
- `BACKEND` - указание на язык шаблона, например `django.tempalte.backends.django.DjangoTemplates` говорит что будет использоваться язык шаблонов django template language
- `DIRS` - список. где джанго ищет шаблоны. Пути должны быть абсолютными?!
    ```python
    DIRS = [
        "BASEDIR\app_name\templates",
    ]
    ```
- `APP_DIRS` - искать ли в папках с приложением

Или можно зарегистрировать своё приложение в переменную `INSTALLED_APPS`, чтобы  шаблоны и искались среди шаблонов указанного приложения.

Чтобы вернуть файл пользователю он отправляется в представлении (вьюхе).
```python
from django.template.loader import render_to_string

def get_info(request):
    response = render_to_string('app_name/app_name_index.html')
    return HttpResponse(response)
```

## 3.2 Поиск шаблонов в Django. Коллизии имен шаблонов. `render`
Поиск шаблонов осуществляется в том порядке, в каком подключены приложения в `settings.INSTALLED_APPS`. Если шаблон нашелся по пути в каталоге `templates` первого по списку подключенного приложения, то он применится. Поэтому следует избегать одинаковых путей к шаблонам для разных приложений. Для этих целей и создают в каталоге `templates` отдельный каталог для каждого приложения, чтобы не было коллизий имён шаблонов.

### `render`
```python
from django.template.loader import render_to_string
from django.http import HttpResponse

def index(request):
    response = render_to_string('app_name/index.html')
    return HttpResponse(response)
```
можно заменить с использованием функции `render`
```python
from django.shortcuts import render

def index(request):
    return render(request, 'app_name/index.html')
```
```python
def render(
    request, template_name, context=None, content_type=None, status=None, using=None
):
    """
    Return an HttpResponse whose content is filled with the result of calling
    django.template.loader.render_to_string() with the passed arguments.
    """
    content = loader.render_to_string(template_name, context, request, using=using)
    return HttpResponse(content, content_type, status)
```
Внутри себя `render` вызывает всю ту же функцию `render_to_string` и затем возвращает `HttpResponse`.

```python
render(request, template_name, context: dict, content_type, status, using)
```
- `request` - обязательный параметр - запрос
- `template_name` - обязательный - имя шаблона
- `context` - словарь с переменными для шаблона
- `content_type` - тип возвращаемого контента `text/html`, по-умолчанию
- `status` - код ответа HTTP, default = 200
- `using` - строка с именем бд, если используется несколько бд.


## 3.3 Django Template Language DTL. Рендеринг шаблона.
В контексте `render` передаются параметры, которые встраиваются в шаблон DTL 
```python
render(request, template_name, context: dict, content_type, status, using)
```

Встраивание в шаблон:
- `{{ variable }}` - переменная
- `{{ dict.key }}` - значение по ключу в словаре
- `{{ var1 }}` `{{ var2 }}` - встраивание нескольких переменных

## 3.4 Фильтры в шаблонах Django
При помощи фильтров можно выполнять вычисления внутри шаблонов.
В шаблонах нельзя вызывать методы питона.
https://docs.djangoproject.com/en/4.2/ref/templates/builtins/#built-in-filter-reference



- `capfirst` | `{{ var|capfirst }}` | делает первую букву заглавной
- `upper` | `{{ var|upper }}` | в верхний регистр
- `title` | `{{ var|title }}` | каждая первая буква каждого слова становится заглавной
- `add` | `{{ value|add:"2" }}` `{{ var1|add:var2 }}` | прибавляет значение (2) к переменной. Работает для целых чисел, строк, массивов и т.п. Можно складывать две переменных шаблона.
- `center` | `{{ value|center:15 }}` | расширяет строку до n символов (15) и центрирует вывод значения
- `ljust` | `{{ value|ljust:"10" }}` | строка длиной 10 символов выравнивание слева
- `rjust` | `{{ value|rjust:"10" }}` | строка длиной 10 символов выравнивание к правому краю
- `cut` | `{{ string|cut:symbol }}` | удаляет все символы из строки
- `truncatewords` | `{{ contents|truncatewords:2 }}` | оставляет первые два слова и затем ставит многоточие
- `date` | `{{ value|date:"D d M Y"}}` | преобразование даты к определённому формату
- `default` | `{{ value|default:"nothing" }}` | заменяет ложные значения ("" пустую строку) на значения по-умолчанию
- `defailt_is_none` | `{{ value|default_is_none:default_value}}` | заменяет только в том случае, если значение None
- `dictsort` | `{{ books|dictsort:"author.age"}}` | возвращает отсортированный словарь
- `divisibleby` | `{{ value|divisibleby:3 }}` | проверяет делится ли число на 3. `True|False`
- `first` | `{{ value|first }}` | возвращает первый элемент последовательности
- | `{{ value.0 }}` | вывести первый элемент последовательности
- `last` | `{{ value|last }}` | последний элемент последовательности
- `length` | `{{ value|length }}` | вернёт длину последовательности
- `length_is` | `{{ value"length_is:"4" }}` | True|False Будет ли длина value == 4
- `get_digit` | `{{ value|get_digit }}` | позиция цифры с правой стороны
- `join` | `{{ value|join:" - "}}` | джоинит последовательности через разделитель
- `make_list` | `{{ value|make_list }}` | `"Joel"` -> `["J", "o", "e", "l"]`
- `random` | `{{ value|random }}` | Случайное значение из последовательности
- `safe` | `{{ value|safe }}` | Позволяет не игнорировать HTML символы в строке
- `safeseq` | `{{ value|safeseq }}` | применяет `safe` к каждому элементу последовательности
- `slice` | `{{ value|slice:"2" }}` | позволяет делать срезы
- `slugify` | `{{ value|slugify }}` | в нижний регистр и все пробелы заменяются на `-`

### Использование фильтров вне шаблона
Фильтры в Django можно использовать не только в шаблонах, но и в любом другом месте python-кода. Для этого нужно импортировать нужный фильтр из `django.template.defaultfilters` и вызвать его, передав нужные аргументы

Пример использования фильтра `slugify` вне шаблона:
```python
from django.template.defaultfilters import slugify

title = "This is a title with spaces and punctuation!"
slug = slugify(title)

print(slug)
```
Все работает как обычные функции в python: вызываете функцию, передаете аргументы, получаете результат.

## 3.5 Тэги в Django
Все тэги заключаются в `{%  %}`. Внутри тэга обычные переменные сразу доступны.

### `{% if %} {% endif %}`
Позволяет добавить условный оператор внутрь шаблона.
```python
{% if athlete_list and not coach_list != x or name in "abcdef" or name not is None %}
    Number of athletes: {{ athlete_list|length }}
{% elif athlete_in_locker_room %}
    Athletes is locker room.
{% else %}
    No athletes.
{% endif %}
```

## 3.6 Тэг `for`

```python
Удобен для рендеринга повторяющихся элементов в шаблоне.
{% for char in "abcde"|makelist %}
    <p>{{ char }}</p>
    <p>i = {{ forloop.counter }}</p>
{% endfor %}
```
Внутри цикла `for` доступны некоторые специальные переменные.
- `forloop.counter` - текущий индекс итерации c 1
- `forloop.counter0` - индекс с 0 (смещение)
- `forloop.revcounter` - номер итерации с конца
- `forloop.revcounter0` - номер итерации с конца с 0
- `forloop.first`, `forloop.last` - True, если итерация совпадает с первой/последней.
- `forloop.parentloop` - содержит объект внешнего цикла, если есть (только для вложенных циклов)

Допустима распаковка при обходе
```python
{% for i, j in values %}
```

Можно обходить словари
```python
{% for key, value in dict.items %}
    <p>
        <b>{{ key }}</b> -- {{ value }}
    </p>
{% endfor %}
```

Можно объодить значения в обратном порядке:
```python
{% for i in list reversed %}
```

### `{% empty %}`
Данный блок срабатывает когда коллекция  пустая
```html
{% for i in list %}
    <p>element: {{i}}</p>
{% empty %}
    <p>Ooops list is empty!</p>
{% endfor %}
```

## 3.7 Тэг `url`
Является аналогом метода `reverse` для шаблонов.
```python
{% url "some_url_name" v1 v2 %}
```
или
```python
{% url "some_url_name" arg1=v1 arg2=v2 %}
```

## 3.8 Наследование шаблонов в Django. Тэги `block` `extends`.
Наследование шаблона в том числе позволяет избавиться от дублирования кода.
В базовом шаблоне описываются все неизменяемые вещи - вся структура html.
Обычно базовый шаблон создают не на уровне приложения, а на уровне проекта по пути `project/templates/base.html`. Естественно, что можно его создавать и на уровне приложения.
Если шаблон будет создан на уровне проекта, то надо добавить каталог с шаблоном в `settings.py` переменную `TEMPLATES`.

Для подстановки значений в родительский шаблон существует тэг `block`:
```html
# base.html
<title>
    {% block title %}

    {% endblock %}
</title>

<body>
    {% block content %}
    {% endblock %}
</body>
```
Для использования базового шаблона, мы должны от него отнаследоваться в дочернем шаблоне при помощи конструкции тэга `extends` и можно задать значения недостающих  блоков.
```html
# index.html
{% extends 'base.html' %}


{% block title %}
Меню
{% endblock %}


{% block content %}
    <h1>Контент</h1>
{% endblock %}
```
При переопределении блока может понадобиться включить содержимое блока из родительского шаблона:
```python
{% extends 'base.html' %}

{% block content %}
  {{ block.super }}
{% endblock %}
```
## 3.8 Тэг `include`
Позволяет включать один шаблон в другой.
По умолчанию создается каталог `templates/app_name/includes`, в который помещается шаблон, например следующей структуры:
```html
<nav>
    <a href="#id1"></a>
    <a href="#id2"></a>
</nav>
```

Затем данный шаблон может быть включен в другой шаблон
```
{% block content %}
    {% block nav %}
        {% include 'app_name/includes/navbar.html' %}
    {% endblock %}
{% endblock %}

{% block footer %}
    {% include 'app_name/includes/navbar.html' %}
{% endblock %}
```

Импортируемый шаблон получает доступ ко всем переменным того шаблона, из которого он импортируется.
Чтобы импортируемому шаблону запретить доступ ко всем внутренним переменным добавляется ключевое слово `only`:
```html
{% include 'horoscope/includes/navbar.html' only %}
```

Также можно вручную задавать переменные для передачи их в подключаемый шаблон при помощи конструкции `with`:
```
{% include 'app_name/includes/navbar.html' only with a=100 b='abc' %}
```

## 3.10 Подключаем статические файлы
https://docs.djangoproject.com/en/5.0/howto/static-files/

Шаблоны не являются статикой.
К статике можно отнести css, js, и не шаблонные html файлы.
Статика может быть либо глобальной на весь проект, либо локальной на приложении.

За подключение статики отвечает строчка `django.contrib.staticfiles` в списке `INSTALLED_APPS` из файла `setting.py`.



### local
По соглашению, папка со статичными файлами в проекте `app_name/static/`. Далее в этом каталоге, по аналогии с шаблонами, создается каталог с именем приложения, где размещаются статичные файлы для избежания коллизии имён.
Далее принято разделять тип статики
- `css/`
- `js/`
- `img/`

## global static
Глобальную статику, относящуюся ко всему проекту целиком принято выносить в каталог с проектом - `project_folder/static/`.
Для добавления каталогов для поиска статичных файлов надо вписать переменную `STATICFILES_DIRS` в файл `settings.py`:
```python
# settings.py

STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static'),
]
```
В переменной `STATIC_URL` содержится путь до статичных файлов для деплоя проекта.

## settings.py
```python
STAIC_URL = 'static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```

## Подключение статики в шаблон
Для включения функциональности, связанной со статичными файлами используется тэг шаблона `{% load static %}`
```html
<head>
    {% load static %}
    <link rel="stylesheet" href="{% static 'app_name/css/style.css' %}">
</head>
```
После этой строчки можно указать какой файл `css` можно подгрузить в шаблон.
Поиск статичный файлов происходит в каталогах `static` для каждого приложения, поэтому путь отсчитывается от этого пути.

Если подключения различных стилей в каждом шаблоне можно воспользоваться блочной структурой и определить блоки, которые подключаются в заголовке.

## 3.11 Создание собственных фильтров
https://docs.djangoproject.com/en/3.2/howto/custom-template-tags/
Для создания собственных фильтров и тэгов надо создать питоновский пакет (каталог с `__init__.py`).
Название пакета - `templatetags`.
Т.е. путь каталога: `project_dir/app_name/templatetags/__init__.py`
Создаем по этому пути файл фильтра, например `my_filter.py`:
```python
# my_filter.py
from django import template

# объект для регистрации фильтров
# для регистрации навешивается как декоратор
register = template.Library()

@register.filter(name="filter_name")
def split(value, key=" ") -> list:
    return value.split(key)
```

Для добавления фильтра в щаблон его надо загрузить при помощи тэга `{% load %}` указав имя файла без расширения.
```html
{% load my_filter %}
{{ value|split:" - " }}
```
Для того, чтобы гаранитровать, что в качестве водного параметра в нащ фильтр попадет строка надо навесить на функцию ещё один декоратор:
```python
# my_filters.py

from django import template
from django.template.defaultfilters import stringfilter

register = template.Library()

@register.filter(name='split')
@stringfilter
def split(value: str, key: str = " ") -> list[str]:
    return value.split(key)
```

# 4. Базы данных. Модели. ORM
## 4.3
Настройки для джанги для подключения к бд хранятся в файле `settings.py` в переменной `DATABASES`.

Модели для ORM размещаются в файле `models.py`.
Классы представляют таблицы в бд, а экземпляры класса являются записями в таблице.
Для создания своей модели данных надо создать класс, отнаследовав его от `django.db.models.Model`
```python

from django.db import models

class Movie(models.Model):
    name = models.CharField(max_length=40)
    rating = models.Integer()   
    
    class Meta:
        verbose_name = "Фильм"
        verbose_name_plural = "Фильмы"
```
Свойство `id` создается автоматически.

Далее следует указать поля. Класс `CharField` является потомком класса `models.Field`, который является базовым для всех полей объекта.
### Типы полей в Django
- `CharField` - для строк фиксированной длины. `max_length` - обязательный параметр.
- `TextField` - для хранения строк без ограничения по длине
- `IntegerField` - целые числа
- `PositiveIntegerField` - положительные целые
- `FloatField` - вещественные числа
- `DecimalField` - числа с фиксированной точностью. Без ошибок обрабатывает количество знаков после запятой.
    - `max_digits` - макс количество цифр в числе (до и после запятой)
    - `decimal_places` - количество цифр после запятой
- `BooleanField` - True|False
- `EmailField` - для адресов электронной почты. Это поле типа CharField с некоторыми функциональными возможностями.
    - макс длина 254 символа
    - валидирует данные
- `URLField` - для хранения и валидации URL адресов. Наследуется от CharField.
    - max_length = 200
    - валидация URL адресов на соответствие шаблону
- `DateTimeField`
    - `auto_now_add: Bool`

При изменении модели может понадобиться переопределить значение по умолчанию или возможность ставить Null значения в таблице. Например при добавлении столбца в таблицу джанго не сможет понять какими значениями его заполнять, если в этом столбце будут запрещены ненулевые значения, а значение по-умолчанию будет отсутствовать. В таком случае можно либо в модели задать `default` или `null=True` перед миграцией.

### Аттрибуты Field
```python
class Field(RegisterLookupMixin):
    """Base class for all field types"""

    def __init__(
        self,
        verbose_name=None,
        name=None,
        primary_key=False,
        max_length=None,
        unique=False,
        blank=False,
        null=False,
        db_index=False,
        rel=None,
        default=NOT_PROVIDED,
        editable=True,
        serialize=True,
        unique_for_date=None,
        unique_for_month=None,
        unique_for_year=None,
        choices=None,
        help_text="",
        db_column=None,
        db_tablespace=None,
        auto_created=False,
        validators=(),
        error_messages=None,
    ):
    pass
```
- `verbose_name: str` - имя поля, которое будет отображаться в админском интерфейсе джанго и в других контекстах.
- `help_text: str` - подсказка под формой ввода в админском интерфейсе
- `null: bool` - указывает может ли поле содержать NULL значения. default: False
- `blank: bool` - (False) указываем может ли поле быть пустым в формах.
- `default: object` - определяет значения по-умолчанию для модели. Применяется только при создании объекта. При обновлении данных не затрагивает сущность.
- `unique: bool` - (False) являются ли значения в этом столбце уникальными (множеством)
    - бд создают индекс по уникальным полям, что приводит к уменьшению задержек при выборке значений по уникальным полям

### Миграции
Механизм миграция является аналогом системы версионирования в гите. Миграции отвечают за сохранения состояний в таблицах.
Для создания миграция надо выполнить команду. Предварительно не забыть добавить приложение в список приложений `INSTALLED_APPS`, иначе не изменения не будут найдены.
```python
python manage.py makemigrations
```

Для применения миграции вводится команда 
```shell
python3 manage.py migrate
```
#### dependencies
При первой миграции происходит создание таблиц, заданных в уже подключенных приложениях.
У первой миграции не будет зависимостей (поле `dependencies`). У остальных будет - это та миграция, которую надо произвести перед данной.. Таким образом выстраивается порядок применения миграций (дерево).
Начальная миграция имеет атрибут `initial  = True`.

#### operations
В этом атрибуте лежит список всех изменений, которые надо применить к текущей миграции.

#### Список миграций
Чтобы просмотреть все миграции можно воспользоваться командой 
```shell
python3 manage.py showmigrations
```
При этом миграция может оказаться намного больше, чем мы делали. Показываются все миграции, но применены к БД будут только те, которые мы сделали.
Все примененные миграции будут отмечены `x`.

### Управление миграциями

Просмотреть миграции
```shell
python3 manage.py showmigrations
```

Откатиться к конкретной миграции можно при помощи команды migrate и номера миграции (например 003).
```shell
python3 manage.py migrate app_name migration_number
```
Чтобы накатить миграции для какого-то приложения можно выполнить команду `migrate` без указания номера:
```shell
python3 manage.py migrate app_name
```

## 4.5 Добавление данных в таблицу

Можно запустить окружение питона в консоли со всеми переменными.
```shell
python manage.py shell
```
Далее в консоли можно будет импортировать нужный класс и создать экземпляр.
```python
from movie_app.models import Movie

movie = Movie(name="FilmName", rating=50)
movie.save()
```
Для того чтобы изменения применились, надо сохранить этот объект (произвести транзакцию) методом `object_name.save()`.

Чтобы вывести список sql запросов надо обратиться к модулю `django.db.connection`:
```python
from django.db import connection

print(connection.queries)
```

Добавить данные можно еще так:
```python
Movie.objects.create(name="Avatar 2", rating=30)
```
метод `save` при этом не вызывается.

### django-extensions
Также можно воспользоваться модулем `django-extensions` который позволяет сразу выводить sql запросы после выполнения транзакций.
Устанавливаем.
```shell
pip3 install django-extensions
```
Добавляем к списку приложений.
```python
INSTALLED_APPS = [
    ...,
    'django_extensions',
]
```
Запускаем консоль с параметрами `--print-sql`
```python
python manage.py shell_plus --print-sql
```

## 4.6 Выборка данных из таблиц
https://docs.djangoproject.com/en/3.2/ref/models/querysets/

В классе таблиц (отнаследованных от django.models.Model) есть специальный атрибут `objects`, который является экземпляром `Manager`. У класса `Manager` есть метод `.all()` который выводит все элементы - коллекция типа QuerySet.
```python
rows = Movie.objects.all()
```
Данный метод поддерживает операцию индексации и срезы. При этом измениться сам sql запрос к базе данных (LIMIT OFFSET).
После получения конкретной записи можно обращаться к её атрибутам через точечную нотацию.
```python
movie = Movie.objects.all()
print(movie.rating)
```

- `Model.objects.all()` - возвращает объект `QuerySet`
- `Model.objects.values()` - возвращает список словарей с полями моделей
- `Model.objects.list_values()` - возвращает список списков со значениями всх полей

## 4.7 Изменение и удаление записей
Для тоо, чтобы изменить значение в поле конкретной записи, надо е     получить, присвоить полю новое значение и затем сохранить.
```python
movie = Movies.objects.all()[2]
movie.name = "XXXX 2"
movie.save()
```
Таким образом можно изменять атрибуты только у одного объекта.

Удалять записи можно выбрать объект и применить метод `.delete()`.
```python
Movie.objects.all()[4].delete()
```

## 4.8 Фильтрация и выборка данных. `get` `filter` `exclude`
Для получения записей по фильтре у класса `Manager` (`objects`) есть метод `.get()`.
Выбираем запись по её `id`.
```python
movie = Movie.objects.get(id=5)
movie2 = Movie.objects.get(rating=2)
```
При использовании get следует быть осторожным с ем, что если записи не найдется то вызовется исключение с типом `DoesNotExist`.
В случае, если найдено более 1 объекта возникнет исключение `MultipleObjectsReturned`.
Таки образом, `get` применяется в случае выборки из уникальных свойств, и должен возвращать ровно один объект.

Для выбора нескольких объектов или не одного применяется метод `.filter()`.
Для использования более сложных запросов на сравнение не на равенство используются специальные псевдонимы полей.
```python
movies = Movie.object.filter(budget__gt=10)
```
- `__gt` - `>`
- `_gte` - `>=`
- `__lt` - `<`
- `__lte` - `<=`
- `__isnull` - is Null True|false
- `__contains='abc'` - `abc in fieldname`
- `__icontains='Abc'` - `abc in fieldname` - без чувствительности к регистру.
- `__startswith='abc'` - начинается на `abc`
- `__endswith='abc'` - заканчивается на `abc`
- `__in=[1, 2, 3]` - проверка на вхождение во множество
- `TableClass.objects.exclude(field=value)` - `!=`

Метод `exclude` работает прямо противоположено методу `filter` - отсеивает все значения, подходящие под фильтр.
В фильтре можно производить фильтрацию по нескольким полям:
```python
movies = Movie.objects.filter(name__isnull=False, budget__gt=10000)
```
или можно зачеинить операции
```python
movies = Movie.exclude(name=None).filter(budget__gt=10000)
```
Для поиска строк содержащих часть другой строки стоит использовать фильтр `__contains` или `__icontains`.

Для поиска по вхождениям во множество:
```python
movies = Movie.object.filter(id__in=[1, 2, 3])
```

## 4.9 Объект `Q`. Более сложные условия `AND` и `OR`
Для использования более сложных условий понадобится класс Q:
```python
from django.db.models import Q
```
Классом `Q` можно пользоваться так же, как и обычным фильтром. Оборачивая в него условия. Все условия также объединяются логическим И.
```python
movies = Movie.objects.filter(rating__gt=80)
movie = Movie.objects.filter(Q(rating__gt=80))
```
Но можно и явно указать союзы
```python
movies = Movie.objects.all(Q(rating__gt=80) | Q(budget__lt=100000))
```
Таким образом можно составлять более сложные запросы. Отрицание для Q выглядит боле удобно `~`.
```python
movies = Movie.objects.filter(
    Q(filter1) | Q(filter2) & ~Q(filter3)
)
```
Для получения союза `AND` можно использовать как знак `,`, так и `&`.
```python
Movie.objects.filter(Q(), Q() and Q())
```
Допускается в аргументах метода filter использовать как Q, так и обычный синтаксис, разве что в этом случае Q должен следовать первым.

## 4.10  Выводим QuerySet в шаблон
```python
movies = Movie.objects.all()
movies = movies.values
```

```html
<table>
    <tr>
        {% for key, value in movies.0.items %}
            <th>
                {{ key }}
            </th>
        {% endfor %}
    </tr>
{% for movie in movies %}    
    <tr>
        {% for key, value in movie.items %}
            <td>
                {{ value }}
            </td>
        {% endfor %}
    </tr>
{% endfor %}
</table>
```
## 4.11 Методы модели
Джанго имеет встроенные методы для получения значений, которые возвращают ответ 404 в случае, если данных не было найдено или возникло исключение при получении данных методом `get`.
```python
from django.shortcuts import get_object_or_404
from django.shortcuts import get_list_or_404

movie = get_object_or_404(klass=Movie, id=20)
movies = get_list_or_404(klass=Movie, name__isnull=False)
```
У модели (класса) можно создать собственный метод, который можно будет использовать в шаблоне. Например нам надо динамически генерировать урл адреса фильмов:
```python
class Movie(django.db.models.Model)
    def get_url(self):
        return reverse(viewname="movie_app:movie_info", args=[self.id])

    @property
    def url(self):
        return self.get_url()
```
В шаблоне к созданным методам надо обращаться без вызова
```html
<a href="{{ movie.url }}"><p>{{movie.name}}</p></a>
```

## 4.12 Поле `slug` `SlugField`
Это уникальная строка-идентификатор, которая понятна человеку
Для этого в модель добавляется дополнительное поле, для которого уже есть встроенный тип данных
```python
from django.db import models

class Movie(models.Model):
    ...
    slug = models.SlugField(
        default='',
        null=False,

    )
```
- `max_length: int`
- `min_length: int`
- `default` - значение по-умолчанию
- `null: boolean` - является ли пустым по умолчанию

Для того чтобы slug изменялся каждый раз при измении полей модели, можно переопределить метод `save`.
```python
from django.utils.text import slugify

class Movie(models.Model):
    ...

    def save(self, *args, **kwargs):
        self.slug = slugify(сфдгу=self.name, allow_unicode=True) + \
             "-" + "".join(random.choices(string.ascii_letters, k=5))
        super(Model, self).save(*args, **kwargs)
```
Для более удобной работы с русскими именами можно использовать функцию `slugify` из модуля `pytils.translit`:
```shell
pip3 install pytils
```
```python
from pytils.translit import slugify

```

Теперь можно сделать отдельную вьюху и роут для получения информации о фильме не по ид, а по слаг полю.

## 4.13 Сортировка QuerySet `order_by` `F`
Можно передать как одно поле, так и несколько. Знак `-` перед название м поля говорит об обратной порядке сортировки.
```python
movies = Movie.object.order_by('-year', 'rating', 'name')
```
Для того чтобы влиять на порядок объектов с Null элементами (начало - конец) надо импортировать класс `F` из `django.db.models`.
Для выбора порядка сортировки используется методы `asc` и `desc`:
```python
from django.db.models import F

movies = Movie.object.order_by(F('rating').desc(nulls_last=True))    # сортировка c null в конце
movies = Movie.object.order_by(F('rating').desc(nulls_first=True))    # сортировка c null в начале
# сортировка с F и без
movies = Movie.objects.sort_by(
    F('rating').desc(),
    '-year'
)
```
## 4.14 Функции агрегации
https://djangodoc.ru/3.1/topics/db/aggregation/
https://webdevblog.ru/razberaemsya-s-group-by-v-django-s-sql/

```python
from django.db.models import Sum
from django.db.models import Min
from django.db.models import Max
from django.db.models import Count
from django.db.models import Mean

mean_rating = Movie.objects.all().aggregate(Sum('budget'))
# {'budget__sum': 256000000}
movies = Movie.objects.all()
min_rating = movies.aggregate(Avg('rating'))
# {'rating__min': 50}
```

В результате операции получим словарь с именем переменной в конце будет фильтр и значение как ключ.

Можно сделать множественные агрегации:
```python
Movie.objects.aggregate(Sum('budget'), Avg('rating'), Max('year'))
# {'budget__sum': 256000000, 'rating__avg': 60.3}
```
## 4.15 django-debug-toolbar

https://django-debug-toolbar.readthedocs.io/en/latest/installation.html


## 4.16 Группировка `annotate` `values`
Для создание дополнительных полей, которые не хранятся в базе данных есть операция `annotate`.
```python
from django.db.models import Value

Movie.objects.annotate(is_year=Value('year'))
```
В этом примере мы создали одно дополнительное поле со значением везде `year`. Чтобы добавить значение надо обернуть его в объект класса `Value`.
Можно создавать несколько полей
```python
Movie.objects.annotate(is_year=Value('year'), is_active=Value(True))
```
Для создания значения но=а основе существующих колонок, нужно использовать объект F, который позволяет обращаться к колонкам внутри джанго запроса.
```python
from django.db.models import F

Movie.objects.annotate(new_budget=F('budget') * 0.9)
Movie.objects.annotate(abc=F('budget') * F('year'))
```
Важно помнить, что в случае, если в операции используется NULL, то результатом будет NULL.

Для группировки есть функция `values`. Например если надо сгруппировать по рейтингу, а потом посчитать средний рейтинг в каждой позиции:
```python
Movie.objects.values('rating').annotate(avg=Avg('budget'))
# <QuerySet [{'rating': 2, 'avg': 0.0}, {'rating': 20, 'avg': 0.0}, {'rating': 30, 'avg': 0.0}, {'rating': 50, 'avg': 0.0}, {'rating': 89, 'avg': 74500000.0}, {'rating': 90, 'avg': 25000000.0}, {'rating': 91, 'avg': 22000000.0}, {'rating': 92, 'avg': 60000000.0}]>
```

## 4.17 Тестирование моделей

Что нужно проверять при тестирование моделей
- создание и сохранение объектов моделей.
- получение и изменение данных
- валидация данных
- методы и свойства модели - проверяем, что они возвращают корректные значения.

Тесты будем писать в каталоге нашего приложения, т.е. в файле `project/app_name/tests.py`:
```python
from django.test import TestCase

class MovieModelTestCase(TestCase):
    pass
```

Джанго предоставляется специальный тестовый иснтурментарий в рамкх которого создается тетовая база данных.
Тестовая база данных будет создана отдельно для каждого теста согласно настрйокам проекта и к ней будут применены все миграциии.
После выполнения теста все теставая база данных удаляется.
### метод `setUp`
Метод `setUp` используется в джанго для настройки тестовой среды перед выполнением каждого тестового метода.
Выполняется автоматически перед каждым запуска теста класса `TestCase`.
Например перед каждым тестом мы будем создавать тестовые записи в базе данных.
```python
from django.test import TestCase
from .models import Movie


class MovieModelTestCase(TestCase)
    def setUp(self):
        print('-' * 10)
        self.print_info('Start setUp')
        self.movie = Movie.objects.create(name="Test Movie", year=1998, rating=80)
        Movie.objects.create(name="Test Matrix", year=2021, rating=90)
        Movie.objects.create(name="Test Mask", year=2023, rating=80)
        self.print_info("Finish setup")

    @staticmethod
    def print_info(message):
        count = Movie.objects.count()
        print(f"{message}: #all_movies={count}")
```

### Написание тестов
Тесты пишутся как и раньше. Тесты начинаются со слова `test` и должны содержать проверки внутри себя.
```python

class MovieModelTestCase(models.TestCase):
    ...
        def test_movie_creation(self):
        # Проверка создания объекта Movie
        self.print_info('Start test_movie_creation')
        self.assertEqual(self.movie.name, 'Test Movie')
        self.assertEqual(self.movie.rating, 80)
        self.assertEqual(self.movie.year, 2022)
        self.assertEqual(self.movie.budget, 1000000)
        self.assertRegex(self.movie.slug, r'test-movie.{4,}')
        self.print_info('Finish test_movie_creation')

    def test_movie_get_all_records(self):
        # Проверка получения всех записей из бд
        self.print_info('Start test_movie_get_all_records')
        movies = Movie.objects.all()
        self.assertEqual(len(movies), 3)
        self.print_info('Finish test_movie_get_all_records')

    def test_movie_get_record(self):
        # Проверка получения записи из бд
        self.print_info('Start test_movie_get_record')
        mask = Movie.objects.get(name='Test Mask')
        self.assertEqual(mask.year, 1995)
        self.print_info('Finish test_movie_get_record')

    def test_movie_get_url(self):
        # Проверка метода get_url()
        self.print_info('Start test_movie_get_url')
        url = self.movie.get_url()
        expected_url = reverse('movie_app:movie_info_slug', args=['test-movie'])
        self.assertRegex(url, expected_url[:-1])
        self.print_info('Finish test_movie_get_url')

    def test_movie_str(self):
        # Проверка метода __str__()
        self.print_info('Start test_movie_str')
        expected_str = 'Test Movie - 80%'
        self.assertEqual(str(self.movie), expected_str)
        self.print_info('Finish test_movie_str')

    def test_movie_save_slug(self):
        # Проверка сохранения корректного slug при сохранении объекта
        self.print_info('Start test_movie_creation')
        self.assertRegex(self.movie.slug, r'^test-movie.{5}')
        self.print_info('Finish test_movie_save_slug')

    def test_movie_budget_default_value(self):
        # Проверка значения по умолчанию для budget
        self.print_info('Start test_movie_budget_default_value')
        movie = Movie.objects.create(name='Default Budget Movie', rating=75)
        self.assertEqual(movie.budget, 0)
        self.print_info('Finish test_movie_budget_default_value')
```

# 5. Интерфейс администратора
## 5.1 Admin панель
Доступна по адресу `/admin/` через установленное приложение `django.contrib.admin`.

Для авторизации используется таблица `auth_users`, которая становится доступной после выполнения начальной миграции.
Также есть `auth_user_groups`, `auth_group_permissions`, `auth_user_user_permissions`.
Создание суперпользователя для админ панели производится командой 
```shell
python manage.py createsuperuser
```

Сразу в админке доступна панель с моделями пользователя и групп пользователей.
Можно добавить и другие панели работы с моделями.
Для этого в файле `project_dir/app_dir/admin.py` надо зарегистрировать модель:
```python
from django.contrib import admin
from .models import Movie

admin.site.register(Movie)
```

## 5.2 Настройка админ панели. Язык, заголовки
```python
# setting.py
LANGUAGE_CODE = "ru-ru"
```
```python
# admin.py

# new
admin.AdminSite.site_header = "Панель администратора"
# old
admin.site.site_header = "Панель администратора"
admin.site.site_title = "Название сайта"
admin.site.index_title = "Админка"
```
## 5.3 Настройка формы списка элементов модели
Чтобы просто добавить модель в админку
```python
#admin.py
from .models import Director

admin.site.register(Director)
```
### Добавление свойств модели
Для добавление дополнительных колонок для таблицы объектов модели нужно создать специальный класс, который дас нам возможность менять настройки и привязать его к нашей модели.
```python
# admin.py
from django.contrib import admin

class MovieAdmin(admin.ModelAdmin):
    list_display = [
        'name',
        'rating'
        'year',
        'budget',
    ]

admin.register(Movie, MovieAdmin)
```
Также привязку можно осуществить не через метод, а через декоратор `admin.register`:
```python
from django.contrib import admin
from . import models

@admin.register(models.Movie)
class MovieAdmin(admin.ModelAdmin):
    pass
```

### Более полный список параметров
- `list_display: list[str]` - список колонок свойств модели
- `list_editable: list[str]` - поля, которые можно сразу редактировать в таблице. __Нельзя__ включать первое поле из `list_fields`, т.к. оно является кликабельной ссылкой на объект.
- `list_filter: list[str]` - список колонок для фильтрации
- `search_fields: list[str]` - список колонок в которых ведётся поиск
- `ordering: list` - колонки, по которым выполняется сортировка при открытии страницы.
- `list_per_page: int` - число записей на одной странице
- `list_max_show_all: int` - максимальное число записей
- `filter_horizontal: list[str]` - список полей для которых отображается горизонтальный фильтр (для полей многие-ео-многим)
- `list_display_links` = ['first_name'] - 
- `list_select_related` = ['age'] - таблицы для связей (внешний ключ)
- `list_exclude` = ['first_name'] - 
- `list_display_links_details` = True - 
- `list_display_links_details_count` = 5 - 
- `list_filter_horizontal` = ['age'] - 
- `list_filter_vertical` = ['age'] - 
- `list_filter_inline` = ['age'] - 
- `list_filter_vertical_count` = 3 - 
- `list_filter_inline_count` = 3 - 
- `list_filter_links_details` = True - 
- `list_filter_links_details_count` = 5 - 
- `list_filter_empty_choice_label` = "Нет фильтров" - 
- `list_filter_empty_choice_label_details` = True - 

## 5.4 Вычисляемые поля в админке
Метод `annotate` для `QuerySet` позволяет создавать вычисляемые поля для запросов.
Также можно добавить дополнительные поля для админки.

Для этого внутри класса администрирования модели добавляется метод, с названием, связанным с названием новой колонки.
```python
class MovieAdmin(admin.ModelAdmin):
    ...
    list_display = [..., rating_status]    
    
    @admin.display(ordering="rating", description="статус")
    def rating_status(self, row: Movie):
        if row.rating < 50:
            return "Не очень"
        elif row.rating < 70:
            return "Смотрибельно"
        else:
            return "Хороший фильм"
```
Чтобы задать порядок сортировки только что созданному свойству можно навесить декоратор `admin.display(ordering="column_name")`

Переопределить название (которое генерируется из названия метода) можно при помощи декоратора`@admin.display(description="column name")`

## 5.5 Атрибут `choices` у поля модели
Позволяет задавать дискретные значения для поля. Часто выбирается например для значения пола, сезона года - таких значений, когда они не будут изменяться на протяжении работы программы. Плюс в том что строка малой длины и пользователю предоставляется ограниченный выбор.
Передается через список кортежей из двух значений. Первое значение а кортеже заносится в базу данных, а второе является отображаемым в административной панели для удобочитаемости.
```python
class Billing(models.Model):
    CARD = "CARD"
    CASH = "CASH"
    TYPES = {
        CARD: "оплата по карте",
        CASH: "оплата наличными",
    }
    payment_type = models.CharField(max_length=5, choices=TYPES.items())

class Movie(models.Model):
    EURO = "eur"
    DOLLARS = "usd"
    ROUBLES = "rub"
    CURRENCY_TYPES = {
        EURO: "Euro",
        DOLLARS: "US dollar",
        ROUBLES: "Russian rouble",
    }
    currency = models.CharField(
        max_length=5,
        choices=CURRENCY_TYPES.items(),
        default="ru"
    )
```

Особенность `choices` в том, что оно не создаёт ограничения на уровне базы данных, а только на уровне приложения. Поэтому после изменения модели, значения в базе данных могут изменяться от тех, что есть в choices.

## 5.6 Создание действий в админке
Надо создать метод который будет принимать два параметра - `request` и `QuerySet`. Задекорироать его `@admin.actions` и добавить в список действий `actions`
```python
from django.contrib import admin
from django.db.models import QuerySet
from .models import Movie

class MovieAdmin(admin.ModelAdmin):
    ...

    actions = ['set_dollars']

    @admin.action(description="Валюта в долларах")
    def set_dollars(self, request, query_set: QuerySet):
        count = querySet.update(currency=Movie.DOLLARS)
        self.message_user(request, f"Изменено {count} фильмов")
```
Полученное значение измененных записей можно использовать для отправки сообщений пользователю через метод `self.message_user(request, message: str)`.

- `level = django.contrib.messages.INFO` - можно менять цвет сообщений
    - INFO
    - SUCCESS
    - WARNING
- `extra_tags:str = ''`
- `fail_silently: bool = False`
```python
from django.contrib import messages

...
        self.message_user(
            self,
            message="Сообщению пользователю",
            level=messages.SUCCESS
        )
```

## 5.7 добавление поисковой панели
Для того, чтобы появилась поисковая строка надо заполнить поле `search_fields: list[str]`. 
Для того чтобы искать не в любом месте поля, добавляются дополнительные параметры к названию строки
- `__startswith` - начинается
- `__endswith` - заканчивается
- `__istartswith` - без учета регистра символов
- `__iendswith` - без учета регистра символов
- `__regex` - поиск по регулярному вырадению
- `__iregex` - без учета регистра символов
В sqlite поиск ведется неригистрозависимый.

С поиском по регистру следует быть осторожным - он может привести к ошибке 500 (например если искать по *).

```python
from django.db.models import ModelAdmin


class MovieAdmin(ModelAdmin):
    ...
    search_fields = [
        'name',
        "last_name__startswith",
        "middle_name__endswith"
    ]

```

## 5.8 Создаём фильтр в django admin
https://docs.djangoproject.com/en/4.2/ref/contrib/admin/filters/#modeladmin-list-filters
Для фильтрации добавляется новый атрибут - `list_filter: list[str]`, со списком полей, по которым мы хотим фильтроваться.
```python
from django.db.models import ModelAdmin


class MovieAdmin(ModelAdmin):
    ...
    list_filter = ['name', 'currency']
```
Но для создание собственного фильтра понадобится создать отдельный класс
```python
# admin.py

class MovieRatingFilter(admin.SimpleListFilter):
    title = "Фильтр по рейтингу"
    parameter_name = "rating"
    def lookups(self, request, model_admin):
        return (
            ("40", "низкий"),
            ("40_60", "средний"),
            ("60_80", "высокий"),
            ("80_100", "экселенц"),
        )
    
    def queryset(self, request, query_set: QuerySet):
        value = self.value() # request.GET.get('rating')
        match value:
            case "_40":
                return queryset.filter(rating__lt=40)
            case "40_60":
                return queryset.filter(rating__gte=40, rating__lt=60)
            case "60_80":
                return queryset.filter(rating__lt=60, rating__gte=80)
            case "80_100":
                return queryset.filter(rating__gte=80)
        return queryset.all() # если нет значения фильтра, то возвращаем все записи

@admin.register(models.Movie)
class MovieAdmin(admin.ModelAdmin):
    ...
    list_filter = ['name', 'year', MovieRatingFilter]
```
- `paramater_name` - задаёт имя query параметра, который передается в бэкэнд со значениями из функции `lookups`. lookups содержит список туплов, где
- 1й элемент - возможное значение query параметра `paramter_name`
- 2й элемент - удобочитаемое название значения  в админке

## 5.9 Настройка формы элемента
По умолчанию в форме отдельного элемента мы видим все поля, которые есть у модели. Но на это поведение можно поменять.
Для отображения только определённый полей нужно в классе моделиАдмин заполнить параметр `fields: list[str]` (сделать его не пустым). Чтобы поставить несколько полей в одну строчку, достаточно поместить их внутри кортежа.
```python
class MovieAdmin(admin.ModelAdmin):
    ...
    fields = ['title', ('rating', 'budget'), 'year']
    readonly_fields = ['budget']
    exclude = ['slug']
```
Поля переданные в списке для исключения `exсlude` не будут отображаться. Следует избегать добавления обязательных для заполнения полей, т.к. эти поля также исключаются из формы для добавления нового элемента, что приводит к 500 ошибке.

Можно добавить поля только для чтения, значения которых нельзя не изменять ни задавать при создании новых элементов.

Обязательноcть ввода полей задается в модели через параметр поля `blank=False|True`.
Можно задать имя поля через `verbose_name` в атрибуте параметра модели и подсказку к полю через `help_text`.

### Валидация
https://docs.djangoproject.com/en/4.0/ref/validators/#maxvaluevalidator
Часть валидаций джанго делает за нас. Но часть валидаций, которых нет в модели надо делать самостоятельно.
```python
# models.py
from django.db import models
from django.core.validators import MaxValueValidator
from django.core.validators import MinValueValidator


class Movie(models.Model):
    rating = models.PositiveIntegerField(
        validators=[MinValueValidator(0), MaxValueValidator(100)]
    )
```

### Вычисляемые поля `prepopulated_fields`
Некоторые поля, например `slug` могут вычисляться и являться обязательными для базы данных. Но пользователь не должен их задавать. Если поле вычисляется в момент сохранения (переопределён в модели метод `save()`), то поле можно скрыть из формы отдельного элемента и при создании такого элемента его заполненность будет игнорироваться, хотя он будет добавляться корректно в базу данных.
Или в модель данных можно добавить параметр `prepopulated_fields`
```python
# admin.py
@admin.register(Movie)
class MovieAdmin(models.ModelAdmin):
    ...
    prepopulated_fields = {
        'slug': ('name',),
    }
```
Благодаря `prepopulated_fields` в форме в поле для `slug` будут автоматически подставляться значения.

# 6 Связи таблиц в Django
## 6.1 Связи между таблицами
### Нормализация
Зачем вообще нужно создавать несколько таблиц в БД? Почему бы нам не хранить все данные в одной большой таблице? Не посещал ли вас такой вопрос? Давайте разбираться

Если мы будем все данные хранить в одной большой таблице, это может привести к дублированию данных. А такое повторение данных может привести к:

- созданию очень больших отношений;
- поддерживать и обновлять данные станет непросто, так как это потребует поиска множества связанных записей;
- потери и плохое использование дискового пространства и ресурсов;
- увеличивается вероятность ошибок и несоответствий.

Таким образом, чтобы справиться с этими проблемами, мы должны проанализировать и разложить отношения с избыточными данными на более мелкие, простые и хорошо структурированные отношения, которые удовлетворяют желаемым свойствам. Нормализация — это процесс разложения отношений на отношения с меньшим количеством атрибутов.

#### Что такое нормализация?
- Нормализация — это процесс организации данных в базе данных.
- Нормализация используется для минимизации избыточности отношения или набора отношений. Она также используется для устранения нежелательных характеристик, таких как аномалии вставки, обновления и удаления.
- Нормализация делит большую таблицу на меньшие и связывает их с помощью отношений.
- Нормальная форма используется для уменьшения избыточности таблицы базы данных.
Зачем нужна нормализация?

Основной причиной нормализации отношений является устранение этих аномалий. Если не устранить аномалии, это приведет к избыточности данных и может привести к нарушению целостности данных и другим проблемам по мере роста базы данных. Нормализация состоит из ряда рекомендаций, которые помогут вам создать хорошую структуру базы данных.

__Аномалией__ называется такая ситуация в таблице БД, которая приводит к противоречию в БД либо существенно усложняет обработку БД. Причиной является излишнее дублирование данных в таблице, которое вызывается наличием функциональных зависимостей от не ключевых атрибутов. Аномалии модификации данных можно разделить на три типа:

- __Аномалии-модификации__ проявляются в том, что изменение одних данных может повлечь просмотр всей таблицы и соответствующее изменение некоторых записей таблицы.
- __Аномалии-удаления__ — при удалении какого либо кортежа из таблицы может пропасть информация, которая не связана напрямую с удаляемой записью.
- __Аномалии-добавления__ возникают, когда информацию в таблицу нельзя поместить, пока она не полная, либо вставка записи требует дополнительного просмотра таблицы.

#### Как выполнить нормализацию?
Чтобы привести БД к нормальной форме, необходимо:

1. Объединить имеющиеся данные в группы.
2. Выяснить логические связи между группами. Чтобы обеспечить правильность связей, связываемые поля должны иметь один тип.
Если таблица не нормализована, она может хранить информацию о нескольких сущностях и включать в себя повторяющиеся столбцы, а они, в свою очередь, могут хранить дублируемые значения. Если же нормализована, то каждая таблица хранит информацию лишь об одной сущности.

При нормализации предполагается использование нормальных форм по отношению к структуре имеющихся данных. Есть несколько правил нормализации. Каждое из них носит название «нормальная форма» (НФ). Каждая такая форма, кроме первой, предполагает, что к данным уже применили предыдущую нормальную форму. При выполнении первого правила БД представлено в первой нормальной форме (1НФ), при выполнении трех правил — в третьей нормальной форме (3НФ).

Таких форм (уровней) — семь, однако на практике для большей части приложений вполне достаточно нормализовать БД до третьей нормальной формы (строго говоря, БД и будет считаться нормализованной, когда к ней применяется 3НФ и выше).

Да, обеспечить полное соответствие правилам и спецификациям — задача не всегда выполнимая, ведь для нормализации придется создавать дополнительные таблицы, а это не всегда приемлемо или не находит отклика у клиентов. Но если правила приходится нарушать, надо понимать, что все, связанные с этим проблемы, включая несогласованные зависимости и избыточность, будут учтены, и что это допустимо для приложения, не нарушит его работоспособность.

Мы с вами разберем три первых НФ
### Типы нормальных форм
Нормализация проходит через ряд этапов, называемых нормальными формами. Нормальные формы применяются к индивидуальным отношениям. Говорят, что отношение находится в определенной нормальной форме, если оно удовлетворяет ограничениям. Нормальная форма — требование, предъявляемое к структуре таблиц в теории реляционных баз данных для устранения из базы избыточных функциональных зависимостей между атрибутами (полями таблиц).

### Требования первой нормальной формы (1NF)
Требование первой нормальной формы (1NF) очень простое и оно заключается в том, чтобы таблицы соответствовали реляционной модели данных и соблюдали следующие реляционные принципы:

- В таблице не должно быть дублирующих строк
- В каждой ячейке таблицы хранится атомарное значение (одно не составное значение)
- В столбце хранятся данные одного типа
Пример приведения таблицы к первой нормальной форме
Следующая таблица не находится даже в первой нормальной форме, так как  в некоторых ячейках хранятся списки значений (каждый номер телефона — это одно значение) и есть повторяющиеся строки.

EMP_ID | EMP_NAME | EMP_PHONE | EMP_STATE
-- | -- | -- | --
14 | John | 7272826385, 9064738238 | UP
20 | Harry | 8574783832 | Bihar
12 | Sam | 7390372389, 8589830302 | Punjab
20 | Harry | 8574783832 | Bihar

Чтобы привести эту таблицу к первой нормальной форме, необходимо  в ячейках хранить один номер телефона, а не список, и убрать одинаковые строки.

EMP_ID | EMP_NAME | EMP_PHONE | EMP_STATE
-- | -- | -- | --
14 | John | 7272826385 | UP
14 | John | 9064738238 | UP
20 | Harry | 8574783832 | Bihar
12 | Sam | 7390372389 | Punjab
12 | Sam | 8589830302 | Punjab

### Требования второй нормальной формы (2NF)
Чтобы база данных находилась во второй нормальной форме (2NF), необходимо чтобы ее таблицы удовлетворяли следующим требованиям:

- Таблица должна находиться в первой нормальной форме
- Таблица должна иметь первичный ключ
- Все неключевые столбцы таблицы должны зависеть от первичного ключа (в случае если он составной)

> __Первичный ключ__ – это столбец или набор столбцов, по которым гарантировано можно отличить строки друг от друга, т.е. ключ идентифицирует каждую строку таблицы. По ключу мы можем обратиться к конкретной строке данных в таблице..

Если ключ составной, т.е. состоит из нескольких столбцов, то все остальные неключевые столбцы должны зависеть от всего ключа, т.е. от всех столбцов в этом ключе. Если какой-то атрибут (столбец) зависит только от одного столбца в ключе, значит, база данных не находится во второй нормальной форме.

Иными словами, в таблице не должно быть данных, которые можно получить, зная только половину ключа, т.е. только один столбец из составного ключа.

Главное правило второй нормальной формы (2NF) звучит следующим образом:

> Таблица должна иметь правильный ключ, по которому можно идентифицировать каждую строку

Пример приведения таблицы ко второй нормальной форме
У нас имеется таблица сотрудников в 1NF.

ФИО | Должность | Подразделение | Описание подразделения
-- | -- | -- | --
Булыкин И.И. | Программист | Отдел разработки | Разработка и сопровождение приложений и сайтов
Григорьев С.С.| Бухгалтер | Буeхгалтерия | Ведение бухгалтерского и налогового учета финансово-хозяйственной деятельности
Анджелина Джоли | Продавец | Отдел реализации | Организация сбыта продукции
Григорьев С.С.| Программист | Бухгалтерия | Ведение бухгалтерского и налогового учета финансово-хозяйственной деятельности

Мы видим, что она удовлетворяет условиям первой нормальной формы, т.е. в ней нет дублирующих строк и все значения атомарны.

Но мы не можем однозначно идентифицировать одну строку от другой. У нас есть сотрудники с одинаковыми именами, но при этом имеющие разные должности.

Для решения этой проблемы мы можем присвоить каждому сотруднику уникальный табельный номер, который никогда не будет изменен. Табельный номер, в нашем случае и будет являться первичным ключом, зная который мы можем четко идентифицировать каждого сотрудника, т.е. каждую строку нашей таблицы. 

Таким образом, чтобы привести эту таблицу ко второй нормальной форме, мы должны добавить в нее еще один атрибут, т.е. столбец с табельным номером.

Табельный номер | ФИО | Должность | Подразделение | Описание подразделения
-- | -- | -- | -- | --
1 | Булыкин И.И. | Программист | Отдел разработки | Разработка и сопровождение приложений и сайтов
2 | Григорьев С.С. | Бухгалтер | Бухгалтерия | Ведение бухгалтерского и налогового учета финансово-хозяйственной деятельности
3 | Анджелина  Джоли | Продавец | Отдел реализации | Организация сбыта продукции
4 | Григорьев С.С. | Программист | Бухгалтерия | Ведение бухгалтерского и налогового учета финансово-хозяйственной деятельности
В данном случае используется целочисленный тип данных, который автоматически увеличивался в случае добавления новый записи в таблицу. Тем самым мы бы точно также четко идентифицировали каждую строку в таблице

### Требования третьей нормальной формы (3NF)
Чтобы база данных находилась во третьей нормальной форме (3NF), необходимо чтобы ее таблицы удовлетворяли следующим требованиям:

- Таблица должна находиться во второй нормальной форме
- Каждый не ключевой атрибут _нетранзитивно_ зависит от первичного ключа

Проще говоря, второе правило требует выносить все не ключевые поля, содержимое которых может относиться к нескольким записям таблицы в отдельные таблицы.

#### Пример приведения таблицы к третьей нормальной форме
У нас имеется таблица сотрудников в 2NF.

Табельный номер | ФИО | Должность | Подразделение | Описание подразделения
-- | -- | -- | -- | -- |
1 | Булыкин И.И. | Программист | Отдел разработки | Разработка и сопровождение приложений и сайтов
2 | Григорьев С.С. | Бухгалтер | Бухгалтерия | Ведение бухгалтерского и налогового учета финансово-хозяйственной деятельности
3 | Анджелина Джоли | Продавец | Отдел реализации | Организация сбыта продукции
4 | Григорьев С.С. |Программист | Бухгалтерия | Ведение бухгалтерского и налогового учета финансово-хозяйственной деятельности

Чтобы определить, находится ли эта таблица в 3NF, мы должны проверить все неключевые столбцы. Каждый из них должен зависеть только от первичного ключа, и никаким образом к другим неключевым столбцам он не должен относиться.

В результате проверки мы выясняем, что столбец «_Описание подразделения_» не зависит напрямую от первичного ключа. Мы это выяснили, когда задали себе один вопрос «Каким образом описание подразделения связано с сотрудником?». И наш ответ звучит следующим образом: «Атрибут описание подразделения содержит детальные сведения того подразделения, в котором работает сотрудник».

Отсюда следует, что столбец «Описание подразделения» не связан на прямую с сотрудником, он связан напрямую со столбцом «Подразделение», который напрямую связан с сотрудником, ведь сотрудник работает в каком-то конкретном подразделении. Это и есть транзитивная зависимость, когда один неключевой столбец связан с первичным ключом через другой неключевой столбец.

Для решения этой проблемы мы должны выполнить декомпозицию - разбить нашу таблицу на две. В одной таблице будем хранить сотрудников, а во второй - подразделения. В подразделениях мы обязаны создать первичный ключ, в нашем случае это будет колонка «Идентификатор подразделения».

_Таблица подразделений в третьей нормальной форме._

Идентификатор подразделения | Подразделение | Описание подразделения
-- | -- | --
1 | Отдел разработки | Разработка и сопровождение приложений и сайтов
2 | Бухгалтерия | Ведение бухгалтерского и налогового учета финансово-хозяйственной деятельности
3 | Отдел реализации | Организация сбыта продукции

И затем для реализации связи в таблице сотрудников создать ссылку на таблицу подразделений, т.е. добавить внешний ключ.

Таблица сотрудников в третьей нормальной форме.

Табельный номер | ФИО | Должность | Подразделение
-- | -- | -- | --
1 | Булыкин И.И. | Программист | 1
2 | Григорьев С.С. | Бухгалтер | 2
3 | Анджелина Джоли | Продавец | 3
4 | Григорьев С.С. | Программист | 2

Внешний ключ связывает значения столбца «Идентификатор подразделения» таблицы подразделений  со столбцом «Подразделение» таблицы сотрудников

## 6.2 Виды связей
Один-к-одному - одна запись из левой таблицы связана с одной записью из правой таблицы
Один-ко-многим - одной записи из левой таблицы может соответствовать много записей из правой таблицы. Но у одной записи из первой таблицы только одна запись из левой.
Многие-ко-многим - одной записи из левой таблицы соответствует много записей из правой таблицы. Одной записи из правой таблицы может соответствовать много записей из левой таблицы.
## 6.3 Связь "один-ко-многим"
```python
# models.py

class Movie(models.Model)
    ...
    director = models.ForeignKey(Director, on_delete=models.PROTECT, null=True,)

class Director(models.Model):
    pass
```
С этим полем также можно работать в админке, как и другими полями модели. 
- `PROTECT` - нельзя удалить запись, пока хоть одна запись в другой таблице ссылается на нее
- `CASCADE` - при удалении запись удаляются все запись, которые на неё ссылаются
- `SET_NULL` - при удалении запись, запись в строках, ссылающиеся на неё устанавливаются в `NULL`

## 6.4 Связь "многие-ко-многим"
```python
# models.py
from django.db import models

class Movie(models):
    actors = models.ManyToManyField(to='Actor',)

class Actor(models.Model):
    MALE = 'm'
    FEMALE = 'f'
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)
    GENDER_TYPES = {MALE: "м", FEMALE: 'ж'}.items()
    gender = models.CharField(max_length=1, choices=GENDER_TYPES)
```
```python
#admin.py
class MovieAdmin(admin.ModelAdmin):
    ...
    filter_horizontal = ['actors']  # более удобный фильтр выбора актеров
    # filter_vertical = ['actors'] # другой вертикальный фильтр
```
Нельзя размещать это поле в `list_display` в админке.

## 6.5 Получение связанных объектов. Related objects.

Находим режиссёра и актёров по фильму:
```python
movie = Movie.objects.filter(director__isnull=False)
movie_director = movie.director # режиссёр фильма
movie_actors = movie.actors.all() # QuerySet всех актёров
```

```html
{% for actor in movie.actors.all %}
    <li>
        {{ actor }}
    </li>
{% endfor %}
```


Каждый раз, когда мы связываем таблицы при помощи внешнего ключа в отношении _многие-ко-многим_, джанго автоматически создаёт поле обратной связи.
Название поля обратной связи выглядит как `названиеМодели_set`. Оно представляет собой объект обратной связи. У этого объекта есть метод `all()`, который позволяет получить все записи.
Название поля обратной связи можно изменить.

Находим список фильмов режиссера
```python
class Movie(models.Model):
    ...
    director = models.ForeignKey(to=Director)

director = Director.objects.all[0]
movies = director.movie_set.all()
```
Находим список фильмов по актёру через связь многие-ко-многим:
```python
class Movie(models.Model):
    actors = models.ManyToManyField(to=Actor)
    ...
class Actor(models.Actor):
    ...

actor = Actor.objects.all()[0]
movies = actor.movie_set.all()
```

Для изменения поля обратной связи в модели служит атрибут `related_name`
```python
class Movie(models.Model):
    ...
    actors = models.ManyToManyField(to-Actor, related_name="movies")

actor = Actor.objects.all()[0]
actor_movies = actor.movies.all()
```

## 6.6 Создание записей в коде с полями `ForeignKey` и `ManyToManyField`
### ForeignKey
```shell
python manage.py shell_plus --print-sql
```

```python
ts2 = Movie.objects.filter(name__icontains="Toy Story 2")
ts2.director = None
ts2.save()
d = Director.objects.get(id=0)
ts2.director = d
ts2.save()
```

Если хотим создать нового режиссёра и добавить его к фильму, то после добавить можно уже существующий объект в базе данных. Т.е. его надо после создания сохранить
```python
d_voody = Director(
    first_name="Вуди",
    last_name="Ален",
)
voody.save()
ts3 = Movie(
    name="Toy story 3",
    rating=65,
    director=d_voody,
)
ts3.save()
```

При использовании метода `create` объект сразу сохраняется в бд, и нет необходимости вызывать метод `save`.
```python
d_voody = Director.objects.create(
    first_name="Вуди",
    last_name="Ален",
)
ts3 = Movie.objects.create(
    name="Toy story 3",
    rating=65,
    director=d_voody,
)
```
Т.е. связи можно образовывать между реально существующими объектами в базе данных.
### ManyToManyField
Связи также надо делать между существующими объектами в базе данных.
Например нельзя добавлять актёров для фильма, который ещё не был сохранён в базе данных.
```python
actor1 = Actor.objects[1]
actor2 = Actor.objects[2]
ts3.actors.add(actor1)  # добавляем актёра1
ts3.actors.add(actor2)  # добавляем актёра2

ts3.actors.remove(actor1)   # удаляем акётра
ts3.actors.remove(actor1)   # ещё раз удаляем - ошибкине будет
```

## 6.7 Связь один-к-одному
```python
class DressingRoom(models.Model):
    floor = models.PositiveIntegerField()
    number = models.PositiveIntegerField()


    def __str__(self):
        return f"Гримёрка #{self.number} на ${self.floor} этаже."


class Actor(models.Model):
    ...
    dressing_room = models.OneToOneField(
        to=DressingRoom,
        on_delete=models.SET_NULL,
        null=True,
        blank=True,
    )
```
Параметр `related_name` в атрибуте модели можно не менять, т.к. для записи один-к-одному django создаёт название поля, полностью совпадающее с названием модели.
```python
d = DressingRoom.objects.all()[0]

dressing_room_actor = d.actor   # получаем объект актера по гримёрке

a = Actor.objects.all()[0]
a.dressing_room = d
a.save()    # поулчим ошибку, т.к. это связь 1-к-1, а гримерка уже связана
```

```python
# admin.py

@admin.register(models.DressingRoom)
class DressingRoomAdmin(admin.ModelAdmin):
    list_display = ['floor', 'number', 'actor']
```

# 7. Формы и Class-Based Views
## 7.2 Создание формы
## 7.3  GET и POST запросы. CSRF

Форма отправляет запросы двух видов: GET и POST.
### GET
По-умолчанию форма отправляет GET запрос с данными проименованных полей в виде query параметров.
Для извлечения query параметров из запроса надо обратиться к полю `GET` объекта `request`:
```python
def index(request):
    query_params = request.GET  # QuerySet - аналог словаря
    pass
    # извлечение определенного значения
    name_param: str = request.GET.get('name')
```

- GET запросы могут кэшироваться
- GET запросы остаются в истории браузера
- GET запросы могут быть закладками
- GET запросы никогда не должны использоваться при работе с конфиденциальными данными
- GET запросы имеют ограничения по длине
- GET запросы должны использоваться только для извлечения данных

### POST
- POST запросы никогда не кэшируются
- Запросы POST не сохраняются в журнале обозревателя
- Запросы POST не могут быть закладками
- Запросы POST не имеют ограничений по длине данных

Чтобы изменить тип запроса на POST в форме нужно прописать параметр `method`:
```html
<form method="post">
    {% csrf_token %}
    <input name="feedback">
    <button type='submit'>
</form>
```
```python
def index(request):
    post_paramaters_dict = request.POST
    ...
```
При отправке методом `post` надо установить `csrf` токен чтобы защититься от межсайтовой подделки запросов.
Поэтому надо вставить этот токен в форму, чтобы он отправлялся вместе с данными формы.
Для извлечения пост параметров из запроса используется конструкция `data: QueryDict = request.POST`.

### адрес отправки данных
по умолчанию форма отправляет данные на тот адрес, с которого она открыта. Чтобы изменить его
### CSRF 
#### Что такое CSRF
CSRF (англ. cross-site request forgery перевод межсайтовая подделка запроса) —  вид атак на посетителей веб-сайтов, использующий недостатки протокола HTTP. Это атака, которой может подвергаться любой веб-ресурс или веб-приложение. В первую очередь это касается сайтов, которые используют cookies, сертификаты авторизации и браузерную аутентификацию. В результате атаки страдают клиенты и репутация ресурса.

Причина CSRF кроется в том, что браузеры не понимают, как различить, было ли действие явно совершено пользователем (как, скажем, нажатие кнопки на форме или переход по ссылке) или пользователь неумышленно выполнил это действие (например, при посещении _bad.com_, ресурсом был отправлен запрос на _good.com/some_action_, в то время как пользователь уже был залогинен на _good.com_).

#### Пример атаки
Атака осуществляется путём размещения на веб-странице ссылки или скрипта, пытающегося получить доступ к сайту, на котором атакуемый пользователь заведомо (или предположительно) уже аутентифицирован. Например, пользователь Алиса может просматривать форум, где другой пользователь, Боб, разместил сообщение. Пусть Боб создал тег <img>, в котором в качестве источника картинки указал URL, при переходе по которому выполняется действие на сайте банка Алисы, например:

>   Боб: Привет, Алиса! Посмотри, какой милый котик:     
```html
​​​​​​​<img src="http://bank.example.com/?account=Alice&amount=1000000&for=Bob">
```
Если банк Алисы хранит информацию об аутентификации Алисы в куки, и если куки ещё не истекли, при попытке загрузить картинку браузер Алисы отправит куки в запросе на перевод денег на счёт Боба, чем подтвердит аутентификацию Алисы. Таким образом, транзакция будет успешно завершена, хотя её подтверждение произойдет без ведома Алисы.

#### Как от нее защититься?
Эффективным и общепринятым на сегодня способом защиты от CSRF-Атаки является токен. Под токеном имеется в виду случайный набор байт, который сервер передает клиенту, а клиент возвращает серверу.

Защита сводится к проверке токена, который сгенерировал сервер, и токена, который прислал пользователь.

#### Что такое CSRF-token и как он работает
В общем понимании __токен__ — это механизм, который позволяет идентифицировать пользователя или конкретную сессию для безопасного обмена информацией и доступа к информационным ресурсам. Токены помогают проверить личность пользователя (например, клиента, который онлайн получает доступ к банковскому счёту). Их используют как вместо пароля, так и вместе с ним. Токен — это в каком-то смысле электронный ключ.

__CSRF-token__ — это максимально простой и результативный способ защиты сайта от CSRF-мошенников. Он работает так: сервер создаёт случайный ключ (он же токен) и отправляет его браузеру клиента. Когда браузер запрашивает у сервера информацию, сервер, прежде чем дать ответ, требует показать ключ и проверяет его достоверность. Если токен совпадает, сессия продолжается, а если нет — прерывается. Токен действителен только одну сессию — с новой сессией он обновляется.

Чтобы получить ответ от сервера, используются разные методы запроса. Условно они делятся на две категории: те, которые не изменяют состояние сервера (GET, TRACE, HEAD), и те, которые изменяют (PUT, PATCH, POST и DELETE). Последние имеют большую CSRF-уязвимость и поэтому должны быть защищены в первую очередь.

При создании и использовании токена должны соблюдаться следующие условия:

нахождение в скрытом параметре;

генерация с помощью генератора псевдослучайных чисел;

ограниченное время жизни (одна сессия);

уникальность для каждой транзакции;

устойчивый к подбору размер (в битах);

невозможно переиспользовать.

##### Источники
- [Хабр](https://ru.wikipedia.org/wiki/%D0%9C%D0%B5%D0%B6%D1%81%D0%B0%D0%B9%D1%82%D0%BE%D0%B2%D0%B0%D1%8F_%D0%BF%D0%BE%D0%B4%D0%B4%D0%B5%D0%BB%D0%BA%D0%B0_%D0%B7%D0%B0%D0%BF%D1%80%D0%BE%D1%81%D0%B0)
- [Что означает ошибка «CSRF токен истек»](https://help.reg.ru/hc/ru/articles/4417799125777-%D0%A7%D1%82%D0%BE-%D0%BE%D0%B7%D0%BD%D0%B0%D1%87%D0%B0%D0%B5%D1%82-%D0%BE%D1%88%D0%B8%D0%B1%D0%BA%D0%B0-CSRF-%D1%82%D0%BE%D0%BA%D0%B5%D0%BD-%D0%B8%D1%81%D1%82%D0%B5%D0%BA-)

- [Вика](https://ru.wikipedia.org/wiki/%D0%9C%D0%B5%D0%B6%D1%81%D0%B0%D0%B9%D1%82%D0%BE%D0%B2%D0%B0%D1%8F_%D0%BF%D0%BE%D0%B4%D0%B4%D0%B5%D0%BB%D0%BA%D0%B0_%D0%B7%D0%B0%D0%BF%D1%80%D0%BE%D1%81%D0%B0)

__Сессия__ - это установленное tcp connection (status "CONNECTED"). Текущие можно посмотреть так:
```shell
netstat -na | grep "CONNECTED"
```

## 7.4 Ручная валидация формы
```python
def index(request):
    if request.method == 'POST':
        name = request.POST.get('name')
        if not name:
            return render(
                request,
                'feedback/feedback.html',
                {'error-name': True}
            )
        return HttpResponseRedirect(
            reverse=(viewname='feedback:done')
        )
    return render(..., {'error-name': False})

def done(request):
    return render(request, '/templates/feedback/done/html')
```
Не очень удобно выполнять проверку на все поля формы вручную.

## 7.5 Класс Django-Form
Формы для джанго пишутся в отдельном файле `app_name/forms.py`
```python
# forms.py
from django import forms

class FeedbackForm(forms.Form):
    name = forms.CharField(
        max_length=100,
        label="имя",
    )
    surname = forms.CharField(
        max_length=100,
        min_length=2,
        required=False,
        label="фамилия",
    )
    feedback = forms.CharField(
        label="отзыв",
        widget=forms.Textarea(attrs={"rows": "3", "cols": "40"}),
    )
```

Затем в шаблон вставляется отдельный объект формы через контекст:

```python
# views.py
from .forms import FeedbackForm

def index(request):
    if request.method == "POST"
        form = FeedbackForm(request.POST)
        if form.is_valid:
            print(form.cleaned_data)    # словарь со значениями формы
            return HttpResponseRedirect(reverse("feedback:done"))
    form = FeedbackForm()
    return render_template(
        request,
        'feedback/feedback.html',
        {'forms': form},
    )
```

```html
<form action="" method="post">
    {% csrf_token %}
    {{ form }}
    <input type="submit">
</form>>
```

## 7.2 Валидация формы
```python
# forms.py
class FeedbackForm(forms.Form):
    name = forms.CharField(
        min_length=2,
        max_length=10,
        label="Имя",
        error_messages = {
            "min_length": "Мало символов",
            "max_length": "Много символов",
            "required": "Должно что-то быть",
        },
    )
    rating = forms.IntegerField(
        min_value=1,
        max_value=5,
        initial=1,
    )

```
В данном примере, мы отправляем невалидную форму обратно клиенту с уже заполненными полями, а не пустую форму.
```python
# views.py
def index(request):
    if request.method == 'POST':
        form = FeedbackForm(data=request.POST)
        if form.is_valid:
            pass
            # ...
        else:
            # ...
            pass
    else:
        form = FeedbackForm()

    return render(
        request,
        'feedback.html',
        {'form': form},
    )
```
Таким образом сначала у клиента форма будет валидироваться через html атрибуты тегов, затем в джанго через параметры поля формы.

## 7.7 Ручная настройка полей и стилей
Можно выводить не все поля формы целиком, а вручную поэлементно
```html
<form>
    {% csrf_token %}

    {{ form.name.label_tag }}
    {{ form.name.errors }}
    {{ form.name }}

    {{ form.surname.label_tag }}
    {{ form.surname.errors }}
    {{ form.surname }}
</form>
```
Чтобы вручную не писать каждое поле, то структуру всех полей записывают в цикле:
```html
{% load static %}
<head>
    <link rel="stylesheet" href="{% static 'feedback/css/form.css' %}">
</head>

<body>

<form>
    {% csrf_token %}
    {% for field in form %}
        <div class="form-style {%if field.errors %}error-class{% endif %}">
            {{ field.label_tag }}
            {{ field.errors }}
            {{ field }}
        </div>
    {% endfor %}
</form>
</body>
```
Можно динамически добавлять класс тем полям, которые с ошибками написав условие прямо в коде.


## 7.8 Сохранение данных с помощью БД
Создадим модель данных для формы
```python
# models.py

class Feedback(models.Model):
    name = models.CharField(max_length=100)
    surname = models.CharField(max_length=100)
    feedback = models.TextField()
    rating = models.PositiveIntegerField()

```
ВО вьюшке сохраним объект модели из формы после валидации и сохраним его в базу данных.
```python
# views.py
from .forms import FeedbackForm
from .models import Feedback

...
if form.is_valid:
    feed = Feedback(
        name=form.cleaned_data.get('name'),
        surname=form.cleaned_data.get('surname'),
        feedback=form.cleaned_data.get('feedback'),
        rating=form.cleaned_data.get('rating'),
    )
    feed.save()

```
или более короче, если поля формы соответствуют модели
```python
...
if form.is_valid:
    Feedback.objects.create(**form.cleaned_data)
...
```

## 7.9 ModelForm
Описание модели для формы очень похоже на форму.
Чтобы не повторять все записи, в джанге можно создать форму из модели. 
```python
# forms.py
from django import forms
from .models import Feedback

class FeedbackForm(ModelForm):
    class Meta:
        model = Feedback
        # fields = ['name', 'surname', 'feedback', 'rating']
        exclude = []
        widgets = {
            'name': forms.TextInput(attrs={'class': 'form-control'}),
            'surname': forms.TextInput(attrs={'class': 'form-control'}),
            'feedback': forms.Textarea(attrs={'class': 'form-control'}),
            'rating': forms.NumberInput(attrs={'class': 'form-control'}),
        }
```
Т.к. есть прямая связь между формой и моделью, уже не надо в объект модели передавать все поля формы, мы сразу получаем объект модели из формы.
```python
# views.py

def index(request) -> HttpResponse:
    if request.method == "POST":
        form = FeedbackForm(request.POST)
        if form.is_valid():
            form.save()
            return HttpResponseRedirect(reverse("feedback:done"))
    else:
        form = FeedbackForm()

    return render(
        request,
        "feedback/feedback.html",
        {"form": form, "feedback_id": 3},
    )
```

### Пример как создать кастомное поле с html ограничениями на макс значение
```python
class IntegerRangeField(models.IntegerField):
    def __init__(self, verbose_name=None, name=None, min_value=None, max_value=None, **kwargs):
        self.min_value, self.max_value = min_value, max_value
        models.IntegerField.__init__(self, verbose_name, name, **kwargs)

    def formfield(self, **kwargs):
        defaults = {'min_value': self.min_value, 'max_value': self.max_value}
        defaults.update(kwargs)
        return super(IntegerRangeField, self).formfield(**defaults)
```

## 7.10 Изменение данных через форму
```python

def update_feedback(request, feedback_id: int) -> HttpResponse:
    feed = get_object_or_404(klass=Feedback, id=feedback_id)
    if request.method == "POST":
        form = FeedbackForm(data=request.POST, instance=feed)
        if form.is_valid():
            feed.save()
            return HttpResponseRedirect(
                reverse(
                    viewname="feedback:update_feedback", args=(feedback_id,)
                )
            )
    else:
        form = FeedbackForm(instance=feed)
    return render(request, "feedback/feedback.html", {"form": form})
```
При создании формы из класса ModelForm можно её сразу привязывать к существующему объекту из базы данных.
```python
feed = get_object_or_404(klass=Feedback, id=feedback_id)
form = FeedbackForm(data=request.POST, instance=feed)
```

## 7.11 Class Based Views
Это представление на классах. Можно реализовывать логику внутри `views.py` при помощи классов а не функций.
Существует несколько вариантов. Будет рассмотрен только один - самый простой.
При использовании CBV название методов должны совпадать с именем http методов
```python
# views.py
from django.views import View

class FeedbackView(View):
    def get(self, request):
        form = FeedbackForm()
        return render(
            request, 'feedback/feedback.html', {'form': form},
        )

    def post(self, request):
        form = FeedbackForm(**request.POST)
        if form.is_valid():
            form.save()
            return HttpResponseRedirect(reverse("feedback:done"))
        return render(
            request, 'feedback/feedback.html', {'form': form},
        )

```
Для привязки роутов к CBV используется метод класса `.as_view()`
```python
from . import views

urlpatterns = [
    path('', views.FeedbackView.as_view(), name="feedback_index")
]
```

## Итоги
```python
# views.py
class FeedbackView(View):
    def get(self, request):
        form = FeedbackForm()
        return render(
            request,
            "feedback/feedback.html",
            {"form": form},
        )

    def post(self, request):
        form = FeedbackForm(request.POST)
        if form.is_valid():
            form.save()
            return HttpResponseRedirect(reverse(viewname="feedback:done"))
        return render(
            request,
            "feedback/feedback.html",
            {"form": form},
        )


class DoneView(View):
    def get(self, request) -> HttpResponse:
        return HttpResponse("Спасибо, ваш отзыв получен")


class FeedbackUpdateView(View):
    def get(self, request, feedback_id: int) -> HttpResponse:
        feed = get_object_or_404(klass=Feedback, id=feedback_id)
        form = FeedbackForm(instance=feed)
        return render(
            request,
            "feedback/feedback.html",
            {"form": form},
        )

    def post(self, request, feedback_id: int) -> HttpResponse:
        feed = get_object_or_404(klass=Feedback, id=feedback_id)
        form = FeedbackForm(data=request.POST, instance=feed)
        if form.is_valid():
            form.save()
        return render(
            request,
            "feedback/feedback.html",
            {"form": form},
        )


def update_feedback(request, feedback_id: int) -> HttpResponse:
    feed = get_object_or_404(klass=Feedback, id=feedback_id)
    if request.method == "POST":
        form = FeedbackForm(data=request.POST, instance=feed)
        if form.is_valid():
            print(f"{form = }", request.POST)
            feed.save()
            return HttpResponseRedirect(
                reverse(
                    viewname="feedback:update_feedback", args=(feedback_id,)
                )
            )
    else:
        form = FeedbackForm(instance=feed)
    return render(request, "feedback/feedback.html", {"form": form})
```
```python
# urls.py
urlpatterns = [
    path(
        "feedback/<int:feedback_id>/",
        views.FeedbackUpdateView.as_view(),
        name="update_feedback",
    ),
    path("feedback/", views.FeedbackView.as_view(), name="index"),
    path("done/", views.DoneView.as_view(), name="done"),
]
```

```python
# models.py
from django.db import models


class Feedback(models.Model):
    name = models.CharField(max_length=100, verbose_name="имя")
    surname = models.CharField(max_length=100, verbose_name="фамилия")
    feedback = models.TextField(verbose_name="отзыв")
    rating = models.PositiveIntegerField(verbose_name="рейтинг")
```

```python
# forms.py
from django import forms
from django.forms import ModelForm
from .models import Feedback


class FeedbackForm(ModelForm):
    class Meta:
        model = Feedback
        exclude = []
        widgets = {
            'name': forms.TextInput(attrs={'class': 'form-control'}),
            'surname': forms.TextInput(attrs={'class': 'form-control'}),
            'feedback': forms.Textarea(attrs={'class': 'form-control'}),
            'rating': forms.NumberInput(attrs={'class': 'form-control'}),
        }
```

# 8 Class Based Views. Представления, основанные на классах.
## 8.1 Введение в CBV
## 8.2 TemplateView
Специальный класс, чтобы сразу отдавать шаблон а не наследоваться от метода View и писать метод get с выдачей шаблона через render.
```python
# views.py
from django.views.generic.base import TemplateView

# class DoneView(View):
#     def get(self, request) -> HttpResponse:
#         return render(
#             request,
#             "feedback/done.html",
#         )

class DoneView(TemplateView):
    template_name = 'feedback/done.html'
```


Т.к. этот класс наследуется от View, то у него также есть метод `.as_view()` для создания представления.
```python
# urls.py

urlpatterns = [
    path('done/', views.DoneView.as_view(), name='done')
]
```
### Передача параметров в шаблон
Чтобы передать параметры в шаблон, нужно у класса переопределить родительский метод `get_context_data`
```python
class DoneView(TemplateView):
    template_name = 'feedback/done.html'

    def get_context_data(self, **kwargs):
        context: dict = super().get_context_data(**kwargs)
        context['name'] = 'ivanov'
        context['data'] = '23.03.23'
        return context
```
Таким образом эти переменные будут доступны внутри шаблона.
Вот пример как можно получать динамический параметр в таком классе:
```python
# views.py
# class FeedbackInfoView(View):
#     def get(self, request, feedback_id: int) -> HttpResponse:
#         feedback = get_object_or_404(klass=Feedback, id=feedback_id)
#         return render(
#             request,
#             "feedback/feedback_info.html",
#             {"feedback": feedback},
#         )


class FeedbackInfoView(TemplateView):
    template_name = "feedback/feedback_info.html"

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        feedback_id: int = kwargs.get("feedback_id")
        feedback = get_object_or_404(klass=Feedback, id=feedback_id)
        context["feedback"] = feedback
        return context
```

## 8.3 ListView
Задача этого шаблона - отображать данные из бд, т.е. несколько объектов модели данных.
По-умолчанию данный шаблон список объектов помещает в `context['object_list']`.
Чтобы переопределить имя этого атрибута надо переопределить `context_object_name`
```python
from django.views.generic import ListView

# class FeedbackListView(TemplateView):
#     template_name = "feedback/list_feedback.html"

#     def get_context_data(self, **kwargs):
#         context = super().get_context_data(**kwargs)
#         context["feedbacks"] = Feedback.objects.all()
#         return context
class FeedbackListView(ListView):
    template_name = 'feedback/list_feedback.html'
    model = Feedback
    context_object_name = 'feedbacks'
```
Также доступен полный перечень стандартных методов, например `get_context_data`.
С помощью переопределения родительского метода `get_queryset` (возвращает множество объектов модели тип `QuerySet`) можно фильтровать значения.
```python
from django.views.generic import ListView

class FeedbackList(ListView):
    template_name = 'feedback/list_feedback.html'
    model = Feedback
    context_object_name = 'feedbacks'
    
    def get_queryset(self):
        # получаем от родительского метода список всех объектов
        queryset = super().get_queryset()
        # возвращает записи с рейтингом >= 4
        qs = queryset.filter(rating__gte=4)
        return qs
```
Если хотите дополнительно передать другие аргументы из views в шаблон, то можете использовать переменную extra_context, где передать словарь. Например, 
```python
extra_context = {'agg': Actor.objects.aggregate(Count('id'))}
```
## 8.4 DetailView
Для отображения одной записи из базы данных.
Для того, чтобы класс `DetailView` понял как ему получать элемент из базы данных, надо определённым образом задавать имя аргумента во вьюхе:
- `pk` - поиск по первичному ключу
- `slug` - поиск по слагу
```python
# urls.py
urlpatterns = [
    path('feedback/<int:pk>', views.FeedbackInfoView.as_view()),
    path('movies/<slug:slug>', views.MovieInfoView.as_view()),
]
```

```python
# views.py
from django.views.generic import DetailView

class FeedbackInfoView(DetailView):
    template_name = 'feedback/'
    model = Feedback
    # context_object_name = 'feedback' # по умолчанию и так будет таким же
```
Имя переменной в контексте, в которой данный класс сохраняет значение объекта - это имя класса в нижнем регистре. Например для класса `Feedback` будет существовать соответствующий ключ в `context['feedback']`.
Через `context_object_name` можно изменить это поведение.

### От базового класса
Можно сразу реализовать это действие через потомков базового класса, просто создав инстанс от `DetailView` и передав в него необходимые параметры:
```python
# urls.py

urlpatterns = [
    path(
        'feedbacks/<int:pk>',
        DetailView(
            template_name='feedback/feedback_info.html',
            model=Feedback,
            context_object_name='feedback',
        ),
    )
]
```
## 8.5 FormView
```python
# views.py

from django.views.generic import FormView

# class FeedbackView(View):
#     def get(self, request):
#         form = FeedbackForm()
#         return render(
#             request,
#             "feedback/feedback.html",
#             {"form": form},
#         )

#     def post(self, request):
#         form = FeedbackForm(request.POST)
#         if form.is_valid():
#             form.save()
#             return HttpResponseRedirect(reverse(viewname="feedback:done"))
#         return render(
#             request,
#             "feedback/feedback.html",
#             {"form": form},
#         )

class FeedbackView(FormView):
    # для метода get достаточно определить модель и шаблон
    model = FeedbackForm
    template_name = 'feedback/feedback.html'
    # post
    success_url = '/done'
```
Для определения метода `get` достаточно в наследуемом классе определить `template_name` и модель формы `model`.
Для отработки отправки формы в классе формы надо определить `success_url` - путь по которому будет отправляться форма при сабмите.
И указать классу что далеть с данными формы.
Для этого надо переопределить метод `FormView.form_valid`.
```python
class FeedbackView(FormView):
    template_name = ...
    model = FeedbackForm
    success_url = '/done'
    def form_valid(self, form):
        form.save()
        return super(FeedbackView, self).form_valid(form)
```

## 8.6 `CreateView` `UpdateView`
Предназначены для создания и изменения элементов
### `CreateView`
```python
from django.views.generic import CreateView

class FeedbackView(CreateView):
    template_name = 'feedback/feedback.html'
    # класс модели
    model = Feedback
    # класс формы для модели
    from_class = FeedbackForm
    # урл успешного перехода
    success_url = '/done/'
```
Если не указать класс с формой, то джанго может сформировать форму на основе модели, если указать атрибут fields:
```python
class FeedbackView(CreateView):
    model = Feedback
    fields = ['name', 'surname', ] # отобразить не все поля
    fields = '__all__'  # отобразить все поля

    success_url = '/done/'
    template_name = 'feedback/feedback.html'
```
При этом может столкнуться с ограничениями модели при отправки не всех полей в форме.
### `UpdateView`
Цель - изменение данных в бд
```python
# views.py
from django.views.generic import UpdateView

# class FeedbackUpdateView(View):
#     def get(self, request, feedback_id: int) -> HttpResponse:
#         feed = get_object_or_404(klass=Feedback, id=pk)
#         form = FeedbackForm(instance=feed)
#         return render(
#             request,
#             "feedback/feedback.html",
#             {"form": form},
#         )

#     def post(self, request, feedback_id: int) -> HttpResponse:
#         feed = get_object_or_404(klass=Feedback, id=pk)
#         form = FeedbackForm(data=request.POST, instance=feed)
#         if form.is_valid():
#             form.save()
#         return render(
#             request,
#             "feedback/feedback.html",
#             {"form": form},
#         )

class FeedbackUpdateView(UpdateView):
    model = Feedback
    form_class = FeedbackForm
    success_utl = '/done/'
    template_name = 'feedback/feedback.html'
```
Ну забыть поменять ключ в urls.py на `pk` или `slug`.
Также, как и с `CreateView` можно не указывать класс формы, а указать список полей, например `fields = '__all__'`.
### `DeleteView`
```python
# views.py
from django.views.generic import DeleteView

class FeedbackDeleteView(DeleteView):
    model = Feedback
    # куда будет выполнен переход после удаления
    # если ошиюка, писать строку
    success_url = reverse('feedback:all_feedbacks')
    template_name = 'feedback/feedback_delete.html'
```
Добавим путь, по которому будет работать этот класс.
```python
# urls.py

urlpatterns = [
    ...,
    path(
        'feedback/delete/<int:pk>/',
        FeedbackDeleteView.as_view(),
        name='delete_feedback'
    )
]
```
Далее можно создать форму, которая отправляет `POST` запрос на url, к которому привязан этот класс. Сработает метод пост, который удалит ассоциированную запись по `pk` или по `slug`.

Форму можно создать на любой странице, т.о. переход на форму удаления производиться не будет. Если ну указать имя шаблона, то метод `GET` не будет реализован для этого url.

# 9. Работа с файлами
## 9.1 Загрузка файла с формы
Создадим форму дял добавления изображения
```html
{% extends 'feedback/base.html' %}

{% block title %} Добавление файла в галерею {% endblock %}

{% block content %}
    <h2>Загрузите файл</h2>
    <form method="post" enctype="multipart/form-data">
        {% csrf_token %}
        <input type="file" name="image">
        <button type="submit">Загрузить</button>
    </form>
{% endblock %}
```


## 9.2
https://docs.djangoproject.com/en/5.0/ref/files/uploads/#django.core.files.uploadedfile.UploadedFile

При отправке форме файлы появляются в `request.FILES`. Главное не забыть прописать параметр у формы `enctype="multipart/form-data"`, чтобы файлы перевелись в двоичный вид и были отправлены.
```python
request.FILES.get('image'): InMemoryUploadFile # специальный тип данных

```
Напишем функцию, которая будет принимать данный объект и записывать его в файловую систему:
```python
from werkzeug.utils import secure filename
from uuid import uuid4

class UploadView(View):
    @staticmethod
    def save_file(file):
        filename = str(uuid4()) + "-" + secure_filename(file.name)
        with open(file=f"gallery_tmp/{filename}", mode="wb+") as f:
            for chunk in file.chunks():
                f.write(chunk)

    def get(self, request):
        form = ImageUploadForm()
        return render(request, "gallery/upload.html", {"form": form})

    def post(self, request):
        file = request.FILES.get("file")
        if not file:
            return HttpResponseRedirect(reverse("gallery:upload"))
        self.__class__.save_file(file)
        return HttpResponseRedirect(reverse("gallery:index"))
```
Записываем не весь файл целиком, а кусками (чанками), чтобы можно было работать с большими файлами.

### Directory Traversal Attack
https://stepik.org/lesson/730393/step/2?discussion=6567371&unit=731895
Довольно важно тут сказать про directory traversal attack и защититься от неё ([пример](https://stackoverflow.com/questions/45188708/how-to-prevent-directory-traversal-attack-from-python-code)), чтобы те кто следуют курсу потом не выкатили приложение с допущенной этой ошибкой и не перезаписали себе системные файлы :)
- https://ru.stackoverflow.com/questions/587855/%D0%98%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5-werkzeug-secure-filename-%D1%81-%D1%80%D1%83%D1%81%D1%81%D0%BA%D0%B8%D0%BC%D0%B8-%D1%81%D0%B8%D0%BC%D0%B2%D0%BE%D0%BB%D0%B0%D0%BC%D0%B8
- https://stackoverflow.com/questions/45188708/how-to-prevent-directory-traversal-attack-from-python-code

## forms FileField
```python
# forms.py

from django import forms

class ImageUploadForm(forms.Form):
    file = formsFileField(
        required=True,
    )
```
```python
# views.py
from .forms import ImageUploadForm

class UploadView(View):
    @staticmethod
    def save_file(file):
        filename = str(uuid4()) + "-" + secure_filename(file.name)
        with open(file=f"gallery_tmp/{filename}", mode="wb+") as f:
            for chunk in file.chunks():
                f.write(chunk)

    def get(self, request):
        form = ImageUploadForm()
        return render(request, 'gallery/upload.html', {'form': form})

    def post(self, request):
        form = ImageUploadForm(
            data = request.POST,
            files = request.FILES,
        )
        if nor form.is_valid():
            return render(request, 'gallery/upload.html', {'form': form})
        self.__class__.save_file(form.cleaned_data['file'])
```

## 9.3 model FileField
В модели FileField хранятся только ссылки на файлы, а не сами файлы.

```python
# settings.py

MEDIA_ROOT = os.path.join(BASE_DIR, "uploads")
```

```python
# models.py
from form_project import settings

class ImageModel = (models.Model):
    image = models.FileField(
        upload_to=settings.MEDIA_ROOT
    )
```
Путь указанный в `upload_to` строится от каталога проекта. Если такого пути нет, то он создастся. По этому пути будут сохраняться файлы, которые были переданы в модель, а в базе сохраняться пути на файлы.
Чтобы файлы хранились не в каталоге, а в указанной папке, в `settings.py` создается переменная `MEDIA_ROOT` и тогда уже в ней будет создаваться каталог, а не в корне проекта.

Если джанго встретятся повторяющиеся имена файлов, то будут добавлены случайные символы к имен файла, чтобы избежать дубликатов. также все пробелы в названии будут заменены на _.


```python
# views.py

class UploadView(View):
    def get(self, request):
        form = ImageUploadForm()
        return render(request, "gallery/upload.html", {"form": form})

    def post(self, request):
        form = ImageUploadForm(data=request.POST, files=request.FILES)
        if not form.is_valid():
            return render(request, "gallery/upload.html", {"form": form})
        object = ImageModel(image=form.cleaned_data['file'])
        object.save()
        return HttpResponseRedirect(reverse("gallery:index"))
```
### Работа с объектом FileField
Обращаясь к полю `FileField` мы получаем набор методов, которые поддерживает файл.
- read() - прочитать содержимое `ImageModel.objects.all()[0].image.read()`

### CreteView, FormModel
Можно создать форму загрузки на основе модели и использовать её в при создании вьюхи через класс `CreateView`
```python
# forms.py
from django import forms
from .models import ImageModel

# class ImageUploadForm(forms.Form):
#     file = forms.FileField(
#         allow_empty_file=False,
#         required=True,
#         label="файл",
        
#     )

class ImageUploadForm(forms.ModelForm):
    class Meta:
        model = ImageModel
        exclude = []
```
```python
# views.py
class UploadView(CreateView):
    template_name = 'gallery/upload.html'
    model = ImageModel
    form_class = ImageUploadForm
    # fields = "__all__"
    success_url = '/gallery'
```

## 9.4 Отображение файлов в шаблоне
```python
# views.py
from django.view.generic import ListView

class IndexView(ListView):
    template_name = 'gallery/index.html'
    model = ImageModel
    context_object_name = 'items'
```

```html
<img src="{{ image.image.url }}" width="100">
```

У объекта из списка, передаваемогов в шаблон есть стрибуты `.path` и `.url`.
- `.path`  - абсолютный путь по которому файл хранится на сервере. Нету доступа у браузера.
- `.url` - путь от корня проекта. Нету доступа по-молчанию.
По дефолту есть доступ только к статическим файлам.
Как получить доступ?

Где забирать? - Путь по которому джанго будет отдавать статику.
```python
# setting.py

# адрес для браузера, по которому сервер отдаёт статику
MEDIA_URL = "/my-media/"
```
Что отдавать? В головном файле `urls.py`:
```python
# projext_folder/project_name/urls.py
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    ...
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```
Данная функция мапит внешнее расположение MEDIA_ROOT и путь MEDIA_URL
Таким образом добавляется новый роут.