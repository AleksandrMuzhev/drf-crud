# DRF CRUD Project - Склады и товары

## Описание проекта

Проект представляет собой REST API для управления складами и товарами, построенный на Django Rest Framework. Реализованы полные CRUD (Create-Read-Update-Delete) операции для продуктов и складов, включая сложные связи между ними.

## Функциональность

- Полный CRUD для продуктов
- Полный CRUD для складов  
- Связь товаров со складами через промежуточную таблицу
- Поиск товаров по названию и описанию
- Фильтрация складов по товарам
- Поиск складов по названию товара
- Пагинация результатов
- Вложенные сериализаторы для создания/обновления связанных объектов

## Технологический стек

- Python 3.8+
- Django 3.2+
- Django REST Framework
- PostgreSQL
- django-filter

## Быстрый старт

### 1. Клонирование и настройка окружения

```
# Клонируйте проект
git clone <url-репозитория>
cd drf-crud

# Создайте виртуальное окружение
python -m venv venv

# Активируйте виртуальное окружение
# Для Windows:
venv\Scripts\activate
# Для Linux/Mac:
source venv/bin/activate

# Установите зависимости
pip install -r requirements.txt
```

### 2. Настройка базы данных

#### Вариант 1: Использование переменных окружения

Создайте файл `.env` в корне проекта:

```env
SECRET_KEY=your-secret-key-here
DEBUG=True
DB_ENGINE=django.db.backends.postgresql
DB_USER=your-database-user
DB_PASSWORD=your-database-password
DB_NAME=netology_stocks_products
DB_HOST=127.0.0.1
DB_PORT=5432
```

#### Вариант 2: Ручное создание базы данных

```bash
# Подключитесь к PostgreSQL
psql -U postgres

# Создайте базу данных
CREATE DATABASE netology_stocks_products;

# Выйдите из консоли
\q
```

### 3. Применение миграций

```bash
cd stocks_products
python manage.py migrate
```

### 4. Запуск сервера

```bash
python manage.py runserver
```

Сервер будет доступен по адресу: http://localhost:8000

## API Endpoints

### Продукты

| Метод | URL | Описание |
|-------|-----|-----------|
| GET | `/api/v1/products/` | Получить список продуктов |
| POST | `/api/v1/products/` | Создать новый продукт |
| GET | `/api/v1/products/{id}/` | Получить продукт по ID |
| PUT | `/api/v1/products/{id}/` | Полностью обновить продукт |
| PATCH | `/api/v1/products/{id}/` | Частично обновить продукт |
| DELETE | `/api/v1/products/{id}/` | Удалить продукт |

### Склады

| Метод | URL | Описание |
|-------|-----|-----------|
| GET | `/api/v1/stocks/` | Получить список складов |
| POST | `/api/v1/stocks/` | Создать новый склад |
| GET | `/api/v1/stocks/{id}/` | Получить склад по ID |
| PUT | `/api/v1/stocks/{id}/` | Полностью обновить склад |
| PATCH | `/api/v1/stocks/{id}/` | Частично обновить склад |
| DELETE | `/api/v1/stocks/{id}/` | Удалить склад |

## Примеры использования API

### Создание продукта

```http
POST /api/v1/products/
Content-Type: application/json

{
  "title": "Помидор",
  "description": "Самые свежие помидоры"
}
```

### Поиск продуктов

```http
GET /api/v1/products/?search=помидор
```

### Создание склада с товарами

```http
POST /api/v1/stocks/
Content-Type: application/json

{
  "address": "ул. Ленина, 1",
  "positions": [
    {
      "product": 1,
      "quantity": 100,
      "price": 120.50
    },
    {
      "product": 2, 
      "quantity": 50,
      "price": 85.00
    }
  ]
}
```

### Поиск складов по товару

```http
GET /api/v1/stocks/?products=1
```

### Поиск складов по названию товара

```http
GET /api/v1/stocks/?search=помидор
```

## Структура проекта

```
drf-crud/
├── stocks_products/          # Основной Django проект
│   ├── logistic/            # Приложение для логистики
│   │   ├── models.py        # Модели Product, Stock, StockProduct
│   │   ├── serializers.py   # Сериализаторы
│   │   ├── views.py         # ViewSets
│   │   └── urls.py          # URL-маршруты
│   ├── settings.py          # Настройки Django
│   └── urls.py              # Корневые URL-маршруты
├── requirements.txt          # Зависимости Python
├── .env.example             # Пример файла окружения
└── .gitignore               # Игнорируемые файлы Git
```

## Модели данных

### Product
- `title` - название товара (уникальное)
- `description` - описание товара (опционально)

### Stock  
- `address` - адрес склада (уникальный)
- `products` - связь с товарами через StockProduct

### StockProduct (промежуточная модель)
- `stock` - ссылка на склад
- `product` - ссылка на товар  
- `quantity` - количество товара на складе
- `price` - цена товара на этом складе

## Особенности реализации

### Вложенные сериализаторы
Склады создаются и обновляются вместе с информацией о товарах на них через единый API-запрос.

### Фильтрация и поиск
- Поиск товаров по названию и описанию
- Фильтрация складов по конкретному товару
- Поиск складов по названию или описанию товара

### Пагинация
Все списковые endpoints поддерживают пагинацию (по 10 элементов на страницу).

## Разработка

### Запуск в режиме разработки

```bash
cd stocks_products
python manage.py runserver 0.0.0.0:8000
```

### Создание миграций

```bash
python manage.py makemigrations
python manage.py migrate
```

### Создание суперпользователя

```bash
python manage.py createsuperuser
```

## Тестирование

Для тестирования API можно использовать:
- Встроенный интерфейс DRF Browsable API
- Postman
- Файл `requests-examples.http` в проекте

## Производственная настройка

Для запуска в production:
1. Установите `DEBUG=False`
2. Настройте надежный `SECRET_KEY`
3. Используйте PostgreSQL в production
4. Настройте статические файлы через Nginx/Apache
5. Используйте WSGI сервер (Gunicorn/uWSGI)