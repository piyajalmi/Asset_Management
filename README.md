# Asset Management API

Simple Spring Boot API for managing coupons and vouchers with:

- JWT authentication
- concurrency-safe claiming
- optimistic locking for updates
- relational history queries
- H2 database for local development

## Stack

- Java 21
- Spring Boot 3
- Spring Security
- Spring Data JPA
- H2
- OpenAPI via Swagger UI

## Default users

- `admin / admin123`
- `alice / password123`

## Run

```bash
mvn spring-boot:run
```

Open:

- API: `http://localhost:8082`
- Swagger UI: `http://localhost:8082/swagger-ui/index.html`
- H2 console: `http://localhost:8082/h2-console`

## Main endpoints

### Auth

- `POST /api/auth/register`
- `POST /api/auth/login`

### Assets

- `GET /api/assets`
- `POST /api/assets` admin only
- `POST /api/assets/{assetId}/claim`
- `PATCH /api/assets/{assetId}`
- `GET /api/assets/history/me`

## Example login

```json
POST /api/auth/login
{
  "username": "alice",
  "password": "password123"
}
```

Use the returned JWT as:

```text
Authorization: Bearer <token>
```

## Concurrency strategy

`claim` is protected with a single conditional database update:

- only assets in `AVAILABLE` state can be claimed
- only one concurrent transaction can change that row successfully
- all other claim attempts receive `409 Conflict`

`update` uses optimistic locking:

- each asset has a `version`
- update requests must send the current version
- stale writes are rejected with `409 Conflict`

## Notes

- Local development uses a file-backed H2 database at `./data/assetdb`
- Tests use an in-memory H2 database
