# Wallet API

Асинхронный REST API для управления балансом кошелька. Позволяет пополнять и снимать средства с обработкой конкурентных запросов.

## Возможности

- Получение баланса кошелька
- Пополнение кошелька (DEPOSIT)
- Снятие средств (WITHDRAW)
- Защита от отрицательного баланса
- Атомарные операции на уровне БД
- Обработка конкурентных запросов (race condition)
- Асинхронный стек (FastAPI + SQLAlchemy)

## Технологии

- **Python** 3.13.1
- **FastAPI** — веб-фреймворк
- **SQLAlchemy** (async) — ORM
- **Alembic** — миграции
- **PostgreSQL** — база данных
- **Pytest** + **httpx** — тестирование
- **Docker** + **Docker Compose** — контейнеризация



### Клонирование репозитория

git clone 
cd fast-api-project
### Настройка окружения
Создайте файл .env в корне проекта:

env

DB_USER=<имя пользователя в бд>

DB_PASSWORD=<пароль бд>

DB_HOST=localhost

DB_PORT=5432

DB_NAME=wallet_db
### 3. Запуск через Docker 
docker-compose up -d

API будет доступно по адресу: http://localhost:8000

Документация Swagger: http://localhost:8000/docs

### 4. Локальный запуск (без Docker)
Установите зависимости:

pip install -r requirements.txt
Создайте базу данных wallet_db в postgreSQL, а также создайте файл 
.env в корне проекта:

env

DB_USER=<имя пользователя в бд>

DB_PASSWORD=<пароль бд>

DB_HOST=localhost

DB_PORT=5432

DB_NAME=wallet_db 

Выполните команду: 

alembic upgrade head

Запустите сервер:

uvicorn main:app --reload

API Эндпоинты

Получить баланс кошелька

GET /api/v1/wallets/{wallet_uuid}

Ответ:

json
{
  "wallet_uuid": "123e4567-e89b-12d3-a456-426614174000",
  "balance": 1500
}
Выполнить операцию (пополнение/снятие)

POST /api/v1/wallets/{wallet_uuid}/operation

Тело запроса:

json
{
  "operation_type": "DEPOSIT",  // или "WITHDRAW"
  "amount": 500
}

Успешный ответ (200):

json
{
  "wallet_uuid": "123e4567-e89b-12d3-a456-426614174000",
  "balance": 2000
}

Ошибки:

404 Not Found — кошелёк не найден

422 Unprocessable Content — Некорректные данные

409 Conflict — недостаточно средств

### 5.Тестирование

Запуск тестов

pytest tests/ -v

Запуск тестов в Docker

docker-compose run --rm app pytest tests/ -v

### Что тестируется

Получение баланса существующего/несуществующего кошелька

Пополнение и снятие средств

Ошибка при недостатке средств

Ошибка при вводе некорретных данных (пустая строка, отрицательные значения)

Атомарность конкурентных списаний — два параллельных запроса на снятие не позволяют уйти в минус

