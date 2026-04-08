---
name: OpenAPI Documentation
description: Document REST APIs using the OpenAPI specification -- schema-first, auto-generated, always current
category: documentation
paths:
  - "**/openapi*.yaml"
  - "**/openapi*.yml"
  - "**/openapi*.json"
  - "**/swagger*.yaml"
  - "**/swagger*.yml"
  - "**/swagger*.json"
---

# OpenAPI Documentation

All REST APIs must be documented using the OpenAPI Specification (OAS, formerly Swagger). The OpenAPI document is the contract between API producers and consumers.

## Approach

Prefer **code-first with generation** over hand-writing OpenAPI YAML/JSON:

- Define request/response schemas in code using typed models (Pydantic, Zod, TypeScript interfaces).
- Use framework integrations to auto-generate the OpenAPI spec from code annotations (FastAPI, NestJS, tsoa, Express with swagger-jsdoc).
- The generated spec is the source of truth. Hand-edited specs drift from the implementation.
- Commit the generated spec to version control so changes are visible in PRs.

## Required Documentation Per Endpoint

Every endpoint must include:

- **Summary:** One-line description (appears in API explorer navigation).
- **Description:** Detailed explanation of behavior, side effects, and business rules.
- **Parameters:** All path, query, and header parameters with types, constraints, and whether required.
- **Request body:** Schema with field descriptions, constraints (min/max, patterns, enums), and required fields.
- **Response schemas:** For every possible status code (200, 201, 400, 401, 403, 404, 409, 422, 500).
- **Error responses:** Use a consistent error schema across all endpoints. Document error codes and their meaning.
- **Authentication:** Which auth scheme applies (Bearer, API key, OAuth2 scope).
- **Examples:** At least one realistic request/response pair per endpoint. Use plausible data, not "foo" and "bar".

## Schema Standards

- Use `$ref` to define reusable schemas in `components/schemas`. Do not inline complex schemas in endpoint definitions.
- Add `description` to every schema property. The property name alone is not documentation.
- Use `enum` for fields with a fixed set of allowed values. Document what each value means.
- Use `format` for well-known string formats: `date-time`, `email`, `uri`, `uuid`.
- Use `nullable: true` (OAS 3.0) or type arrays (OAS 3.1) for fields that can be null.
- Set `required` arrays on object schemas. Do not rely on consumers guessing which fields are optional.
- Use `example` or `examples` on schemas and properties to provide realistic sample values.

## Versioning

- Version the API in the URL path (`/api/v1/`) or via a header.
- The OpenAPI spec version must match the deployed API version.
- When introducing breaking changes, create a new API version and maintain the old spec until deprecation.
- Mark deprecated endpoints with `deprecated: true` and document the migration path.

## Validation and Testing

- Validate the generated OpenAPI spec in CI using a linter (spectral, openapi-generator validate, swagger-cli validate).
- Use contract testing to verify the implementation matches the spec (Schemathesis, Dredd, Prism).
- Block merges if the spec is invalid or if the implementation diverges from the documented contract.

## Hosting and Access

- Serve the API documentation via an interactive explorer (Swagger UI, Redoc, Stoplight Elements) at a well-known path (e.g., `/docs`, `/api-docs`).
- Make the raw OpenAPI spec downloadable at a predictable URL (e.g., `/openapi.json`).
- Client SDKs should be generated from the OpenAPI spec, not hand-written.

## Anti-patterns

- Hand-writing OpenAPI YAML that is not validated against the running API -- specs drift silently.
- Documenting only success responses. Error responses are the most important documentation for API consumers.
- Using generic descriptions like "Gets the resource" or "Creates a new item" -- describe what the endpoint actually does and any non-obvious behavior.
- Omitting authentication requirements from individual endpoints.
- Leaving `example` fields empty or using placeholder data like `"string"` or `0`.
