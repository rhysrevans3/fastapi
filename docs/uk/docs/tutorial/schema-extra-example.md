# Декларування прикладів вхідних даних

Ви можете задати приклади даних, які Ваш застосунок може отримувати.

Ось кілька способів, як це зробити.

## Додаткові дані JSON-схеми в моделях Pydantic

Ви можете задати `examples` для моделі Pydantic, які буде додано до згенерованої JSON-схеми.

//// tab | Pydantic v2

{* ../../docs_src/schema_extra_example/tutorial001_py310.py hl[13:24] *}

////

//// tab | Pydantic v1

{* ../../docs_src/schema_extra_example/tutorial001_pv1_py310.py hl[13:23] *}

////

Ця додаткова інформація буде додана як є до **JSON-схеми**, і вона буде використовуватися в документації до API.

//// tab | Pydantic v2

У версії Pydantic 2 використовується атрибут `model_config`, який приймає `dict`, як описано в <a href="https://docs.pydantic.dev/latest/api/config/" class="external-link" target="_blank">документації Pydantic: Конфігурація</a>.

Ви можете встановити `"json_schema_extra"` як `dict`, що містить будь-які додаткові дані, які Ви хочете відобразити у згенерованій JSON-схемі, включаючи `examples`.

////

//// tab | Pydantic v1

У версії Pydantic 1 використовується внутрішній клас `Config` і параметр `schema_extra`, як описано в <a href="https://docs.pydantic.dev/1.10/usage/schema/#schema-customization" class="external-link" target="_blank">документації Pydantic: Налаштування схеми</a>.

Ви можете задати `schema_extra` як `dict`, що містить будь-які додаткові дані, які Ви хочете бачити у згенерованій JSON-схемі, включаючи `examples`.

////

/// tip | Підказка

Ви можете використати ту ж техніку, щоб розширити JSON-схему і додати власну додаткову інформацію.

Наприклад, Ви можете використати її для додавання метаданих для інтерфейсу користувача на фронтенді тощо.

///

/// info | Інформація

OpenAPI 3.1.0 (який використовується починаючи з FastAPI 0.99.0) додав підтримку `examples`, що є частиною стандарту **JSON-схеми**.

До цього підтримувався лише ключ `example` з одним прикладом. Він все ще підтримується в OpenAPI 3.1.0, але є застарілим і не входить до стандарту JSON Schema. Тому рекомендується перейти з `example`  на `examples`. 🤓

Більше про це можна прочитати в кінці цієї сторінки.

///

## Додаткові аргументи `Field`

Коли ви використовуєте `Field()` у моделях Pydantic, Ви також можете вказати додаткові `examples`:

{* ../../docs_src/schema_extra_example/tutorial002_py310.py hl[2,8:11] *}

## `examples` у JSON-схемі — OpenAPI

При використанні будь-кого з наступного:

* `Path()`
* `Query()`
* `Header()`
* `Cookie()`
* `Body()`
* `Form()`
* `File()`

Ви також можете задати набір `examples` з додатковою інформацією, яка буде додана до їхніх **JSON-схем** у **OpenAPI**.

### `Body` з `examples`

Тут ми передаємо `examples`, які містять один приклад очікуваних даних у `Body()`:

{* ../../docs_src/schema_extra_example/tutorial003_an_py310.py hl[22:29] *}

### Приклад у UI документації

За допомогою будь-якого з наведених вище методів це виглядатиме так у документації за `/docs`:

<img src="/img/tutorial/body-fields/image01.png">

### `Body` з кількома `examples`

Звичайно, Ви також можете передати кілька `examples`:

{* ../../docs_src/schema_extra_example/tutorial004_an_py310.py hl[23:38] *}

Коли Ви це робите, приклади будуть частиною внутрішньої **JSON-схеми** для цих даних.

Втім, на момент написання цього (<abbr title="2023-08-26">26 серпня 2023</abbr>), Swagger UI — інструмент, який відповідає за відображення UI документації — не підтримує показ кількох прикладів у **JSON-схеми**. Але нижче можна прочитати про обхідний шлях.

### Специфічні для OpenAPI `examples`

Ще до того, як **JSON-схема** почала підтримувати `examples`, OpenAPI вже мала підтримку поля з такою ж назвою — `examples`.

Це **специфічне для OpenAPI** поле `examples` розміщується в іншій частині специфікації OpenAPI — у **деталях кожної *операції шляху***, а не всередині самої JSON-схеми.

Swagger UI вже давно підтримує це поле `examples`. Тому Ви можете використовувати його, щоб **відображати** кілька **прикладів у документації**.

Це поле `examples` у специфікації OpenAPI — це `dict` (словник) з **кількома прикладами** (а не список `list`), кожен із яких може містити додаткову інформацію, що буде додана до **OpenAPI**.

Воно не включається до JSON Schema кожного параметра, а розміщується зовні, безпосередньо в *операції шляху*.

### Використання параметра `openapi_examples`

Ви можете оголосити специфічні для OpenAPI `examples` у FastAPI за допомогою параметра `openapi_examples` для:

* `Path()`
* `Query()`
* `Header()`
* `Cookie()`
* `Body()`
* `Form()`
* `File()`

Ключі словника (`dict`) ідентифікують кожен приклад, а кожне значення `dict` — кожен специфічний словник `dict` в `examples` може містити:

* `summary`: короткий опис прикладу.
* `description`: розгорнутий опис (може містити Markdown).
* `value`: сам приклад, наприклад, словник (`dict`).
* `externalValue`: альтернатива `value`, URL-адреса, що вказує на приклад. Проте ця опція може не підтримуватися більшістю інструментів, на відміну від `value`.

Використання виглядає так:

{* ../../docs_src/schema_extra_example/tutorial005_an_py310.py hl[23:49] *}

### Приклади OpenAPI у UI документації

З параметром `openapi_examples`, доданим до `Body()`, документація `/docs` виглядатиме так:

<img src="/img/tutorial/body-fields/image02.png">

## Технічні деталі

/// tip | Підказка

Якщо Ви вже використовуєте **FastAPI**  версії **0.99.0 або вище**, Ви можете **пропустити** цей розділ.

Він більш актуальний для старих версій, до появи OpenAPI 3.1.0.

Можна вважати це коротким **історичним екскурсом** у OpenAPI та JSON Schema. 🤓

///

/// warning | Попередження

Це дуже технічна інформація про стандарти **JSON Schema** і **OpenAPI**.

Якщо вищезгадані ідеї вже працюють у Вас — можете не заглиблюватися в ці деталі.

///

До OpenAPI 3.1.0 специфікація використовувала стару та модифіковану версію **JSON Schema**.

Оскільки JSON Schema раніше не підтримувала `examples`, OpenAPI додала власне поле `examples`.

OpenAPI також додала `example` і `examples`  до інших частин специфікації:

* <a href="https://github.com/OAI/OpenAPI-Specification/blob/main/versions/3.1.0.md#parameter-object" class="external-link" target="_blank">`Parameter Object` (в специфікації)</a> використовується FastAPI для:
    * `Path()`
    * `Query()`
    * `Header()`
    * `Cookie()`
* <a href="https://github.com/OAI/OpenAPI-Specification/blob/main/versions/3.1.0.md#media-type-object" class="external-link" target="_blank">`Request Body Object`, в полі `content`, в `Media Type Object` (в специфікації)</a> використовується FastAPI для:
    * `Body()`
    * `File()`
    * `Form()`

/// info | Інформація

Цей старий параметр `examples`, специфічний для OpenAPI, тепер називається `openapi_examples`, починаючи з FastAPI версії `0.103.0`.

///

### Поле `examples` у JSON Schema

Пізніше JSON Schema додала поле <a href="https://json-schema.org/draft/2019-09/json-schema-validation.html#rfc.section.9.5" class="external-link" target="_blank">`examples`</a> у нову версію специфікації.

І вже OpenAPI 3.1.0 базується на цій новій версії (JSON Schema 2020-12), яка включає поле `examples`.

Тепер це поле `examples` є пріоритетним і замінює старе (і кастомне) поле `example`, яке стало застарілим.

Нове поле `examples`  у JSON Schema — це **просто список (`list`)** прикладів, без додаткових метаданих (на відміну від OpenAPI).

/// info | Інформація

Навіть після того, як з'явився OpenAPI 3.1.0, який підтримував examples у JSON Schema, інструмент Swagger UI ще деякий час не підтримував цю версію (підтримка з’явилась з версії 5.0.0 🎉).

Через це версії FastAPI до 0.99.0 все ще використовували версії OpenAPI нижчі за 3.1.0.

///

### `Examples` в Pydantic і FastAPI

Коли Ви додаєте `examples` у модель Pydantic через `schema_extra` або `Field(examples=["something"])`, ці приклади додаються до **JSON Schema** цієї моделі.

І ця **JSON Schema** Pydantic-моделі включається до **OpenAPI** Вашого API, а потім використовується в UI документації (docs UI).

У версіях FastAPI до 0.99.0 (починаючи з 0.99.0 використовується новіший OpenAPI 3.1.0), коли Ви використовували `example` або `examples` з іншими утилітами (`Query()`, `Body()` тощо), ці приклади не додавалися до JSON Schema, який описує ці дані (навіть не до власної версії JSON Schema у OpenAPI). Натомість вони додавалися безпосередньо до опису *обробника шляху* *(path operation)* в OpenAPI (тобто поза межами частин, які використовують JSON Schema).

Але тепер, коли FastAPI 0.99.0 і вище використовують OpenAPI 3.1.0, а той — JSON Schema 2020-12, разом із Swagger UI 5.0.0 і вище — все стало більш узгодженим, і examples тепер включаються до JSON Schema.

### Swagger UI та специфічні для OpenAPI `examples`

Раніше (станом на 26 серпня 2023 року) Swagger UI не підтримував кілька прикладів у JSON Schema, тому користувачі не мали можливості показати декілька прикладів у документації.

Щоб вирішити це, FastAPI починаючи з версії 0.103.0 **додав підтримку** старого **OpenAPI-специфічного** поля `examples` через новий параметр `openapi_examples`. 🤓

### Підсумок

Раніше я казав, що не люблю історію... а тепер ось я — розповідаю "технічні історичні" лекції. 😅

Коротко: **оновіться до FastAPI 0.99.0 або вище** — і все стане значно **простішим, узгодженим та інтуїтивно зрозумілим**, і Вам не доведеться знати всі ці історичні деталі. 😎
