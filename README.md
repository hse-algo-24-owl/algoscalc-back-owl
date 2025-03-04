# algoscalc-back

## Описание проекта
Репозиторий с исходным кодом серверной части приложения Онлайн-калькулятор. Проект по разработке и поддержке онлайн-калькулятора предназначен для проведения практических занятий со студентами по разработке алгоритмов для web-приложения.

## Ресурсы
- [Тестовый контур](https://owl.ommat.ru/api/algorithms) серверной части онлайн-калькулятора.
- [Документация API (Swagger)](https://owl.ommat.ru/docs) серверной части онлайн-калькулятора.

## Запуск приложения в Docker
Определить настройки конфигурации приложения можно в файле .env.local

Исходный код в каталоге src монтируется в контейнер и приложение запускается в режиме reload, то есть перезапускается при внесении изменений в исходный код.

Запустить приложение можно командой:

```sh
docker compose -f docker-compose.local.yml up
```

Документация API запущенного приложения в формате Swagger доступна в браузере по адресу http://0.0.0.0:8080/docs

## Установка и запуск приложения локально
### Предварительные требования
- Python 3.12
- Python venv

### Шаги установки
1. Клонируйте репозиторий:

    ```sh
    git clone https://github.com/hse-algo-24-owl/algoscalc-back-owl.git
    cd algoscalc-back
    ```

2. Установите Python 3.12 и модуль `venv` (если они еще не установлены).

3. Создайте виртуальное окружение:

    ```sh
    python3 -m venv .venv
    ```

4. Активируйте виртуальное окружение:

    ```sh
    source .venv/bin/activate
    ```

5. Установите Poetry:

    ```sh
    pip install poetry
    ```

6. Настройте Poetry для создания виртуальных окружений в проекте:

    ```sh
    poetry config virtualenvs.in-project true
    ```

7. Установите зависимости проекта:

    ```sh
    poetry install
    ```

8.  Создайте каталог для хранения логов приложения:

    ```sh
    mkdir logs
    ```

9.  Запустите приложение:

    ```sh
    poetry run start
    ```

Документация API запущенного приложения в формате Swagger доступна в браузере по адресу http://0.0.0.0:8080/docs

## Разработка приложения

### Запуск приложения в режиме разработки

Запустить приложение локально в режиме разработки можно командой:

    ```sh
    poetry run dev
    ```

При этом сервер запускает приложение в режиме reload, то есть перезапускается после внесения изменений в исходный код.

### Отладка приложения в VS Code

Для отладки приложения необходимо добавить в файл .vscode/launch.json конфигурацию отладки:

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python Debugger: FastAPI",
            "type": "debugpy",
            "request": "launch",
            "module": "uvicorn",
            "args": [
                "src.dev:app",
                "--reload",
                "--host",
                "0.0.0.0",
                "--port",
                "8080"
            ]
        }
    ]
}
```

После чего в разделе RUN AND DEBUG необходимо выбрать и запусти конфигурацию отладки с названием "Python Debugger: FastAPI".

Swagger UI для запущенного приложения будет доступен в браузере по адресу http://0.0.0.0:8080/docs

### Добавление в приложение нового алгоритма

Для нового алгоритма необходимо создать каталог в src/algorithms и разместить в нем файлы:
- `__init__.py` - файл инициализации пакета алгоритма;
- `definition.json` - файл с описанием алгоритма в формате json. Структура данных описания должна соответствовать классу AlgorithmDefinitionSchema.
- `function.py` - файл с функцией, реализующей алгоритм. Файл должен содержать функцию с названием main, которая принимает параметры, описанные в файле definition.json и возвращает результаты в формате словаря с ключами, соответствующими названиям, описанным в файле definition.json.
- `tests.py` - файл с тестами, проверяющими работу функции main из файла function.py. Тесты автоматически запускаются при сборке алгоритма в состав приложения.

При реализации функции в файле `function.py` для выброса исключений необходимо использовать классы ошибок, наследующие от `AlgorithmError`, расположенные в модуле `src/internal/errors/exceptions.py`, например:
- AlgorithmValueError - ошибка некорректного значения параметра при выполнении алгоритма.
- AlgorithmTypeError - ошибка некорректного типа данных параметра при выполнении алгоритма.
- AlgorithmRuntimeError - ошибка выполнения алгоритма.

В случае использования указанных классов исключения возникающие ошибки будут корректно выводится для клиентов.

После добавления алгоритма необходимо:
1. Запустить форматирование импортов в файлах с исходным кодом с помощью библиотеки isort:

    ```sh
    poetry run isort .
    ```

2. Запустить форматирование исходного кода с помощью библиотеки black:

    ```sh
    poetry run black .
    ```

3. Запустить проверку исходного кода с помощью библиотеки black:

    ```sh
    poetry run flake8 .
    ```

4. Запустить автоматическое тестирование:

    ```sh
    poetry run test
    ```
