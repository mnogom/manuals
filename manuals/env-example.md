## .env в Django

### Цели

1. Спрятать секретные/чуствительные данные приложения
2. Автоматизация прокидывания конфигураций для запуска приложений

### Шаги
1. Используем библиотеку [python-dotenv](https://pypi.org/project/python-dotenv/)

    1.1 Если виртуальное окружение создано через poetry:
    ```
    poetry add python-dotenv
    ```
   
    1.2 Если пакеты добавляются через `pip`
    ```
    pip install python-dotenv
    ```

2. В корне проекта создаём файл `.env`
```bash
SECRET_KEY=hard-password
DEBUG=1
ALLOWED_HOSTS=host1,host2,*
EMAIL_HOST_USER=user@email.com
EMAIL_HOST_PASSWORD=userpassword
```

3. Добавим файл в `.gitignore`, чтобы не запушить его в репозиторий. Если .gitignore нет - создадим его в корне проекта.
[Подробнее...](https://www.atlassian.com/ru/git/tutorials/saving-changes/gitignore)
```
/.env
```

4. В `settings.py`:
```python
from dotenv import load_dotenv
import os

# Load .env file
load_dotenv()

...

# SECURITY WARNING: keep the secret key used in production secret!
# Reading from env
SECRET_KEY = os.getenv('SECRET_KEY')

# SECURITY WARNING: don't run with debug turned on in production!
# [!] All variables will be read as strings
DEBUG = os.getenv('DEBUG', '0') == '1'
ALLOWED_HOSTS = os.getenv('ALLOWED_HOSTS', 'localhost').split(',')

...

# Email configs
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.someservice.com'
EMAIL_PORT = 465
EMAIL_USE_TLS = False
EMAIL_USE_SSL = True
EMAIL_HOST_USER = os.getenv('EMAIL_HOST_USER')
EMAIL_HOST_PASSWORD = os.getenv('EMAIL_HOST_PASSWORD')

...

```
5. Теперь можно пушить в репозиторий (в примере - через консоль)
```
git add .
git commit -m "Roses are red ..."
git push
```

6. Проверяем, что в наш репозиторий загрузились все файлы, кроме `.env`

### Post Scriptum
Для удобства переноса проектов я создаю дополнительно файл `.env.example` и __НЕ__ добавляю его в `.gitignore`
(Чтобы не искать по всему проекту переменные, которые необходимы для запуска). Содержит список переменных и намекает на их формат

`.env.example`:
```
SECRET_KEY=
DEBUG= # 1 or 0 
ALLOWED_HOSTS=,
EMAIL_HOST_USER=
EMAIL_HOST_PASSWORD=
```
