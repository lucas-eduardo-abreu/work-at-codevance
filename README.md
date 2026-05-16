# work-at-codevance — Payment Anticipation System

Web application and REST API for managing early payment requests between companies
and their suppliers. Built as part of the Codevance technical assessment.

## Stack

- Python 3.12 · Django 5 · Django REST Framework
- PostgreSQL · Celery + Redis (async email notifications)
- JWT authentication for suppliers · Docker + docker-compose

## Business rules

Suppliers request early settlement of upcoming invoices. Discount formula:

```
new_amount = original_amount - (original_amount × (3% / 30 × days_diff))
```

Payments that reach their original due date without being anticipated are
automatically locked and removed from the anticipation queue.

## Running locally

```bash
cp .env.example .env
docker-compose up -d          # starts postgres, redis, celery worker
python manage.py migrate
python manage.py createsuperuser
```

App at `http://localhost:8000`.

## Tests

```bash
pytest --cov=. --cov-report=term-missing
```

## API endpoints

| Method | Endpoint                                   | Auth |
|--------|--------------------------------------------|------|
| GET    | `/api/payments/`                           | JWT  |
| POST   | `/api/payments/{id}/request-anticipation/` | JWT  |

`GET /api/payments/` accepts a `status` query param:
`unavailable | available | pending | anticipated | rejected`

Full docs: [API.md](API.md).

## Deploy

Configured for Render via `render.yaml`. Required vars: `DATABASE_URL`,
`SECRET_KEY`, `REDIS_URL`, `EMAIL_HOST`, `EMAIL_HOST_USER`, `EMAIL_HOST_PASSWORD`.
