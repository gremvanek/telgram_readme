## Проект: Мессенджер с аутентификацией, WebSocket, Telegram-ботом и веб-интерфейсом

### Описание проекта

Этот проект представляет собой мессенджер с аутентификацией пользователей, отправкой и получением сообщений в реальном времени через WebSocket, уведомлениями через Telegram-бота и простым веб-интерфейсом для тестирования. Проект разработан с использованием FastAPI, PostgreSQL, Redis, Celery и Docker.

### Технические требования

-   Python 3.10 или новее
-   FastAPI
-   PostgreSQL
-   Redis
-   Celery
-   Docker
-   Nginx

### Установка и запуск проекта

#### 1. Клонирование репозитория

```bash
git clone https://github.com/yourusername/FastAPI_Messenger.git
cd FastAPI_Messenger

```

#### 2. Создание виртуального окружения и установка зависимостей

```bash
python3.10 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

```

#### 3. Настройка переменных окружения

Создайте файл  `.env`  в корневой директории проекта и добавьте следующие переменные:

```env
DATABASE_URL=postgresql://postgres:050305@localhost/test_messenger
REDIS_URL=redis://localhost:6379/0
TELEGRAM_BOT_TOKEN=your_telegram_bot_token

```

#### 4. Запуск проекта с использованием Docker

Убедитесь, что у вас установлен Docker и Docker Compose. Перейдите в директорию messenger/docker. Затем выполните следующую команду в корневой директории проекта:

```bash
docker-compose up --build

```

Эта команда создаст и запустит контейнеры для вашего приложения, базы данных PostgreSQL и Redis.

### Проверка функциональности

#### 1. Регистрация и аутентификация пользователей

##### Регистрация нового пользователя

Отправьте POST-запрос на  `/register`  для регистрации нового пользователя:

```bash
curl -X POST "http://localhost:8000/register" -d "username=testuser&password=testpassword"

```

Вы должны получить ответ:

```json
{"message": "User registered successfully"}

```

##### Аутентификация пользователя

Отправьте POST-запрос на  `/token`  для получения токена аутентификации:

```bash
curl -X POST "http://localhost:8000/token" -d "username=testuser&password=testpassword"

```

Вы должны получить ответ с токеном:

```json
{"access_token": "your_access_token", "token_type": "bearer"}

```

#### 2. Отправка и получение сообщений

##### Отправка сообщения

Используйте токен, полученный на предыдущем шаге, для отправки сообщения:

```bash
curl -X POST "http://localhost:8000/send" -H "Authorization: Bearer your_access_token" -d "text=Hello&receiver_id=2"

```

Вы должны получить ответ:

```json
{"message": "Message sent successfully"}

```

##### Получение истории сообщений

Отправьте GET-запрос на  `/history`  для получения истории сообщений:

```bash
curl -X GET "http://localhost:8000/history?receiver_id=2" -H "Authorization: Bearer your_access_token"

```

Вы должны получить ответ с историей сообщений:

```json
{"messages": [{"id": 1, "text": "Hello", "sender_id": 1, "receiver_id": 2, "timestamp": "2023-10-01T12:00:00"}]}

```

#### 3. Проверка WebSocket для получения сообщений в реальном времени

Используйте любой WebSocket клиент (например,  [WebSocket.org Echo Test](https://www.websocket.org/echo.html)) для подключения к WebSocket:

```json
ws://localhost:8000/ws/2

```

Отправьте сообщение через WebSocket и убедитесь, что оно отображается в клиенте.

#### 4. Проверка Telegram-бота

##### Запуск Telegram-бота

Запустите Telegram-бота локально или в контейнере:

```bash
python app/utils/telegram_bot.py

```

##### Отправка уведомления через бота

Отправьте команду  `/notify`  боту в Telegram, чтобы проверить отправку уведомлений:

```bash
/notify

```

Вы должны получить сообщение:

```bash
You have a new message!

```

#### 5. Проверка веб-интерфейса

Откройте веб-страницы в браузере:

-   Регистрация:  `http://localhost:8000/register.html`
-   Вход:  `http://localhost:8000/login.html`
-   Главная страница:  `http://localhost:8000/index.html`

Проверьте, что вы можете зарегистрироваться, войти в систему и отправлять/получать сообщения через веб-интерфейс.

#### 6. Проверка фоновых задач с использованием Celery

##### Настройка Celery

Убедитесь, что у вас установлены все зависимости для Celery и Redis. Затем запустите Celery worker:

```bash
celery -A app.tasks.celery worker --loglevel=info

```

##### Отправка фоновой задачи

Отправьте фоновую задачу, например, для отправки уведомления через Telegram-бота:

```python
# app/tasks/celery.py
from celery import Celery
from app.utils.telegram_bot import bot

app = Celery('tasks', broker='redis://localhost:6379/0')

@app.task
def send_telegram_notification(user_id):
    bot.send_message(user_id, "You have a new message!")

```

Вызовите задачу из вашего кода:

```python
from app.tasks.celery import send_telegram_notification

send_telegram_notification.delay(user_id)

```

Убедитесь, что уведомление отправляется через Telegram-бота.

### Документация API

Используйте Swagger UI для просмотра и тестирования API:

```python
http://localhost:8000/docs

```

Это позволит вам увидеть все доступные эндпоинты и протестировать их через веб-интерфейс.

### Заключение

Теперь вы должны иметь полностью функционирующий проект с регистрацией и аутентификацией пользователей, отправкой и получением сообщений, работой Telegram-бота, веб-интерфейсом и фоновыми задачами. Убедитесь, что все компоненты работают как ожидается, и делайте коммиты после каждого шага для отслеживания изменений.
