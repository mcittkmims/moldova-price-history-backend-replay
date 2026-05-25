# Moldova Price History Backend

This repository contains the Spring Boot backend for **pricehistory.md**. It powers product search, product detail and history endpoints, authentication, tracked-product management, and supporting metadata used by the frontend.

## Overview

The backend combines a locally persisted product catalog with data fetched from the scraper service. It exposes cache-friendly read APIs for the public storefront and authenticated APIs for per-user tracking features. The service also handles JWT-based session auth, CSRF protection, database migrations, and scheduled refreshes for tracked products.

## Features

- Product search and lookup endpoints
- Product detail and price history endpoints
- Product sitemap endpoints for SEO
- Store, category, and sort-option metadata endpoints
- User registration, login, logout, and session endpoints
- Cookie-based JWT auth with CSRF protection
- Tracked-product APIs for authenticated users
- Flyway database migrations
- Local H2 profile and PostgreSQL-ready runtime configuration

## Tech Stack

- Spring Boot 4
- Spring MVC
- Spring Security
- Spring Data JPA
- Flyway
- PostgreSQL
- H2 for local in-memory development
- Maven

## Project Structure

```text
.
├── src/main/java/md/pricehistory/backend/
│   ├── auth/
│   ├── common/
│   ├── config/
│   ├── product/
│   ├── scraper/
│   ├── search/
│   ├── tracking/
│   └── user/
├── src/main/resources/
│   ├── db/migration/
│   └── application*.properties
├── src/test/
├── docker-compose.yml
├── Dockerfile
├── pom.xml
└── README.md
```

## Requirements

- Java 17 or newer
- Maven 3.9 or newer

## Running Locally

Start the application with the default `local` profile:

```bash
mvn spring-boot:run
```

By default, the backend listens on `http://localhost:8080` and uses:

- H2 in-memory storage
- `http://localhost:8000` as the scraper base URL
- local CORS allowances for Next.js and Vite development

## Local Profiles

- `local`
  Uses H2 in memory, seeds local data, and disables Flyway.
- `prod`
  Uses PostgreSQL and Flyway for real runtime environments.
- `swagger`
  Enables Swagger-specific configuration used by the project.

To run in production profile locally:

```bash
SPRING_PROFILES_ACTIVE=prod mvn spring-boot:run
```

## Environment Variables

Common runtime configuration:

```bash
PORT=8080
CORS_ALLOWED_ORIGINS=https://pricehistory.md,https://www.pricehistory.md
SCRAPER_BASE_URL=http://localhost:8000
SCRAPER_API_KEY=
TRACKING_REFRESH_INTERVAL=PT12H
JWT_EXPIRATION=15m
JWT_COOKIE_NAME=pricehistory_access
JWT_COOKIE_SECURE=false
JWT_COOKIE_SAME_SITE=Lax
DATABASE_URL=jdbc:postgresql://localhost:5432/pricehistory
DATABASE_USERNAME=postgres
DATABASE_PASSWORD=postgres
DATABASE_DRIVER=org.postgresql.Driver
```

Local auth secret:

```bash
JWT_SECRET=local-demo-secret-local-demo-secret-1234
```

## Database

For a PostgreSQL-backed local setup, start the bundled database:

```bash
docker compose up -d
```

The included compose file provisions:

- database: `pricehistory`
- username: `postgres`
- password: `postgres`

Flyway migrations live under `src/main/resources/db/migration/`.

## API Surface

Public catalog and metadata endpoints include:

```http
GET /api/products/search?q=iphone&page=1&page_size=24
GET /api/products/lookup?url=https://example.com/product
GET /api/products/{slug}
GET /api/products/{slug}/history
GET /api/products/sitemap
GET /api/products/sitemap/page?page=1&page_size=5000
GET /api/stores
GET /api/categories
GET /api/sort-options
GET /actuator/health
```

Authentication endpoints include:

```http
POST /api/auth/register
POST /api/auth/login
POST /api/auth/logout
GET /api/auth/session
GET /api/csrf
```

Tracked-product endpoints include:

```http
GET /api/me/tracked?page=1&page_size=12
GET /api/me/tracked/{slug}
PUT /api/me/tracked/{slug}
DELETE /api/me/tracked/{slug}
```

## Testing

Run the test suite with:

```bash
mvn test
```

## Frontend Pairing

This backend is designed to serve the `moldova-price-history` frontend in [tum-web-lab6](/Users/avremere/Documents/web/tum-web-lab6/README.md).

## Repository

- GitHub: <https://github.com/mcittkmims/moldova-price-history-backend>
