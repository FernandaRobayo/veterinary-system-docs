# API Documentation

## OpenAPI Endpoints

When the backend is running locally, the generated OpenAPI documentation is available at:

- `http://localhost:9090/swagger-ui.html`
- `http://localhost:9090/v3/api-docs`
- `http://localhost:9090/v3/api-docs.yaml`

## Authentication

Most endpoints require HTTP Basic authentication.

- Public endpoint: `POST /api/auth/login`
- Protected endpoints: `GET`, `POST`, `PUT`, and `DELETE` operations under `/api/*`

The login endpoint returns a Basic Auth token that can be reused in Swagger UI or in the frontend.

## Local Verification

1. Start the backend.
2. Open `http://localhost:9090/swagger-ui.html`.
3. Confirm the OpenAPI contract is visible.
4. Open `http://localhost:9090/v3/api-docs` and confirm the specification is generated as JSON.
5. Use the `Authorize` action in Swagger UI with a valid Basic Auth credential when testing protected endpoints.
